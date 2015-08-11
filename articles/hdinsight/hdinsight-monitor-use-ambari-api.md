<properties
	pageTitle="Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari | Microsoft Azure"
	description="Usare le API Apache Ambari per il provisioning, la gestione e il monitoraggio di cluster Hadoop. Gli intuitivi strumenti operatore e le API nascondono la complessità di Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	editor="cgronlun"
	manager="paulettm"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="jgao"/>

# Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari

Informazioni sul monitoraggio di cluster HDInsight versioni 3.1 e 2.1 con API Ambari.

> [AZURE.NOTE]Le informazioni contenute in questo articolo sono relative principalmente ai cluster HDInsight basati su Windows, che forniscono una versione in sola lettura dell’API REST Ambari. Per i cluster basati su Linux, vedere [Gestire i cluster Hadoop tramite Ambari](hdinsight-hadoop-manage-ambari.md).

## <a id="whatisambari"></a> Cos'è Ambari?

[Apache Ambari][ambari-home] viene usato per il provisioning, la gestione e il monitoraggio di cluster Apache Hadoop. Comprende una raccolta di strumenti operatore intuitivi e un set affidabile di API che nascondono la complessità di Hadoop, semplificando le operazioni sui cluster. Per altre informazioni, vedere la [documentazione di riferimento dell'API Ambari][ambari-api-reference].


Attualmente HDInsight supporta solo la funzione di monitoraggio di Ambari. L'API Ambari 1.0 è supportata dai cluster HDInsight versioni 3.0 e 2.1. Questo articolo descrive l'accesso alle API Ambari su cluster HDInsight versioni 3.1 e 2.1. La differenza principale tra i due è che alcuni componenti sono cambiati con l'introduzione di nuove funzionalità (ad esempio, il server della cronologia dei processi).


##<a id="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


- (Facoltativo) [cURL][curl]. Per installarlo, vedere la pagina relativa alle [versioni e ai download di cURL][curl-download].

	>[AZURE.NOTE]Quando si usa il comando cURL in Windows, per i valori delle opzioni usare le virgolette doppie invece di quelle singole.

- **Un cluster HDInsight di Azure**. Per istruzioni sul provisioning dei cluster, vedere [Introduzione a HDInsight][hdinsight-get-started] o [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]. Per completare l'esercitazione sono necessari i dati seguenti:

	<table border="1">
	<tr><th>Proprietà del cluster</th><th>Nome variabile di Azure PowerShell</th><th>Valore</th><th>Descrizione</th></tr>
	<tr><td>Nome del cluster HDInsight</td><td>$clusterName</td><td></td><td>Il nome del cluster HDInsight.</td></tr>
	<tr><td>Nome utente cluster</td><td>$clusterUsername</td><td></td><td>Nome utente del cluster specificato al momento del provisioning.</td></tr>
	<tr><td>Password cluster</td><td>$clusterPassword</td><td></td><td>Password dell'utente del cluster.</td></tr>
	</table>

	> [AZURE.NOTE] Specificare i valori nella tabella. Questa operazione sarà utile per completare l'esercitazione.



##<a id="jumpstart"></a>Avvio rapido

È possibile usare Ambari per monitorare cluster HDInsight in vari modi.

**Uso di Azure PowerShell**

Di seguito è riportato uno script di Azure PowerShell per ottenere le informazioni di analisi del processo MapReduce *in un cluster HDInsight 3.1*. La differenza principale consiste nel fatto che queste informazioni vengono estratte dal servizio YARN (anziché da MapReduce).

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'yarn.queueMetrics'

Di seguito è riportato uno script di Azure PowerShell per ottenere le informazioni di analisi del processo MapReduce *in un cluster HDInsight 2.1*:

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'mapred.JobTracker'

L'output è:

![Output di jobtracker][img-jobtracker-output]

**Uso di cURL**

Di seguito è riportato un esempio di recupero di informazioni sul cluster mediante cURL:

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

L'output è:

	{"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
	 "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
	 "services"[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
	 "hosts":[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Note per la versione rilasciata in data 08/10/2014**:

Quando si usa l'endpoint Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", il campo *host_name* restituisce il nome di dominio completo (FQDN) del nodo anziché il solo nome host. Prima della versione rilasciata l'8/10/2014, questo esempio restituiva semplicemente "**headnode0**". Dopo la versione dell'8/10/2014, si ottiene il nome FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**", come nell'esempio precedente. Questa modifica si è resa necessaria per facilitare scenari in cui sia possibile implementare più tipi di cluster, come HBase e Hadoop, in un'unica rete virtuale (VNET). Ciò accade, ad esempio, quando si usa HBase come piattaforma back-end per Hadoop.

##<a id="monitor"></a>API Ambari di monitoraggio

La tabella seguente riporta alcune delle chiamate API Ambari di monitoraggio più comuni. Per altre informazioni, vedere la [documentazione di riferimento dell'API Ambari][ambari-api-reference].

<table border="1">
<tr><th>Chiamata API di monitoraggio</th><th>URI</th><th>Descrizione</th></tr>
<tr><td>Get clusters</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>Get cluster info.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net</tt></td><td>cluster, servizi, hosts</td></tr>
<tr><td>Get services</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services</tt></td><td>Services include: hdfs, mapreduce</td></tr>
<tr><td>Get services info.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName></tt></td><td></td></tr>
<tr><td>Get service components</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName>/components</tt></td><td>HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker</td></tr>
<tr><td>Get component info.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/services/&lt;ServiceName>/components/&lt;ComponentName></tt></td><td>ServiceComponentInfo, componenti host, metriche</td></tr>
<tr><td>Get hosts</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts</tt></td><td>headnode0, workernode0</td></tr>
<tr><td>Get host info.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>
</td><td></td></tr>
<tr><td>Get host components</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components </tt></td><td>namenode, resourcemanager</td></tr>
<tr><td>Get host component info.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/hosts/&lt;HostName>/host_components/&lt;ComponentName> </tt></td><td>HostRoles, componente, host, metriche</td></tr>
<tr><td>Get configurations</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations </tt></td><td>Config types: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>Get configuration info.</td><td><tt>/api/v1/clusters/&lt;ClusterName>.azurehdinsight.net/configurations?type=&lt;ConfigType>&amp;tag=&lt;VersionName> </tt></td><td>Config types: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>


##<a id="nextsteps"></a>Passaggi successivi

In questa esercitazione si è appreso come utilizzare le chiamate API Ambari di monitoraggio. Per ulteriori informazioni, vedere:

- [Gestire cluster HDInsight tramite il portale di gestione][hdinsight-admin-portal]
- [Gestire cluster HDInsight tramite Azure PowerShell][hdinsight-admin-powershell]
- [Gestire cluster HDInsight tramite l'interfaccia della riga di comando][hdinsight-admin-cli]
- [Documentazione relativa a HDInsight][hdinsight-documentation]
- [Introduzione all'uso di HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
 

<!-----HONumber=July15_HO4-->