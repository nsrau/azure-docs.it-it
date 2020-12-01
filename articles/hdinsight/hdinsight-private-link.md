---
title: Proteggere e isolare i cluster HDInsight di Azure con collegamento privato (anteprima)
description: Informazioni su come isolare i cluster HDInsight di Azure in una rete virtuale usando il collegamento privato di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: fac26c616c977eedc466f004a9455297ec995fb8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352542"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Proteggere e isolare i cluster HDInsight di Azure con collegamento privato (anteprima)

Nell' [architettura di rete virtuale predefinita](./hdinsight-virtual-network-architecture.md)di Azure HDInsight, il provider di risorse HDInsight comunica con il cluster usando indirizzi IP pubblici. Alcuni scenari richiedono un isolamento di rete completo senza l'uso di indirizzi IP pubblici. Questo articolo illustra i controlli avanzati che è possibile usare per creare un cluster HDInsight privato. Per informazioni su come limitare il traffico da e verso il cluster senza isolamento di rete completo, vedere [controllare il traffico di rete in Azure HDInsight](./control-network-traffic.md).

È possibile creare cluster HDInsight privati configurando proprietà di rete specifiche in un modello di Azure Resource Manager (ARM). Esistono due proprietà che è possibile usare per creare cluster HDInsight privati:

* Rimuovere gli indirizzi IP pubblici impostando `resourceProviderConnection` su in uscita.
* Abilitare collegamento privato di Azure e usare [endpoint privati](../private-link/private-endpoint-overview.md) impostando `privateLink` su abilitato.

## <a name="remove-public-ip-addresses"></a>Rimuovi indirizzi IP pubblici

Per impostazione predefinita, HDInsight RP usa una connessione in *ingresso* al cluster usando indirizzi IP pubblici. Quando la `resourceProviderConnection` proprietà di rete è impostata su in *uscita*, inverte le connessioni a HDInsight RP, in modo che le connessioni vengano sempre avviate dall'interno del cluster alla relying party. Senza una connessione in ingresso, non è necessario disporre dei tag del servizio in ingresso o degli indirizzi IP pubblici.

I bilanciamenti del carico di base usati nell'architettura di rete virtuale predefinita forniscono automaticamente il NAT pubblico (Network Address Translation) per accedere alle dipendenze in uscita necessarie, ad esempio HDInsight RP. Se si vuole limitare la connettività in uscita alla rete Internet pubblica, è possibile [configurare un firewall](./hdinsight-restrict-outbound-traffic.md), ma non è un requisito.

`resourceProviderConnection`La configurazione in uscita consente anche di accedere a risorse specifiche del cluster, ad esempio Azure Data Lake storage Gen2 o Metastore esterni, usando endpoint privati. L'uso di endpoint privati per queste risorse non è mandetory, ma se si prevede di avere endpoint privati per queste risorse, è necessario configurare gli endpoint privati e le voci DNS `before` che si crea il cluster HDInsight. Si consiglia di creare e fornire tutti i database SQL esterni necessari, ad esempio Apache Ranger, Ambari, oozie e Metastore hive, al momento della creazione del cluster. Il requisito è che tutte queste risorse devono essere accessibili dall'interno della subnet del cluster, tramite il proprio endpoint privato o in caso contrario.

L'uso di endpoint privati per Azure Key Vault non è supportato. Se si usa Azure Key Vault per la crittografia CMK, è necessario che l'endpoint Azure Key Vault sia accessibile dall'interno della subnet HDInsight senza endpoint privato.

Il diagramma seguente mostra il possibile aspetto di una potenziale architettura di rete virtuale HDInsight quando `resourceProviderConnection` è impostato su in uscita:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagramma dell'architettura HDInsight con una connessione del provider di risorse in uscita":::

Dopo aver creato il cluster, è necessario configurare la risoluzione DNS corretta. Il nome canonico seguente (CNAME) viene creato nella zona DNS pubblica gestita di Azure: `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

Per accedere al cluster usando FQDN del cluster, è possibile usare direttamente gli indirizzi IP privati del servizio di bilanciamento del carico interno oppure usare la propria zona di DNS privato per sostituire gli endpoint del cluster in base alle esigenze. Ad esempio, è possibile avere una zona di DNS privato, `azurehdinsight.net` . e aggiungere gli indirizzi IP privati in base alle esigenze:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Abilita collegamento privato

Il collegamento privato, disabilitato per impostazione predefinita, richiede una conoscenza di rete completa per configurare correttamente le route definite dall'utente (UDR) e le regole del firewall prima di creare un cluster. L'uso di questa impostazione è facoltativo ma è disponibile solo quando la `resourceProviderConnection` proprietà di rete è impostata su in *uscita* , come descritto nella sezione precedente.

Quando `privateLink` è impostato su *Abilita*, vengono creati i servizi di [bilanciamento del carico standard](../load-balancer/load-balancer-overview.md) interni (SLB) e viene eseguito il provisioning di un servizio di collegamento privato di Azure per ogni SLB. Il servizio di collegamento privato consente di accedere al cluster HDInsight da endpoint privati.

I bilanciamenti del carico standard non forniscono automaticamente il [NAT in uscita pubblico](../load-balancer/load-balancer-outbound-connections.md) come i bilanciamenti del carico di base. Per le dipendenze in uscita, è necessario fornire una soluzione NAT personalizzata, ad esempio [NAT della rete virtuale](../virtual-network/nat-overview.md) o un [Firewall](./hdinsight-restrict-outbound-traffic.md). Il cluster HDInsight deve ancora accedere alle dipendenze in uscita. Se queste dipendenze in uscita non sono consentite, la creazione del cluster potrebbe non riuscire.

### <a name="prepare-your-environment"></a>Preparare l'ambiente

Per la creazione di successgfull di servizi di collegamento privato, è necessario disabilitare in modo esplicito [i criteri di rete per il servizio di collegamento privato](../private-link/disable-private-link-service-network-policy.md).

Il diagramma seguente illustra un esempio della configurazione di rete necessaria prima di creare un cluster. In questo esempio, tutto il traffico in uscita è [forzato](../firewall/forced-tunneling.md) al firewall di Azure con UdR e le dipendenze in uscita obbligatorie devono essere "consentite" nel firewall prima di creare un cluster. Per i cluster Enterprise Security Package, la connettività di rete alle Azure Active Directory Domain Services può essere fornita dal peering VNet.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagramma dell'ambiente di collegamento privato prima della creazione del cluster":::

Dopo aver configurato la rete, è possibile creare un cluster con connessione del provider di risorse in uscita e collegamento privato abilitato, come illustrato nella figura seguente. In questa configurazione non sono disponibili indirizzi IP pubblici e viene effettuato il provisioning del servizio di collegamento privato per ogni servizio di bilanciamento del carico standard.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagramma dell'ambiente di collegamento privato dopo la creazione del cluster":::

### <a name="access-a-private-cluster"></a>Accedere a un cluster privato

Per accedere ai cluster privati, è possibile usare direttamente gli indirizzi IP privati del servizio di bilanciamento del carico interno oppure è possibile usare estensioni DNS di collegamento privato ed endpoint privati. Quando l' `privateLink` impostazione è impostata su abilitata, è possibile creare endpoint privati e configurare la risoluzione DNS tramite zone DNS private.

Le voci di collegamento privato create nella zona DNS pubblica gestita da Azure `azurehdinsight.net` sono le seguenti:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

La figura seguente mostra un esempio delle voci DNS private necessarie per accedere al cluster da una rete virtuale senza peering o che non ha una linea di visibilità diretta per i bilanciamenti del carico del cluster. È possibile usare la zona privata di Azure per eseguire l'override dei nomi `*.privatelink.azurehdinsight.net` FQDN e risolverli negli indirizzi IP degli endpoint privati.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagramma dell'architettura di collegamento privato":::

## <a name="how-to-create-clusters"></a>Come creare i cluster
### <a name="use-arm-template-properties"></a>Usare le proprietà del modello ARM

Il frammento di codice JSON seguente include le due proprietà di rete che è necessario configurare nel modello ARM per creare un cluster HDInsight privato.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Per un modello completo con molte delle funzionalità di sicurezza di HDInsight Enterprise, incluso il collegamento privato, vedere [modello di sicurezza HDInsight Enterprise](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Usare Azure PowerShell

Per usare PowerShell, vedere l'esempio [qui](/powershell/module/az.hdinsight/new-azhdinsightcluster?view=azps-5.1.0#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per usare l'interfaccia della riga di comando di Azure, vedere l'esempio [qui](/cli/azure/hdinsight?view=azure-cli-latest#az_hdinsight_create-examples).

## <a name="next-steps"></a>Passaggi successivi

* [Enterprise Security Package per Azure HDInsight](enterprise-security-package.md)
* [Informazioni generali e linee guida per la sicurezza aziendale in Azure HDInsight](./domain-joined/general-guidelines.md)