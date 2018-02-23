---
title: Creare un cluster Linux di Service Fabric in Azure | Microsoft Docs
description: Informazioni su come distribuire un cluster Linux di Service Fabric in una rete virtuale di Azure esistente tramite l'interfaccia della riga di comando di Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b09e676a26336d1ef1e744f9e45066c4815fe21
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="deploy-a-service-fabric-linux-cluster-into-an-azure-virtual-network"></a>Distribuire un cluster Linux di Service Fabric in una rete virtuale di Azure
Questa è la prima di una serie di esercitazioni. Si apprenderà come distribuire un cluster Linux di Service Fabric in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) e in un [gruppo di sicurezza di rete (NSG)](../virtual-network/virtual-networks-nsg.md) usando l'interfaccia della riga di comando di Azure e un modello. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni. Per creare un cluster Windows tramite PowerShell, vedere [Creare un cluster sicuro di Windows in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando l'interfaccia della riga di comando di Azure
> * Creare un cluster sicuro di Service Fabric in Azure usando l'interfaccia della riga di comando di Azure
> * Proteggere il cluster con un certificato X.509
> * Connettersi al cluster con l'interfaccia della riga di comando di Service Fabric
> * Rimuovere un cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un cluster sicuro in Azure
> * [Aumentare o ridurre un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Distribuire Gestione API e Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installare l'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)
- Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli)

Le procedure seguenti creano un cluster di Service Fabric a cinque nodi. Per calcolare i costi sostenuti per l'esecuzione di un cluster di Service Fabric in Azure, usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="key-concepts"></a>Concetti chiave
Un [cluster di Service Fabric](service-fabric-deploy-anywhere.md) è un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi. I cluster possono supportare migliaia di macchine. Un computer o una macchina virtuale che fa parte di un cluster viene chiamato nodo. A ogni nodo viene assegnato un nome (stringa). I nodi presentano delle caratteristiche, ad esempio le proprietà di posizionamento.

Un tipo di nodo definisce le dimensioni, il numero e le proprietà di un set di macchine virtuali nel cluster. Ogni tipo di nodo definito viene configurato come [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/), una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. I tipi di nodo vengono usati per definire i ruoli relativi a un set di nodi del cluster, ad esempio "front-end" o "back-end".  Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario deve essere costituito da almeno cinque macchine virtuali per i cluster di produzione (o da almeno tre macchine virtuali per i cluster di test).  I [servizi di sistema Service Fabric](service-fabric-technical-overview.md#system-services) vengono inseriti nei nodi del tipo di nodo primario.

Il cluster è protetto con un certificato cluster. Un certificato del cluster è un certificato X.509 usato per proteggere le comunicazioni da nodo a nodo e autenticare gli endpoint di gestione dei cluster in un client di gestione.  Il certificato del cluster fornisce anche un certificato SSL per l'API di gestione HTTPS e per Service Fabric Explorer tramite HTTPS. I certificati autofirmati sono utili per i cluster di test.  Come certificato per i cluster di produzione usare un certificato di un'entità di certificazione (CA).

Il certificato del cluster deve:

- contenere una chiave privata;
- essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange);
- avere un nome del soggetto corrispondente al dominio usato per accedere al cluster di Service Fabric. Questa corrispondenza è necessaria per fornire SSL per gli endpoint di gestione HTTPS del cluster e Service Fabric Explorer. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio .cloudapp.azure.com. È necessario ottenere un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per il cluster.

L'insieme di credenziali delle chiavi di Azure viene usato per gestire i certificati dei cluster di Service Fabric in Azure.  Quando viene distribuito un cluster in Azure, il provider di risorse di Azure responsabile della creazione di cluster Service Fabric estrae i certificati dall'insieme di credenziali delle chiavi e li installa nelle macchine virtuali del cluster.

Questa esercitazione distribuisce un cluster con cinque nodi in un unico tipo di nodo. La [pianificazione della capacità](service-fabric-cluster-capacity.md), tuttavia, è un passaggio importante per qualsiasi distribuzione di cluster di produzione. Di seguito sono elencati alcuni aspetti da considerare nell'ambito di questo processo.

- Numero e tipi di nodi necessari per il cluster 
- Proprietà di ogni tipo di nodo (ad esempio, dimensione, tipo primario, tipo per Internet e numero di macchine virtuali)
- Caratteristiche di affidabilità e durabilità del cluster

## <a name="download-and-explore-the-template"></a>Scaricare ed esplorare il modello
Scaricare i file del modello di Resource Manager seguenti:
- [vnet-linuxcluster.json][template]
- [vnet-linuxcluster.parameters.json][parameters]

[vnet-linuxcluster.json][template] distribuisce alcune risorse, incluse le seguenti.

### <a name="service-fabric-cluster"></a>Cluster di Service Fabric
Viene distribuito un cluster Linux con le caratteristiche seguenti.
- Unico tipo di nodo 
- Cinque nodi del tipo di nodo primario (configurabile nei parametri del modello)
- Sistema operativo Ubuntu 16.04 LTS (configurabile nei parametri del modello)
- Protezione con certificato (configurabile nei parametri del modello)
- [Servizio DNS](service-fabric-dnsservice.md) abilitato
- [Livello di durabilità](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Bronzo (configurabile nei parametri del modello)
- [Livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Argento (configurabile nei parametri del modello)
- Endpoint di connessione client: 19000 (configurabile nei parametri del modello)
- Endpoint del gateway HTTP: 19080 (configurabile nei parametri del modello)

### <a name="azure-load-balancer"></a>Servizio di bilanciamento del carico di Azure
Viene distribuito un servizio di bilanciamento del carico e vengono configurati probe e regole per le porte seguenti.
- Endpoint di connessione client: 19000
- Endpoint del gateway HTTP: 19080 
- Porta applicazione: 80
- Porta applicazione: 443

### <a name="virtual-network-subnet-and-network-security-group"></a>Rete virtuale, subnet e gruppo di sicurezza di rete
I nomi della rete virtuale, della subnet e del gruppo di sicurezza di rete vengono dichiarati nei parametri del modello,  così come gli spazi indirizzi della rete virtuale e della subnet.
- Spazio indirizzi della rete virtuale: 10.0.0.0/16
- Spazio indirizzi della subnet di Service Fabric: 10.0.2.0/24

Nel gruppo di sicurezza di rete vengono abilitate le regole per il traffico in ingresso riportate di seguito. È possibile modificare i valori di porta modificando le variabili del modello.
- Endpoint di connessione client (TCP): 19000
- Endpoint del gateway HTTP (HTTP/TCP): 19080
- SMB: 445
- Comunicazione tra nodi: 1025, 1026, 1027
- Intervallo di porte temporaneo: da 49152 a 65534 (sono necessarie almeno 256 porte)
- Porte utilizzabili per l'applicazione: 80 e 443
- Intervallo di porte dell'applicazione: da 49152 a 65534 (quelle usate per la comunicazione tra servizi e di altro tipo non vengono aperte nel servizio di bilanciamento del carico)
- Bloccare tutte le altre porte

Se sono necessarie altre porte dell'applicazione, si dovranno modificare le risorse Microsoft.Network/loadBalancers e Microsoft.Network/networkSecurityGroups in modo da consentire il traffico in ingresso.

## <a name="set-template-parameters"></a>Impostare i parametri del modello
Nel file dei parametri [vnet-cluster.parameters.json][parameters] vengono dichiarati molti valori usati per distribuire il cluster e le risorse associate. Di seguito sono riportati alcuni dei parametri che potrebbe essere necessario modificare per la propria distribuzione:

|Parametro|Valore di esempio|Note|
|---|---||
|adminUserName|vmadmin| Nome utente amministratore per le VM del cluster. |
|adminPassword|Password#1234| Password amministratore per le VM del cluster.|
|clusterName|mysfcluster123| Nome del cluster. |
|location|southcentralus| Località del cluster. |
|certificateThumbprint|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore di identificazione personale del certificato, ad esempio "6190390162C988701DB5676EB81083EA608DCCF3". </p>| 
|certificateUrlValue|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere l'URL del certificato, ad esempio "https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore dell'insieme di credenziali di origine, ad esempio "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuire la rete virtuale e il cluster
Configurare quindi la topologia di rete e distribuire il cluster di Service Fabric. Il modello di Resource Manager [vnet-linuxcluster.json][template] crea una rete virtuale, nonché una subnet e un gruppo di sicurezza di rete (NSG) per Service Fabric. Il modello distribuisce anche un cluster in cui è abilitata la sicurezza basata su certificati.  Come certificato per i cluster di produzione usare un certificato di un'entità di certificazione (CA). Per proteggere i cluster di test è possibile usare un certificato autofirmato.

Lo script seguente usa il comando [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) e il modello per distribuire un nuovo cluster protetto con un certificato esistente. Il comando crea anche un nuovo insieme di credenziali delle chiavi in Azure e carica il certificato.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"  
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file vnet-linuxcluster.json --parameter-file vnet-linuxcluster.parameters.json
```

## <a name="connect-to-the-secure-cluster"></a>Connettersi al cluster sicuro
Connettersi al cluster tramite l'interfaccia della riga di comando di Service Fabric `sfctl cluster select` usando la chiave.  Usare solo l'opzione **--no-verify** per un certificato autofirmato.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Verificare di essere connessi e che il cluster sia integro usando il comando `sfctl cluster health`.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>Pulire le risorse
Gli altri articoli in questa serie di esercitazioni usano il cluster appena creato. Se non si intende passare subito all'articolo successivo, è opportuno eliminare il cluster per evitare di sostenere costi. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster.  È possibile trovare l'ID della sottoscrizione accedendo al [portale di Azure](http://portal.azure.com). Eliminare il gruppo di risorse e tutte le risorse del cluster con il comando [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete).

```azurecli
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare una rete virtuale in Azure usando l'interfaccia della riga di comando di Azure
> * Creare un cluster sicuro di Service Fabric in Azure usando l'interfaccia della riga di comando di Azure
> * Proteggere il cluster con un certificato X.509
> * Connettersi al cluster con l'interfaccia della riga di comando di Service Fabric
> * Rimuovere un cluster

Procedere con l'esercitazione seguente per scoprire come ridimensionare il cluster.
> [!div class="nextstepaction"]
> [Ridimensionare un cluster](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-linuxcluster.parameters.json
