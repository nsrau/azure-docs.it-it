---
title: Plan a virtual network for Azure HDInsight
description: Learn how to plan an Azure Virtual Network deployment to connect HDInsight to other cloud resources, or resources in your datacenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: d337d026e89d2383e25498288ba11a9c60f77b39
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228999"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Plan a virtual network for Azure HDInsight

This article provides background information on using [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) with Azure HDInsight. It also discusses design and implementation decisions that must be made before you can implement a virtual network for your HDInsight cluster. Once the planning phase is finished, you can proceed to [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md). For more information on HDInsight management IP addresses that are needed to properly configure network security groups and user-defined routes, see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).

Il servizio Rete virtuale di Azure consente di implementare gli scenari seguenti:

* Connessione a HDInsight direttamente da una rete locale.
* Connessione di HDInsight ad archivi dati in una rete virtuale di Azure.
* Accesso diretto ai servizi [Apache Hadoop](https://hadoop.apache.org/) che non sono disponibili pubblicamente su Internet, ad esempio le API [Apache Kafka](https://kafka.apache.org/) o l'API Java [Apache HBase](https://hbase.apache.org/).

> [!IMPORTANT]
> Creating an HDInsight cluster in a VNET will create several networking resources, such as NICs and load balancers. Do **not** delete these networking resources, as they are needed for your cluster to function correctly with the VNET.
>
> After Feb 28, 2019, the networking resources (such as NICs, LBs, etc) for NEW HDInsight clusters created in a VNET will be provisioned in the same HDInsight cluster resource group. Previously, these resources were provisioned in the VNET resource group. There is no change to the current running clusters and those clusters created without a VNET.

## <a name="planning"></a>Pianificazione

Di seguito sono elencate le domande che è necessario porsi quando si intende installare HDInsight in una rete virtuale:

* Occorre installare HDInsight in una rete virtuale esistente? Oppure si intende creare una rete nuova?

    Se si usa una rete virtuale esistente, potrebbe essere necessario modificare la configurazione di rete prima di poter installare HDInsight. Per altre informazioni, vedere la sezione [Aggiungere HDInsight a una rete virtuale esistente](#existingvnet).

* La rete virtuale che contiene HDInsight deve essere connessa a un'altra rete virtuale o alla rete locale?

    Per usare facilmente le risorse tra più reti, potrebbe essere necessario creare un DNS personalizzato e configurare l'inoltro DNS. Per altre informazioni, vedere la sezione [Connessione a più reti](#multinet).

* Si vuole limitare/reindirizzare il traffico in ingresso o in uscita a HDInsight?

    HDInsight deve disporre di una comunicazione senza restrizioni con indirizzi IP specifici nel data center di Azure. Sono presenti anche diverse porte che devono essere abilitate attraverso i firewall per la comunicazione client. Per altre informazioni, vedere la sezione [Controllo del traffico di rete](#networktraffic).

## <a id="existingvnet"></a>Aggiungere HDInsight a una rete virtuale esistente

Seguire la procedura in questa sezione per aggiungere un nuovo cluster HDInsight a una rete virtuale di Azure esistente.

> [!NOTE]  
> È possibile aggiungere un cluster HDInsight esistente in una rete virtuale.

1. Si intende usare un modello di distribuzione classico o di Resource Manager per la rete virtuale?

    HDInsight 3.4 e versione successiva richiedono l'utilizzo di una rete virtuale di Resource Manager. Le versioni precedenti di HDInsight richiedono una rete virtuale classica.

    Se la rete virtuale esistente è un modello classico, è necessario creare una rete virtuale di Resource Manager e successivamente connettere le due reti. [Connessione di reti virtuali classiche a reti virtuali nuove](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Dopo che le due reti sono state unite, HDInsight installato nella rete di Resource Manager può interagire con le risorse della rete classica.

2. Si usano gruppi di sicurezza di rete, route definite dall'utente o appliance di rete virtuali per limitare il traffico all'interno o all'esterno della rete virtuale?

    In qualità di servizio gestito, HDInsight richiede l'accesso senza restrizioni a vari indirizzi IP nel data center di Azure. Per consentire la comunicazione con questi indirizzi IP, aggiornare tutti i gruppi di sicurezza di rete o le route definite dall'utente esistenti.
    
    HDInsight ospita più servizi, che usano porte diverse. Non bloccare il traffico indirizzato a queste porte. Per un elenco di porte da abilitare attraverso i firewall dell'appliance virtuale, vedere la sezione Sicurezza.
    
    Per trovare la configurazione di sicurezza esistente, usare i comandi di Azure PowerShell o dell'interfaccia della riga di comando di Azure che seguono:

    * Gruppi di sicurezza di rete

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Per altre informazioni, vedere il documento [Risolvere i problemi relativi ai gruppi di sicurezza di rete](../virtual-network/diagnose-network-traffic-filter-problem.md).

        > [!IMPORTANT]  
        > Le regole di gruppo di sicurezza di rete vengono applicate seguendo un ordine basato sulla priorità delle regole. Viene applicata la prima regola che corrisponde al modello di traffico, dopodiché non vengono applicate altre regole per quel traffico. Ordinare le regole dalla più permissiva alla più restrittiva. Per altre informazioni, vedere il documento [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/security-overview.md).

    * Route definite dall'utente

        Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Per altre informazioni, vedere il documento [Risolvere i problemi relativi alle route](../virtual-network/diagnose-network-routing-problem.md).

3. Creare un cluster HDInsight e selezionare la rete virtuale di Azure durante la configurazione. Per comprendere il processo di creazione del cluster, attenersi alla procedura nei documenti seguenti:

    * [Creare cluster HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Creare cluster HDInsight tramite Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Creare HDInsight usando l'interfaccia della riga di comando classica di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Creare cluster HDInsight tramite un modello Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > L'aggiunta di un cluster HDInsight a una rete virtuale è un passaggio di configurazione facoltativo. Assicurarsi di selezionare la rete virtuale quando si configura il cluster.

## <a id="multinet"></a>Connessione a più reti

Il problema più grande con una configurazione con più reti è la risoluzione dei nomi tra le reti.

Azure assicura la risoluzione dei nomi per i servizi di Azure che vengono installati in una rete virtuale. La risoluzione dei nomi integrata consente a HDInsight di connettersi alle risorse seguenti usando un nome di dominio completo (FQDN):

* Qualsiasi risorsa disponibile in Internet, ad esempio microsoft.com o windowsupdate.com.

* Qualsiasi risorsa che si trovi nella stessa rete virtuale di Azure tramite l'utilizzo del __nome DNS interno__ della risorsa. For example, when using the default name resolution, the following are examples of internal DNS names assigned to HDInsight worker nodes:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Entrambi questi nodi possono comunicare direttamente tra loro e con altri nodi in HDInsight, usando i nomi DNS interni.

La risoluzione dei nomi predefinita __non__ consente a HDInsight di risolvere i nomi di risorse in reti che sono aggiunte alla rete virtuale. È ad esempio pratica diffusa unire la rete locale alla rete virtuale. Con la semplice risoluzione dei nomi predefinita, HDInsight non può accedere alle risorse della rete locale in base al nome. È vero anche il contrario. Le risorse nella rete locale non possono accedere alle risorse della rete virtuale in base al nome.

> [!WARNING]  
> È necessario creare il server DNS personalizzato e configurare la rete virtuale per il suo utilizzo prima di creare il cluster HDInsight.

Per abilitare la risoluzione dei nomi tra la rete virtuale e le risorse in reti aggiunte, è necessario eseguire le azioni seguenti:

1. Creare un server DNS personalizzato nella rete virtuale di Azure in cui si intende installare HDInsight.

2. Configurare la rete virtuale per l'utilizzo del server DNS personalizzato.

3. Trovare il suffisso DNS assegnato da Azure per la rete virtuale. Questo valore è simile a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Per informazioni sulla ricerca del suffisso DNS, vedere la sezione [Esempio: DNS personalizzato](hdinsight-create-virtual-network.md#example-dns).

4. Configurare l'inoltro tra i server DNS. La configurazione dipende dal tipo di rete remota.

   * Se la rete remota è una rete locale, configurare il DNS come indicato di seguito:
        
     * __DNS personalizzato__ (nella rete virtuale):

         * Inoltrare le richieste per il suffisso DNS della rete virtuale al sistema di risoluzione ricorsiva di Azure (168.63.129.16). Azure gestisce le richieste per le risorse nella rete virtuale

         * Inoltrare tutte le altre richieste al server DNS locale. Il server DNS locale gestisce tutte le altre richieste di risoluzione dei nomi, persino le richieste per le risorse Internet quali Microsoft.com.

     * __DNS locale__: inoltrare le richieste per il suffisso DNS di rete virtuale al server DNS personalizzato. Il server DNS personalizzato le inoltra quindi al sistema di risoluzione ricorsiva di Azure.

       Questa configurazione indirizza le richieste di nomi di dominio completi che contengono il suffisso DNS della rete virtuale al server DNS personalizzato. Tutte le altre richieste (anche per gli indirizzi Internet pubblici) vengono gestite dal server DNS locale.

   * Se la rete remota è un'altra rete virtuale di Azure, configurare il DNS come indicato di seguito:

     * __DNS personalizzato__ (in ogni rete virtuale):

         * Le richieste per il suffisso DNS delle reti virtuali vengono inoltrate ai server DNS personalizzati. Il DNS in ogni rete virtuale è responsabile della risoluzione delle risorse all'interno della propria rete.

         * Inoltrare tutte le altre richieste al sistema di risoluzione ricorsiva di Azure. Il sistema di risoluzione ricorsiva è responsabile della risoluzione delle risorse Internet e locali.

       Il server DNS per ogni rete inoltra le richieste all'altro, in base al suffisso DNS. Le altre richieste vengono risolte usando il sistema di risoluzione ricorsiva di Azure.

     Per un esempio di ogni configurazione, vedere la sezione [Esempio: DNS personalizzato](hdinsight-create-virtual-network.md#example-dns).

Per altre informazioni, vedere il documento [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="directly-connect-to-apache-hadoop-services"></a>Connettersi direttamente ai servizi Apache Hadoop

È possibile connettersi al cluster all'indirizzo `https://CLUSTERNAME.azurehdinsight.net`. Questo indirizzo usa un IP pubblico, che potrebbe non essere accessibile se sono stati usati gruppi di sicurezza di rete per limitare il traffico in ingresso da Internet. Quando si distribuisce il cluster in una rete virtuale è inoltre possibile accedervi tramite l'endpoint privato `https://CLUSTERNAME-int.azurehdinsight.net`. Questo endpoint viene risolto in un indirizzo IP privato all'interno della rete virtuale per l'accesso al cluster.

Per connettersi alle pagine Apache Ambari e ad altre pagine Web tramite la rete virtuale, seguire questa procedura:

1. Per individuare i nomi di dominio completi (FQDN) interni dei nodi del cluster HDInsight, usare uno dei modi seguenti:

    Replace `RESOURCEGROUP` with the name of the resource group that contains the virtual network, and then enter the command:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Nell'elenco di nodi restituito, trovare i nomi di dominio completi dei nodi head e usarli per connettersi ad Ambari e altri servizi Web. Usare ad esempio `http://<headnode-fqdn>:8080` per accedere ad Ambari.

    > [!IMPORTANT]  
    > Alcuni servizi ospitati nei nodi head sono attivi solo in un nodo per volta. Se nel tentativo di accedere a un servizio in un nodo head si riceve un messaggio di errore 404, passare al nodo head successivo.

2. Per determinare il nodo e la porta su cui un servizio è disponibile, vedere il documento [Porte usate dai servizi Hadoop su HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a id="networktraffic"></a> Controllo del traffico di rete

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Techniques for controlling inbound and outbound traffic to HDInsight clusters

Il traffico di rete nelle reti virtuali di Azure può essere controllato usando i modi seguenti:

* i **gruppi di sicurezza di rete** (NSG) consentono di filtrare il traffico di rete in ingresso e in uscita. Per altre informazioni, vedere il documento [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/security-overview.md).

* **Network virtual appliances** (NVA) can be used with outbound traffic only. NVAs replicate the functionality of devices such as firewalls and routers. Per altre informazioni, vedere il documento relativo alle [Appliance di rete](https://azure.microsoft.com/solutions/network-appliances).

As a managed service, HDInsight requires unrestricted access to the HDInsight health and management services both for incoming and outgoing traffic from the VNET. When using NSGs, you must ensure that these services can still communicate with HDInsight cluster.

![Diagram of HDInsight entities created in Azure custom VNET](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight with network security groups

If you plan on using **network security groups** to control network traffic, perform the following actions before installing HDInsight:

1. Identificare l'area di Azure che si intende usare per HDInsight.

2. Identify the service tags required by HDInsight for your region. For more information, see [Network security group (NSG) service tags for Azure HDInsight](hdinsight-service-tags.md).

3. Create or modify the network security groups for the subnet that you plan to install HDInsight into.

    * __Gruppi di sicurezza di rete__: consentono il traffico __in ingresso__ sulla porta __443__ dagli indirizzi IP. This will ensure that HDInsight management services can reach the cluster from outside the virtual network.

For more information on network security groups, see the [overview of network security groups](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Controlling outbound traffic from HDInsight clusters

For more information on controlling outbound traffic from HDInsight clusters, see [Configure outbound network traffic restriction for Azure HDInsight clusters](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Forced tunneling to on-premises

Il tunneling forzato è una configurazione di routing definita dall'utente in cui tutto il traffico da una subnet viene spinto verso una rete o un percorso specifico, ad esempio la rete locale. HDInsight does __not__ support forced tunneling of traffic to on-premises networks. 

## <a id="hdinsight-ip"></a> Indirizzi IP richiesti

If you use network security groups or user-defined routes to control traffic, please see [HDInsight management IP addresses](hdinsight-management-ip-addresses.md).
    
## <a id="hdinsight-ports"></a> Porte richieste

Se si intende usare un **firewall** e accedere al cluster dall'esterno su determinate porte, potrebbe essere necessario consentire il traffico sulle porte necessarie per lo scenario. Per impostazione predefinita, non è necessario definire uno speciale elenco delle porte consentite, purché al traffico di gestione di Azure illustrato nella sezione precedente venga consentito di raggiungere il cluster sulla porta 443.

Per un elenco di porte per servizi specifici, vedere il documento [Porte usate dai servizi Apache Hadoop su HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Per altre informazioni sulle regole del firewall per le appliance virtuali, vedere il documento [Scenario dell'appliance virtuale](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="load-balancing"></a>Bilanciamento del carico

When you create an HDInsight cluster, a load balancer is created as well. The type of this load balancer is at the [basic SKU level](../load-balancer/load-balancer-overview.md#skus) which has certain constraints. One of these constraints is that if you have two virtual networks in different regions, you cannot connect to basic load balancers. See [virtual networks FAQ: constraints on global vnet peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers), for more information.

## <a name="next-steps"></a>Passaggi successivi

* For code samples and examples of creating Azure Virtual Networks, see [Create virtual networks for Azure HDInsight clusters](hdinsight-create-virtual-network.md).
* Per un esempio completo di configurazione di HDInsight per la connessione a una rete locale, vedere [Connettere HDInsight alla rete locale](./connect-on-premises-network.md).
* For configuring Apache HBase clusters in Azure virtual networks, see [Create Apache HBase clusters on HDInsight in Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Per la configurazione della replica geografica di Apache HBase, vedere [Configurare la replica di cluster Apache HBase nelle reti virtuali di Azure](hbase/apache-hbase-replication.md).
* Per altre informazioni sulle reti virtuali di Azure, vedere [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).
* Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
* Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).
