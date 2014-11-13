<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari | Azure" metaKeywords="" description="Usare le API Apache Ambari per il provisioning, la gestione e il monitoraggio di cluster Hadoop. Gli intuitivi strumenti operatore e le API di Ambari nascondono la complessit&agrave; di Hadoop." services="hdinsight" documentationCenter="" title="Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari

Informazioni sul monitoraggio di cluster HDInsight versioni 3.1 e 2.1 con API Ambari.

**Tempo previsto per il completamento:** 15 minuti

## Contenuto dell'articolo

-   [Informazioni su Ambari][Informazioni su Ambari]
-   [Prerequisiti][Prerequisiti]
-   [Avvio rapido][Avvio rapido]
-   [API Ambari di monitoraggio][API Ambari di monitoraggio]
-   [Passaggi successivi][Passaggi successivi]

## <span id="whatisambari"></span></a>Informazioni su Ambari

[Apache Ambari][Apache Ambari] viene usato per il provisioning, la gestione e il monitoraggio di cluster Apache Hadoop. Comprende una raccolta di strumenti operatore intuitivi e un set affidabile di API che nascondono la complessità di Hadoop, semplificando le operazioni sui cluster. Per altre informazioni sulle API, vedere [Ambari API reference][Ambari API reference].

Attualmente HDInsight supporta solo la funzione di monitoraggio di Ambari. L'API Ambari v1.0 è supportata dal cluster HDInsight versione 2.1 e 3.0. Questo articolo descrive l'accesso alle API Ambari su cluster HDInsight versioni 3.1 e 2.1. La differenza principale tra i due è che alcuni componenti sono cambiati con l'introduzione di nuove funzionalità (ad esempio, il server della cronologia dei processi).

## <span id="prerequisites"></span></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una workstation** in cui sia stato installato e configurato Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell]. Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell][esecuzione di script di Windows PowerShell].

    [Curl][Curl] è facoltativo. Può essere installato da [questo collegamento][questo collegamento].

    > [WACOM.NOTE] Quando si usa il comando curl in Windows, per i valori delle opzioni sostituire le virgolette alle virgolette singole.

-   **Un cluster HDInsight di Azure**. Per istruzioni sul provisioning del cluster, vedere [Introduzione all'uso di HDInsight][Introduzione all'uso di HDInsight] o [Provisioning di cluster HDInsight][Provisioning di cluster HDInsight]. Per completare l'esercitazione sono necessari i dati seguenti:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà del cluster</th>
    <th align="left">Nome variabile di PowerShell</th>
    <th align="left">Valore</th>
    <th align="left">Descrizione</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome del cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Il nome del cluster HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome utente cluster</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Nome utente del cluster specificato al momento del provisioning.</td>
    </tr>
    <tr class="odd">
    <td align="left">Password cluster</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Password dell'utente del cluster.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] L'inserimento dei valori nelle tabelle potrà essere utile per completare questa esercitazione.

## <span id="jumpstart"></span></a>Avvio rapido

È possibile usare Ambari per monitorare cluster HDInsight in vari modi.

**Uso di Azure PowerShell**

Di seguito è riportato uno script di PowerShell che consente di ottenere informazioni sul jobtracker di MapReduce *su un cluster 3.1*. La differenza principale consiste nel fatto che le informazioni verranno estratte dal servizio YARN (piuttosto che da Map Reduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'yarn.queueMetrics'

Di seguito è riportato uno script di PowerShell che consente di ottenere informazioni sul jobtracker di MapReduce *su un cluster 2.1*:

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

![Output di jobtracker][Output di jobtracker]

**Uso di curl**

Di seguito è riportato un esempio di recupero di informazioni sul cluster mediante Curl:

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

Nota per la versione dell'8/10/2014:
Quando si usa l'endpoint Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", il campo *host\_name* ora restituisce il nome di dominio completo (FQDN) del nodo invece del solo nome host. Prima della versione dell'8/10/2014, questo esempio restituiva semplicemente "**headnode0**". Dopo la versione dell'8/10/2014, si otterrà il nome FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**", come nell'esempio precedente. Questa modifica si è resa necessaria per facilitare scenari in cui sia possibile implementare più tipi di cluster, come HBase e Hadoop, in un'unica rete virtuale (VNET). Ciò accade, ad esempio, quando si usa HBase come piattaforma back-end per Hadoop.

## <span id="monitor"></span></a>API Ambari di monitoraggio

La tabella seguente riporta alcune delle chiamate API Ambari di monitoraggio più comuni. Per altre informazioni sull'API, vedere [Ambari API reference][Ambari API reference].

<table border="1">
<tr>
<th>
Chiamata API di monitoraggio

</th>
<th>
URI

</th>
<th>
Descrizione

</th>
</tr>
<tr>
<td>
Get clusters

</td>
<td>
`/api/v1/clusters`

</td>
<td>
</td>
</tr>
<tr>
<td>
Get cluster info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net`

</td>
<td>
cluster, servizi, hosts

</td>
</tr>
<tr>
<td>
Get services

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`

</td>
<td>
I servizi comprendono: hdfs, mapreduce

</td>
</tr>
<tr>
<td>
Get services info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`

</td>
<td>
</td>
</tr>
<tr>
<td>
Get service components

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`

</td>
<td>
HDFS: namenode, datanode
MapReduce: jobtracker; tasktracker

</td>
</tr>
<tr>
<td>
Get component info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`

</td>
<td>
ServiceComponentInfo, componenti host, metriche

</td>
</tr>
<tr>
<td>
Get hosts

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`

</td>
<td>
headnode0, workernode0

</td>
</tr>
<tr>
<td>
Get host info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName> `

<td>
</td>
</tr>
<tr>
<td>
Get host components

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`

</td>
<td>
namenode, resourcemanager

</td>
</tr>
<tr>
<td>
Get host component info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`

</td>
<td>
HostRoles, componente, host, metriche

</td>
</tr>
<tr>
<td>
Get configurations

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations `

</td>
<td>
Tipi di config: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
<tr>
<td>
Get configuration info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName> `

</td>
<td>
Tipi di config: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
</table>
## <span id="nextsteps"></span></a>Passaggi successivi

In questa esercitazione si è appreso come usare le chiamate API Ambari di monitoraggio. Per altre informazioni, vedere:

-   [Amministrazione di cluster HDInsight tramite il portale di gestione][Amministrazione di cluster HDInsight tramite il portale di gestione]
-   [Amministrazione di cluster HDInsight tramite Azure PowerShell][Amministrazione di cluster HDInsight tramite Azure PowerShell]
-   [Amministrazione di cluster HDInsight tramite l'interfaccia della riga di comando][Amministrazione di cluster HDInsight tramite l'interfaccia della riga di comando]
-   [Documentazione relativa a HDInsight][Documentazione relativa a HDInsight]
-   [Introduzione all'uso di HDInsight][Introduzione all'uso di HDInsight]

  [Informazioni su Ambari]: #whatisambari
  [Prerequisiti]: #prerequisites
  [Avvio rapido]: #jumpstart
  [API Ambari di monitoraggio]: #monitor
  [Passaggi successivi]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Ambari API reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [esecuzione di script di Windows PowerShell]: http://technet.microsoft.com/it-it/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [questo collegamento]: http://curl.haxx.se/download.html
  [Introduzione all'uso di HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster HDInsight]: ../hdinsight-provision-clusters/
  [Output di jobtracker]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [Amministrazione di cluster HDInsight tramite il portale di gestione]: ../hdinsight-administer-use-management-portal/
  [Amministrazione di cluster HDInsight tramite Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Amministrazione di cluster HDInsight tramite l'interfaccia della riga di comando]: ../hdinsight-administer-use-command-line/
  [Documentazione relativa a HDInsight]: /it-it/documentation/services/hdinsight/
