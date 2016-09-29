<properties
	pageTitle="Estendere HDInsight con Rete virtuale | Microsoft Azure"  
	description="Informazioni su come usare Rete virtuale di Azure per la connessione di HDInsight ad altre risorse cloud o risorse nel proprio data center"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>


#Estendere le funzionalità di HDInsight usando Rete virtuale di Azure

Rete virtuale di Azure permette di estendere le soluzioni Hadoop in modo da aggiungere risorse locali, come SQL Server, o creare reti private sicure tra le risorse nel cloud.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>Che cos'è Rete virtuale di Azure?

[Rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud tra loro in una rete privata (solo cloud).

	![Diagramma di una configurazione solo cloud](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

	L'uso di Rete virtuale per collegare servizi di Azure ad Azure HDInsight rende possibili gli scenari seguenti:

	* **Richiamo di servizi o processi di HDInsight** da siti Web o servizi di Azure in esecuzione in macchine virtuali di Azure.

	* **Trasferimento diretto di dati** tra HDInsight e il database SQL di Azure, SQL Server o un'altra soluzione di archiviazione dei dati in esecuzione in una macchina virtuale.

	* **Combinazione di più server HDInsight** in un'unica soluzione. Un esempio consiste nell'usare un server Storm di HDInsight per i dati in ingresso e quindi archiviare i dati elaborati in un server HBase di HDInsight. I dati non elaborati possono essere archiviati anche in un server Hadoop di HDInsight per analisi future tramite MapReduce.

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN)

	La configurazione da sito a sito permette di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto.

	![Diagramma di una configurazione da sito a sito](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

	La configurazione da punto a sito permette di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

	![Diagramma di una configurazione da punto a sito](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

	L'uso di Rete virtuale per collegare il cloud e il data center permette scenari simili alla configurazione solo cloud. Tuttavia, invece di limitarsi a usare risorse nel cloud, permette anche di usare risorse nel data center.

	* **Trasferimento diretto di dati** tra HDInsight e il data center. Un esempio consiste nell'usare Sqoop per trasferire dati da o verso SQL Server o per leggere i dati generati da un'applicazione line-of-business.

	* **Richiamo di servizi o processi di HDInsight** da un'applicazione line-of-business. Un esempio consiste nell'usare API Java HBase per archiviare e recuperare dati da un cluster HBase di HDInsight.

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE] È necessario creare la rete virtuale di Azure prima del provisioning di un cluster HDInsight. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).

## Requisiti della rete virtuale

> [AZURE.IMPORTANT] La creazione di un cluster HDInsight su una rete virtuale richiede configurazioni di rete virtuale specifiche, come descritto in questa sezione.

###Reti virtuali basate sulla posizione

Azure HDInsight supporta solo le reti virtuali basate sulla località e attualmente non funziona con le reti virtuali basate su gruppi di affinità.

###Rete virtuale classica o v2

I cluster basati su Windows richiedono una rete virtuale classica, mentre i cluster basati su Linux richiedono una rete virtuale di Azure Resource Manager. Se non si dispone del tipo di rete corretto, non sarà utilizzabile quando si crea il cluster.

Se si dispone di risorse in una rete virtuale non utilizzabile da parte del cluster che si intende creare, creare una nuova rete virtuale che può essere utilizzata dal cluster e connetterla alla rete virtuale non compatibile. È quindi possibile creare il cluster nella versione della rete che richiede e questo sarà in grado di accedere alle risorse nell'altra rete, poiché le due sono unite. Per ulteriori informazioni sulla connessione di reti virtuali classiche e nuove, vedere [Connessione di VNet classiche a VNet nuove](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

###DNS personalizzato

Quando si crea una rete virtuale, Azure fornisce la risoluzione dei nomi predefinita per i servizi di Azure, ad esempio HDInsight, che vengono installati nella rete. Tuttavia, può essere necessario usare il proprio Domain Name System (DNS), per casi quali la risoluzione dei nomi di dominio tra più reti. Ad esempio, nel caso di una comunicazione tra servizi che si trovano in due reti virtuali associate. HDInsight supporta sia la risoluzione dei nomi predefinita di Azure sia il DNS personalizzato quando usato con la rete virtuale di Azure.

Per altre informazioni sull'uso del proprio server DNS con la rete virtuale di Azure, vedere la sezione __Risoluzione dei nomi usando il server DNS__ dell'articolo [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

###Reti virtuali protette

Il servizio HDInsight è un servizio gestito che richiede accesso Internet durante il provisioning e l'esecuzione. In questo modo Azure può monitorare l'integrità del cluster, avviare il failover di risorse cluster, modificare il numero di nodi nel cluster tramite operazioni di ridimensionamento e altre attività di gestione.

Se HDInsight deve essere installato in una rete virtuale protetta, è necessario consentire l'accesso in ingresso sulla porta 443 agli indirizzi IP seguenti, che consentono ad Azure di gestire il cluster HDInsight.

* 168\.61.49.99
* 23\.99.5.239
* 168\.61.48.131
* 138\.91.141.162

Consentendo a questi indirizzi l'accesso in ingresso dalla porta 443, sarà possibile installare correttamente HDInsight in una rete virtuale protetta.

> [AZURE.IMPORTANT] HDInsight non supporta la limitazione del traffico in uscita, solo del traffico in ingresso. Quando si definiscono regole del gruppo di sicurezza di rete per la subnet che contiene HDInsight, usare solo regole in ingresso.

Gli esempi seguenti spiegano come creare un nuovo gruppo di sicurezza di rete che ammette gli indirizzi specificati e applica il gruppo di sicurezza a una subnet all'interno della rete virtuale. Questa procedura presuppone la creazione di una rete virtuale e della subnet in cui si desidera installare HDInsight.

__Uso di Azure PowerShell__

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
        -NetworkSecurityGroupId $nsg

__Uso dell'interfaccia della riga di comando di Azure__

1. Usare il comando seguente per creare un nuovo gruppo di sicurezza di rete denominato `hdisecure`. Sostituire __RESOURCEGROUPNAME__ e __LOCATION__ con il gruppo di risorse che contiene la rete virtuale di Azure e la località (area) in cui è stato creato il gruppo.

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    Dopo aver creato il gruppo, si riceveranno informazioni sul nuovo gruppo. Cercare una riga simile alla seguente e salvare l'informazione `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure`. Queste informazioni saranno usare in un passaggio successivo.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. Usare le informazioni seguenti per aggiungere regole al nuovo gruppo di sicurezza di rete che ammettono la comunicazione in ingresso sulla porta 443 dal servizio integrità e gestione di Azure HDInsight. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo di risorse che contiene la rete virtuale di Azure.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. Dopo aver creato le regole, usare le informazioni seguenti per applicare il nuovo gruppo di sicurezza di rete a una subnet. Sostituire __RESOURCEGROUPNAME__ con il nome del gruppo di risorse che contiene la rete virtuale di Azure. Sostituire __VNETNAME__ e __SUBNETNAME__ con il nome della rete virtuale di Azure e la subnet da usare durante l'installazione di HDInsight.

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    Dopo avere completato il comando, è possibile installare HDInsight nella rete virtuale protetta sulla subnet usata in questa procedura.

> [AZURE.IMPORTANT] La procedura descritta precedentemente consente di accedere solo al servizio integrità e gestione di HDInsight nel cloud di Azure. In questo modo è possibile installare correttamente un cluster HDInsight nella subnet, tuttavia l'accesso al cluster HDInsight dall'esterno della rete virtuale è bloccato per impostazione predefinita. È necessario aggiungere ulteriori regole del gruppo di sicurezza di rete se si desidera abilitare l'accesso dall'esterno della rete virtuale.
>
> Per consentire l'accesso SSH da Internet, ad esempio, è necessario aggiungere una regola simile alla seguente:
>
> * Azure PowerShell: ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Interfaccia della riga di comando di Azure: ```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

Per altre informazioni sui gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Per informazioni sul controllo del routing in una rete virtuale di Azure, vedere [Cosa sono le route definite dall'utente e l'inoltro IP](../virtual-network/virtual-networks-udr-overview.md).

##<a id="tasks"></a>Attività e informazioni

Questa sezione contiene informazioni sulle attività comuni e alcune indicazioni che possono rivelarsi utili quando si usa HDInsight con una rete virtuale.

###Determinare l'FQDN

Al cluster HDInsight verrà assegnato un nome di dominio completo (FQDN) specifico per l'interfaccia di rete virtuale. Si tratta dell'indirizzo da usare per la connessione al cluster da altre risorse nella rete virtuale. Per determinare l'FQDN, usare l'URL seguente per eseguire query sul servizio di gestione Ambari:

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md).

È necessario specificare il nome del cluster e un servizio e un componente in esecuzione nel cluster, ad esempio il gestore di risorse YARN.

> [AZURE.NOTE] I dati restituiti consistono in un documento JSON (JavaScript Object Notation) che contiene molte informazioni sul componente. Per estrarre solo l'FQDN, è necessario usare un parser JSON per recuperare il valore `host_components[0].HostRoles.host_name`.

Ad esempio, per restituire l'FQDN di un cluster Hadoop di HDInsight, è possibile usare uno dei metodi seguenti per recuperare i dati per la gestione delle risorse YARN.

* [Azure PowerShell](../powershell-install-configure.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Connettersi ad HBase

Per connettersi a HBase in remoto tramite l'API Java, è necessario determinare gli indirizzi di quorum di Zookeeper per il cluster HBase e specificarli nell'applicazione.

Per ottenere l'indirizzo di quorum di ZooKeeper, usare uno dei metodi seguenti per eseguire query sul servizio di gestione Ambari:

* [Azure PowerShell](../powershell-install-configure.md)

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

> [AZURE.NOTE] Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari](hdinsight-monitor-use-ambari-api.md).

Dopo aver ottenuto le informazioni sul quorum, usarle nell'applicazione client.

Ad esempio, per un'applicazione Java che usa l'API HBase, è necessario aggiungere un file **hbase-site.xml** al progetto e specificare le informazioni sul quorum nel file nel modo seguente:

```
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

###Verificare la connettività di rete

Alcuni servizi, ad esempio SQL Server, possono limitare le connessioni di rete in ingresso. In questo modo HDInsight non potrà funzionare correttamente con questi servizi.

Se si verificano problemi di accesso a un servizio da HDInsight, consultare la documentazione relativa al servizio per assicurarsi di avere abilitato l'accesso alla rete. È anche possibile verificare l'accesso alla rete creando una macchina virtuale di Azure nella stessa rete virtuale e usare le utilità client per verificare che la macchina virtuale sia in grado di connettersi al servizio tramite la rete virtuale.

##<a id="nextsteps"></a>Passaggi successivi

Gli esempi seguenti mostrano come usare HDInsight con Rete virtuale di Azure.

* [Analisi dei dati dei sensori con Storm e HBase in HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): mostra come configurare un cluster Storm e HBase in una rete virtuale, nonché come scrivere dati in remoto in HBase da Storm.

* [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate](hdinsight-hadoop-provision-linux-clusters.md): fornisce informazioni sul provisioning dei cluster Hadoop, con informazioni sull'uso di Rete virtuale di Azure.

* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop-mac-linux.md): fornisce informazioni sull'uso di Sqoop per trasferire dati con SQL Server su una rete virtuale.

Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).

<!---HONumber=AcomDC_0914_2016-->