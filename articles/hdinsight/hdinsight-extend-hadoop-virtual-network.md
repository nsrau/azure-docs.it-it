---
title: Estendere HDInsight con Rete virtuale | Microsoft Docs
description: Informazioni su come usare Rete virtuale di Azure per la connessione di HDInsight ad altre risorse cloud o risorse nel proprio data center
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: afd43fb536278d6aa76acaf7c9d18714bc5acd2b
ms.lasthandoff: 03/15/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Estendere le funzionalità di HDInsight usando Rete virtuale di Azure
La rete virtuale di Azure permette di estendere le soluzioni Hadoop per aggiungere risorse locali, come SQL Server. Permette anche di combinare più tipi di cluster HDInsight o di creare reti private protette tra risorse nel cloud.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure 2.0: per altre informazioni, vedere [Installare e configurare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

* Azure PowerShell: per altre informazioni, vedere [Installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Per eseguire la procedura descritta in questo documento, è necessaria la versione più recente dell'interfaccia della riga di comando di Azure e di Azure PowerShell. Se si usa una versione precedente, i comandi possono essere diversi. Per ottenere risultati ottimali, usare i collegamenti precedenti per installare le versioni più recenti.

## <a id="whatis"></a>Che cos'è Rete virtuale di Azure?

[Rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).
  
    ![Diagramma di una configurazione solo cloud](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    L'uso di Rete virtuale per collegare servizi di Azure ad Azure HDInsight rende possibili gli scenari seguenti:
  
    * **Richiamo di servizi o processi di HDInsight** da siti Web o servizi di Azure in esecuzione in macchine virtuali di Azure.
    * **Trasferimento diretto di dati** tra HDInsight e il database SQL di Azure, SQL Server o un'altra soluzione di archiviazione dei dati in esecuzione in una macchina virtuale.
    * **Combinazione di più server HDInsight** in un'unica soluzione. Sono disponibili vari tipi di cluster HDInsight, corrispondenti al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster. L'uso di una rete virtuale consente a più cluster di comunicare direttamente tra loro.

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN)
  
    La configurazione da sito a sito permette di connettere più risorse del centro dati alla rete virtuale di Azure. La connessione può essere eseguita con un dispositivo VPN hardware o con il servizio Routing e Accesso remoto.
  
    ![Diagramma di una configurazione da sito a sito](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    La configurazione da punto a sito permette di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.
  
    ![Diagramma di una configurazione da punto a sito](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    L'uso di Rete virtuale per collegare il cloud e il data center permette scenari simili alla configurazione solo cloud. Tuttavia, invece di limitarsi a usare risorse nel cloud, permette anche di usare risorse nel data center.
  
    * **Trasferimento diretto di dati** tra HDInsight e il data center. Un esempio consiste nell'usare Sqoop per trasferire dati da o verso SQL Server o per leggere i dati generati da un'applicazione line-of-business.
    * **Richiamo di servizi o processi di HDInsight** da un'applicazione line-of-business. Un esempio consiste nell'usare API Java HBase per archiviare e recuperare dati da un cluster HBase di HDInsight.

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Creare la rete virtuale di Azure prima del provisioning di un cluster HDInsight, quindi specificare la rete durante la creazione del cluster. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Requisiti della rete virtuale

> [!IMPORTANT]
> La creazione di un cluster HDInsight su una rete virtuale richiede configurazioni di rete virtuale specifiche, come descritto in questa sezione.

### <a name="location-based-virtual-networks"></a>Reti virtuali basate sulla posizione

Azure HDInsight supporta solo le reti virtuali basate sulla località e attualmente non funziona con le reti virtuali basate su gruppi di affinità.

### <a name="classic-or-v2-virtual-network"></a>Rete virtuale classica o v2

I cluster basati su Linux richiedono una rete virtuale di Azure Resource Manager, mentre i cluster basati su Windows richiedono una rete virtuale classica. Se non si dispone del tipo di rete corretto, la rete non sarà selezionabile quando si crea il cluster.

È possibile unire tipi di rete diversi e in tal modo accedere alle risorse in una rete virtuale non compatibile. Creare il cluster nella versione della rete richiesta e il cluster sarà in grado di accedere alle risorse nell'altra rete, poiché le due reti sono unite. Per altre informazioni sulla connessione tra reti virtuali classiche e reti di Azure Resource Manager, vedere [Connettere le reti virtuali classiche alle reti virtuali di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

### <a name="custom-dns"></a>DNS personalizzato

Quando si crea una rete virtuale, Azure fornisce la risoluzione dei nomi predefinita per i servizi di Azure, ad esempio HDInsight, che vengono installati nella rete. Tuttavia, può essere necessario usare il proprio Domain Name System (DNS), per casi quali la risoluzione dei nomi di dominio tra più reti. Ad esempio, nel caso di una comunicazione tra servizi che si trovano in due reti virtuali associate. Se usato con la rete virtuale di Azure, HDInsight supporta sia la risoluzione dei nomi predefinita di Azure sia il server DNS personalizzato.

Per altre informazioni sull'uso di un server DNS personalizzato, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

### <a name="secured-virtual-networks"></a>Reti virtuali protette

Il servizio HDInsight è un servizio gestito che richiede accesso Internet durante il provisioning e l'esecuzione. Azure usa la connettività Internet per:

* Verificare l'integrità dell’entità cluster
* Avviare il failover delle risorse del cluster
* Modificare il numero di nodi del cluster tramite operazioni di ridimensionamento
* Altre attività di gestione

Queste operazioni non richiedono l'accesso completo a Internet. Quando si limita l'accesso a Internet, consentire l'accesso in entrata sulla porta 443 per i seguenti indirizzi IP. In tal modo Azure è in grado di gestire HDInsight.

> [!IMPORTANT]
> Gli indirizzi IP che dovrebbero essere concessi sono specifici all'area in cui risiedono il cluster HDInsight e la rete virtuale. Usare i dati indicati di seguito per trovare gli indirizzi IP per l'area in uso.

__Brasile meridionale__:

* 191.235.84.104
* 191.235.87.113

__Canada orientale__:

* 52.229.127.96
* 52.229.123.172

__Canada centrale__:

* 52.228.37.66
* 52.228.45.222

__India centrale__:

* 52.172.153.209
* 52.172.152.49

__Stati Uniti centro-occidentali__:

* 52.161.23.15
* 52.161.10.167

__Stati Uniti occidentali 2__:

* 52.175.211.210
* 52.175.222.222

__Tutte le altre aree__:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Consentendo a questi indirizzi l'accesso in ingresso dalla porta 443, sarà possibile installare correttamente HDInsight in una rete virtuale protetta.

> [!IMPORTANT]
> HDInsight non supporta la limitazione del traffico in uscita, solo del traffico in ingresso. Quando si definiscono regole del gruppo di sicurezza di rete per la subnet che contiene HDInsight, usare solo regole in ingresso.

Gli esempi seguenti spiegano come creare un gruppo di sicurezza di rete che ammette gli indirizzi specificati e applica il gruppo di sicurezza a una subnet all'interno della rete virtuale. Modificare gli indirizzi IP usati in questo esempio in modo che corrispondano all'area di Azure in uso.

Questa procedura presuppone la creazione di una rete virtuale e della subnet in cui si desidera installare HDInsight. Vedere [Creare una rete virtuale usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!IMPORTANT]
> Osservare il valore `priority` usato in questi esempi. Le regole vengono verificate con il traffico di rete, in ordine di priorità. Quando una regola corrisponde ai criteri di verifica e viene applicata, non vengono verificate altre regole.
> 
> Se sono presenti regole personalizzate che determinano un blocco esteso del traffico in ingresso, ad esempio una regola di tipo **deny all** (nega tutto), può essere necessario modificare i valori di priorità degli esempi. Le regole degli esempi devono precedere quelle che bloccano l'accesso. In caso contrario verranno verificate per prime le regole di tipo **deny all** e quelle dell'esempio non verranno mai applicate. Evitare di bloccare le regole predefinite per una rete virtuale di Azure. È ad esempio consigliabile non creare una regola di tipo **nega tutto** che venga applicata prima della regola predefinita **ALLOW VNET INBOUND** (con priorità 65000).
> 
> Per altre informazioni sulle regole dei gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

**Uso del modello di gestione risorse di Azure**

Usare il modello di gestione risorse seguente disponibile nei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) per creare un cluster HDInsight in una rete virtuale con le configurazioni di rete protetta:

[Deploy a secured Azure VNet and an HDInsight Hadoop cluster within the VNet](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/) (Distribuire una rete virtuale protetta di Azure e un cluster HDInsight Hadoop nella rete virtuale)

**Uso di Azure PowerShell**

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroup $nsg

**Uso dell'interfaccia della riga di comando di Azure**

1. Usare il comando seguente per creare un nuovo gruppo di sicurezza di rete denominato `hdisecure`. Sostituire **RESOURCEGROUPNAME** con il gruppo di risorse che contiene la rete virtuale di Azure. Sostituire **LOCATION** con la posizione (area geografica) in cui è stato creato il gruppo.
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    Dopo aver creato il gruppo si ricevono informazioni sul nuovo gruppo.

2. Usare le informazioni seguenti per aggiungere regole al nuovo gruppo di sicurezza di rete che ammettono la comunicazione in ingresso sulla porta 443 dal servizio integrità e gestione di Azure HDInsight. Sostituire **RESOURCEGROUPNAME** con il nome del gruppo di risorse che contiene la rete virtuale di Azure.
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. Dopo aver creato le regole, usare le informazioni seguenti per recuperare l'identificatore univoco per questo gruppo di sicurezza di rete:

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    Il comando restituisce un valore simile al testo seguente:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Se non si ottengono i risultati previsti, nel comando inserire l'ID tra virgolette doppie.

4. Usare il comando seguente per applicare il gruppo di sicurezza di rete a una subnet. Sostituire i valori __GUID__ e __RESOURCEGROUPNAME__ con quelli restituiti nel passaggio precedente. Sostituire __VNETNAME__ e __SUBNETNAME__ con il nome di rete virtuale e il nome di subnet da usare per la creazione di un cluster HDInsight.
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    Dopo avere completato il comando, è possibile installare HDInsight nella rete virtuale protetta sulla subnet usata in questa procedura.

> [!IMPORTANT]
> La procedura sopra descritta consente di accedere solo al servizio integrità e gestione HDInsight nel cloud di Azure. Qualsiasi altro accesso al cluster HDInsight dall'esterno della rete virtuale è bloccato. Per abilitare l'accesso dall'esterno della rete virtuale è necessario aggiungere altre regole del gruppo di sicurezza di rete.
> 
> L'esempio seguente dimostra come abilitare l'accesso SSH da Internet: 
> 
> * Azure PowerShell: ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Interfaccia della riga di comando di Azure: ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

Per altre informazioni sui gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Per informazioni sul controllo del routing in una rete virtuale di Azure, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).

## <a id="tasks"></a>Attività e informazioni

Questa sezione contiene informazioni sulle attività comuni e alcune indicazioni che possono rivelarsi utili quando si usa HDInsight con una rete virtuale.

### <a name="determine-the-fqdn"></a>Determinare l'FQDN

Al cluster HDInsight viene assegnato un nome di dominio completo (FQDN) specifico per l'interfaccia di rete virtuale. Il nome di dominio completo (FQDN) è l'indirizzo da usare per la connessione al cluster da altre risorse di Azure nella rete virtuale. Per determinare l'FQDN, usare l'URL seguente per eseguire query sul servizio di gestione Ambari:

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

Usare il nome del cluster e un servizio e un componente in esecuzione nel cluster, ad esempio il gestore di risorse YARN.

> [!NOTE]
> I dati vengono restituiti come documento JavaScript Object Notation (JSON). Per estrarre solo l'FQDN, è necessario usare un parser JSON per recuperare il valore `host_components[0].HostRoles.host_name` .

Ad esempio, per restituire l'FQDN di un cluster Hadoop di HDInsight, è possibile usare uno dei metodi seguenti per recuperare i dati per la gestione delle risorse YARN.

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

> [!IMPORTANT]
> Se si dispone di accesso Internet limitato al cluster, non è possibile usare Ambari tramite Internet. In tal caso è necessario usare uno dei metodi seguenti per recuperare il nome FQDN:
>
> * Azure PowerShell: `Get-AzureRmNetworkInterface -ResourceGroupName GROUPNAME | Foreach-object { Write-Output $_.DnsSettings.InternalFqdn }`
> * Interfaccia della riga di comando di Azure 2.0: ` az network nic list --resource-group GROUPNAME --query '[].dnsSettings.internalFqdn'`
>
> In entrambi gli esempi sostituire __RESOURCEGROUPNAME__ con il nome del gruppo di risorse che contiene la rete virtuale.

### <a name="connecting-to-hbase"></a>Connettersi ad HBase

Per connettersi a HBase in remoto tramite l'API Java è necessario determinare gli indirizzi di quorum di Zookeeper per il cluster HBase e specificarli nell'applicazione.

Per ottenere l'indirizzo di quorum di ZooKeeper, usare uno dei metodi seguenti per eseguire query sul servizio di gestione Ambari:

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md).

Dopo aver ottenuto le informazioni sul quorum, usarle nell'applicazione client.

Ad esempio, per un'applicazione Java che usa l'API HBase, è necessario aggiungere un file **hbase-site.xml** al progetto e specificare le informazioni sul quorum nel file nel modo seguente:

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### <a name="verify-network-connectivity"></a>Verificare la connettività di rete

Alcuni servizi, ad esempio SQL Server, possono limitare le connessioni di rete in ingresso. Se si verificano problemi di accesso a un servizio da HDInsight, consultare la documentazione relativa al servizio per assicurarsi di avere abilitato l'accesso alla rete. È anche possibile verificare l'accesso alla rete creando una macchina virtuale di Azure nella stessa rete virtuale. Quindi usare le utilità client nella macchina virtuale per verificare che questa sia in grado di connettersi al servizio sulla la rete virtuale.

## <a id="nextsteps"></a>Passaggi successivi

Gli esempi seguenti mostrano come usare HDInsight con Rete virtuale di Azure.

* [Analizzare i dati del sensore con Apache Storm, hub eventi e HBase in HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): spiega come configurare un cluster Storm e HBase in una rete virtuale.
* [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md) : fornisce informazioni sul provisioning dei cluster Hadoop, con informazioni sull'uso di Rete virtuale di Azure.
* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop-mac-linux.md) : fornisce informazioni sull'uso di Sqoop per trasferire dati con SQL Server su una rete virtuale.

Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).


