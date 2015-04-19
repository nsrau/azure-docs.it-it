<properties
   pageTitle="Estendere HDInsight con Rete virtuale di Azure | VNet" metaKeywords="virtual network, vnet, azure, hdinsight" description="Informazioni su come usare Rete virtuale di Azure per la connessione di HDInsight ad altre risorse cloud o risorse nel proprio data center"
   services="hdinsight"
   documentationCenter=""
   authors="blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/2/2015"
   ms.author="larryfr"/>


#Estensione delle funzionalità di HDInsight tramite Rete virtuale di Azure

Rete virtuale di Azure consente di estendere le soluzioni di Hadoop per l'aggiunta nelle risorse locali, come SQL Server, o per la creazione di reti private protette tra le risorse nel cloud.

> [AZURE.NOTE] HDInsight non supporta attualmente reti virtuali di Azure basate sull'affinità. Con HDInsight è necessario usare le reti virtuali basate sulla posizione.

##<a id="whatis"></a>Che cos'è la rete virtuale di Azure?

La [rete virtuale di Azure](/documentation/services/virtual-network/) consente di creare una rete sicura e persistente che contiene le risorse necessarie alla propria soluzione. Una rete virtuale consente di:

* Connettere le risorse cloud in una rete privata (solo cloud)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

	L'uso della rete virtuale per collegare i servizi di Azure con HDInsight rende possibili i seguenti scenari.

	* **Richiamo di servizi o processi di HDInsight** da Siti Web di Azure o servizi in esecuzione in Macchine virtuali di Azure.

	* **Trasferimento diretto di dati** tra HDInsight e il database SQL, SQL Server o un'altra soluzione di archiviazione dati in esecuzione in una macchina virtuale.

	* **Combinazione di più server HDInsight** in un'unica soluzione. Ad esempio, l'uso di un server HDInsight Storm per i dati in arrivo, quindi l'archiviazione in un server HDInsight HBase dei dati elaborati. I dati non elaborati possono essere archiviati anche in un server HDInsight Hadoop per analisi future tramite MapReduce.

* Connettere le risorse cloud alla rete del data center locale (da sito a sito o da punto a sito) usando una rete privata virtuale (VPN, Virtual Private Network)

	La configurazione da sito a sito consente di connettere più risorse dal data center alla rete virtuale di Azure usando una rete VPN hardware o il servizio Routing e accesso remoto

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	La configurazione da punto a sito consente di connettere una specifica risorsa alla rete virtuale di Azure usando una rete VPN software

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

	L'uso della rete virtuale per collegare il cloud e il data center rende possibili scenari simili per la configurazione solo del cloud, ma invece di limitarsi all'uso di risorse nel cloud, è inoltre possibile usare le risorse del data center.

	* **Trasferimento diretto di dati** tra HDInsight e il data center. Ad esempio, l'uso di Sqoop per il trasferimento di dati in o da SQL Server o per la lettura dei dati generati da un'applicazione line-of-business.

	* **Richiamo di servizi o processi di HDInsight** da un'applicazione line-of-business. Ad esempio, l'uso delle API Java HBase per archiviare e recuperare dati da un cluster HBase di HDInsight.

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] È necessario creare la rete virtuale di Azure prima del provisioning di un cluster HDInsight. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.
>
> È consigliabile definire una singola subnet per ogni cluster.

Per altre informazioni sul provisioning di un cluster HDInsight in una rete virtuale, vedere l'articolo relativo al [provisioning di cluster Hadoop in HDInsight](hdinsight-provision-clusters.md).

##<a id="tasks"></a>Attività e informazioni

Questa sezione contiene informazioni sulle attività comuni e indicazioni che possono rivelarsi utili quando si usa HDInsight con una rete virtuale.

###Determinare il nome di dominio completo

Al cluster HDInsight verrà assegnato un nome di dominio completo specifico per l'interfaccia di rete virtuale. Questo è l'indirizzo da usare per la connessione al cluster da altre risorse nella rete virtuale. Per determinare il nome di dominio completo, usare il seguente URL per eseguire query nel servizio di gestione Ambari.

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambar](hdinsight-monitor-use-ambari-api.md).

È necessario specificare il nome del cluster e un servizio e un componente in esecuzione nel cluster, ad esempio il gestore di risorse YARN.

> [AZURE.NOTE] I dati restituiti sono un documento JSON che contiene molte informazioni sul componente. Per estrarre solo il nome di dominio completo, è necessario usare un parser JSON per recuperare il valore `host_components[0].HostRoles.host_name`.

Ad esempio, per restituire il nome di dominio completo di un cluster HDInsight Hadoop, è possibile usare uno dei seguenti metodi per recuperare i dati per il gestore di risorse YARN.

* [Azure PowerShell](install-configure-powershell.md)

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

* [Curl](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Connettersi ad HBase

Per connettersi ad HBase in remoto tramite l'API Java, è necessario determinare gli indirizzi di quorum di Zookeeper per il cluster HBase e specificare questa informazione nell'applicazione.

Per ottenere l'indirizzo di quorum di Zookeeper, usare uno dei seguenti metodi per eseguire query nel servizio di gestione di Ambari.

* [Azure PowerShell](install-configure-powershell.md)

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

* [Curl](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Per altre informazioni sull'uso di Ambari con HDInsight, vedere [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambar](hdinsight-monitor-use-ambari-api.md).

Dopo aver ottenuto le informazioni sul quorum, usarle nell'applicazione client.

Ad esempio, per un'applicazione Java che usa l'API HBase, è necessario aggiungere un file **hbase-site.xml** al progetto e specificare le informazioni sul quorum nel file come indicato di seguito.

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

Se si verificano problemi di accesso a un servizio da HDInsight, consultare la documentazione relativa al servizio per assicurarsi di avere abilitato l'accesso alla rete. È inoltre possibile verificare l'accesso alla rete mediante la creazione di una macchina virtuale di Azure nella stessa rete virtuale e usare le utilità client per verificare che la macchina virtuale possa connettersi al servizio tramite la rete virtuale.

##<a id="nextsteps"></a>Passaggi successivi

I seguenti esempi illustrano come usare HDInsight con Rete virtuale di Azure.

* [Analizzare i dati del sensore in tempo reale con Storm e HBase in HDInsight](hdinsight-storm-sensor-data-analysis.md): illustra come configurare un cluster Storm e HBase in una rete virtuale, nonché come scrivere dati in remoto in HBase da Storm.

* [Effettuare il provisioning di cluster HBase in Rete virtuale di Azure](hdinsight-hbase-provision-vnet.md) : fornisce informazioni sul provisioning di un cluster HBase in una rete virtuale di Azure

* [Effettuare il provisioning dei cluster Hadoop in HDInsight](hdinsight-provision-clusters.md): fornisce informazioni sul provisioning dei cluster Hadoop, con informazioni sull'uso di Rete virtuale di Azure

* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md) : fornisce informazioni sull'uso di Sqoop per trasferire dati con SQL Server su una rete virtuale

Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di Rete virtuale di Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=47-->
