<properties
	pageTitle="Estendere HDInsight con Rete virtuale | Microsoft Azure"  
	description="Informazioni su come usare Rete virtuale di Azure per la connessione di HDInsight ad altre risorse cloud o risorse nel proprio data center"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/29/2016"
   ms.author="larryfr"/>


#Estendere le funzionalità di HDInsight usando Rete virtuale di Azure

Rete virtuale di Azure permette di estendere le soluzioni Hadoop in modo da aggiungere risorse locali, come SQL Server, o creare reti private sicure tra le risorse nel cloud.

> [AZURE.NOTE] HDInsight non supporta le reti virtuali di Azure basate sull'affinità. Con HDInsight è necessario usare reti virtuali basate sulla località.


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

###Subnet

È consigliabile creare una singola subnet per ogni cluster HDInsight.

###Rete virtuale classica o v2

I cluster basati su Windows richiedono una rete virtuale v1 (classica), mentre i cluster basati su Linux richiedono una rete virtuale v2 (Gestione risorse di Azure). Se non si dispone del tipo di rete corretto, non sarà utilizzabile quando si crea il cluster.

Se si dispone di risorse in una rete virtuale non utilizzabile da parte del cluster che si intende creare, creare una nuova rete virtuale che può essere utilizzata dal cluster e connetterla alla rete virtuale non compatibile. È quindi possibile creare il cluster nella versione della rete che richiede e questo sarà in grado di accedere alle risorse nell'altra rete, poiché le due sono unite. Per ulteriori informazioni sulla connessione di reti virtuali classiche e nuove, vedere [Connessione di VNet classiche a VNet nuove](../virtual-network/virtual-networks-arm-asm-s2s.md).

###Reti virtuali protette

HDInsight non è supportato sulle reti virtuali di Azure che limitano in modo esplicito l'accesso da e verso Internet. Ad esempio, l'uso di gruppi di sicurezza di rete o ExpressRoute per bloccare il traffico Internet alle risorse nella rete virtuale. Il servizio HDInsight è un servizio gestito e richiede l'accesso a Internet durante il provisioning e durante l'esecuzione per consentire ad Azure di monitorare l'integrità del cluster, avviare il failover di risorse cluster e altre attività di gestione automatizzate.

Se si vuole usare HDInsight su una rete virtuale che blocca il traffico Internet, è possibile seguire questa procedura:

1. Creare una nuova subnet all'interno della rete virtuale. Per impostazione predefinita, la nuova subnet può comunicare con Internet. Questo permette di installare HDInsight nella subnet. Dal momento che la nuova subnet si trova nella stessa rete virtuale delle subnet protette, può anche comunicare con le risorse installate al loro interno.

2. Creare il cluster HDInsight. Quando si configurano le impostazioni della rete virtuale per il cluster, selezionare la subnet creata nel passaggio 1.

> [AZURE.NOTE] I passaggi precedenti presuppongono che le comunicazioni non siano state limitate agli indirizzi IP _all'interno dell'intervallo di indirizzi IP della rete virtuale_. In tal caso potrebbe essere necessario modificare le limitazioni per consentire la comunicazione con la nuova subnet.

Se non si è certi di avere applicato restrizioni alla subnet in cui si vuole installare HDInsight o si vogliono rimuovere le restrizioni della subnet, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com).

2. Selezionare la rete virtuale.

3. Selezionare __Proprietà__.

4. Selezionare __Subnet__ e quindi la subnet specifica. Nel pannello della subnet le voci __Gruppo di sicurezza di retee__ e __Tabella route__ saranno impostate sul valore __Nessuna__ se non sono state applicate restrizioni.

    Se sono state applicate restrizioni, è possibile rimuovere la restrizione selezionando la voce __Gruppo di sicurezza di rete__ o __Tabella route__ voce e quindi selezionando __Nessuna__. Infine selezionare __Salva__ nel pannello Subnet per salvare le modifiche.
    
    ![Immagine del pannello Subnet e selezione del Gruppo di sicurezza di rete](./media/hdinsight-extend-hadoop-virtual-network/subnetnsg.png)

Per altre informazioni sui gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Per informazioni sul controllo del routing in una rete virtuale di Azure, vedere [Route e inoltro IP definito dall'utente](../virtual-network/virtual-networks-udr-overview.md).

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

<!---HONumber=AcomDC_0204_2016-->