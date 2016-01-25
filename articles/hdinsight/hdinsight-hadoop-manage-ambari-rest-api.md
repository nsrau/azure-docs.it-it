<properties
   pageTitle="Monitorare e gestire i cluster HDInsight mediante l'API REST Apache Ambari | Microsoft Azure"
   description="Informazioni sull'uso di Ambari per monitorare e gestire cluster HDInsight basati su Linux. In questo documento si apprenderà come usare l'API REST Ambari inclusa nei cluster HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/08/2016"
   ms.author="larryfr"/>

#Gestire i cluster HDInsight mediante l'API REST Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari semplifica la gestione e il monitoraggio di un cluster Hadoop grazie a un'interfaccia utente Web facile da usare e alle API REST. Ambari è incluso nei cluster HDInsight basati su Linux e viene usato per monitorare il cluster e modificare la configurazione. In questo documento vengono illustrati i concetti fondamentali sull'utilizzo dell'API REST Ambari tramite l'esecuzione di attività comuni, ad esempio l'individuazione del nome di dominio completo dei nodi cluster o dell'account di archiviazione predefinito usato dal cluster.

> [AZURE.NOTE]Le informazioni contenute in questo articolo sono specifiche solo per i cluster HDInsight basati su Linux. Per i cluster HDInsight basati su Windows, è disponibile solo un sottoinsieme di funzionalità di monitoraggio tramite l'API REST Ambari. Vedere [Monitorare Hadoop basato su Windows in HDInsight con l'API Ambari](hdinsight-monitor-use-ambari-api.md).

##Prerequisiti

* [cURL](http://curl.haxx.se/): cURL è un'utilità multipiattaforma che consente di utilizzare le API REST dalla riga di comando. In questo documento viene usata per comunicare con l'API REST Ambari.
* [jq](https://stedolan.github.io/jq/): jq è un'utilità da riga di comando multipiattaforma per l'utilizzo di documenti JSON. In questo documento viene usata per analizzare i documenti JSON restituiti dall'API REST Ambari.

##<a id="whatis"></a>Cos'è Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> semplifica la gestione di Hadoop, fornendo un'interfaccia utente Web intuitiva che può essere usata per effettuare il provisioning, la gestione e il monitoraggio dei cluster Hadoop. Gli sviluppatori possono integrare queste funzionalità nelle proprie applicazioni usando le <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API REST Ambari</a>.

Ambari viene fornito per impostazione predefinita con i cluster HDInsight basati su Linux.

##API REST

L'URI di base per l'API REST Ambari su HDInsight è https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, dove __CLUSTERNAME__ è il nome del cluster.

> [AZURE.IMPORTANT]La connessione ad Ambari su HDInsight richiede HTTPS. È anche necessario effettuare l'autenticazione in Ambari mediante il nome dell'account amministratore (il valore predefinito è __admin__) e la password fornita durante la creazione del cluster.

L'esempio seguente illustra l'uso di cURL per eseguire una richiesta GET nell'API REST:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Se si esegue questa richiesta, sostituendo __PASSWORD__ con la password dell'amministratore per il cluster e __CLUSTERNAME__ con il nome del cluster, si riceverà un documento JSON che inizia con informazioni analoghe alle seguenti:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Poiché si tratta di JSON, è in genere più semplice usare un parser JSON per recuperare i dati. Ad esempio, se si vuole recuperare un conteggio di avvisi, contenuto nell'elemento __"Host/host\_status/ALERT"__, è possibile usare il codice seguente per accedere direttamente al valore:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
Il documento JSON verrà recuperato e verrà eseguito il piping dell'output a jq. `'.Clusters.health_report."Host/host_status/ALERT"'` indica l'elemento da recuperare nel documento JSON.

> [AZURE.NOTE]L'elemento __Host/host\_status/ALERT__ è racchiuso tra virgolette per indicare che '/' fa parte del nome dell'elemento. Per altre informazioni sull'uso di jq, vedere il [sito Web jq](https://stedolan.github.io/jq/).

##Esempio: Ottenere il nome di dominio completo dei nodi del cluster

Quando si usa HDInsight, potrebbe essere necessario conoscere il nome di dominio completo di un nodo del cluster. È possibile recuperare con facilità il nome di dominio completo per i diversi nodi del cluster mediante il codice seguente:

* __Nodi head__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodi di lavoro__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodi Zookeeper__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Si noti che ogni richiesta segue lo stesso modello usato per la query su un componente in esecuzione su tali nodi, recuperando successivamente gli elementi `host_name` che contengono il nome di dominio completo per i nodi specifici.

L'elemento `host_components` del documento restituito contiene più elementi. Se si usa `.host_components[]` e quindi si specifica un percorso entro l'elemento, si creerà un ciclo in ogni elemento e verrà recuperato il valore dal percorso specifico. Se si vuole solo un valore, ad esempio la prima voce relativa al nome di dominio completo, è possibile restituire gli elementi come una raccolta e quindi selezionare una voce specifica:

    jq '[.host_components[].HostRoles.host_name][0]'

Verrà restituito il primo nome di dominio completo dalla raccolta.

##Esempio: Ottenere l'account di archiviazione e il contenitore predefiniti

Quando si crea un cluster HDInsight, è necessario usare un account di archiviazione di Azure e un contenitore BLOB come risorsa di archiviazione predefinita per il cluster. È possibile usare Ambari per recuperare queste informazioni dopo la creazione del cluster, ad esempio, se si vuole scrivere dati a livello di codice direttamente nel contenitore.

Il codice seguente recupererà l'URI WASB della risorsa di archiviazione predefinita dei cluster:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]Verrà restituita la prima configurazione applicata al server (`service_config_version=1`) che conterrà queste informazioni. Se si recupera un valore che è stato modificato dopo la creazione del cluster, potrebbe essere necessario elencare le versioni della configurazione e recuperare la versione più recente.

Verrà restituito un valore analogo al seguente, dove __CONTAINER__ è il contenitore predefinito e __ACCOUNTNAME__ è il nome dell'account di archiviazione di Azure:

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

È quindi possibile usare queste informazioni con l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) per caricare o scaricare dati dal contenitore. Ad esempio:

1. Ottenere il gruppo di risorse per l'account di archiviazione. Sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione recuperato da Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Verrà restituito il nome del gruppo di risorse per l'account.
    
    > [AZURE.NOTE]Se il comando non restituisce risultati, può essere necessario modificare l'interfaccia della riga di comando di Azure in modalità Gestione risorse di Azure ed eseguire nuovamente il comando. Per passare alla modalità Gestione risorse di Azure, usare il comando seguente.
    >
    > `azure config mode arm`
    
2. Ottenere la chiave per l'account di archiviazione. Sostituire __GROUPNAME__ con il gruppo di risorse del passaggio precedente. Sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Verrà restituita la chiave primaria per l'account.
    
3. Usare il comando di caricamento per archiviare un file nel contenitore:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione. Sostituire __ACCOUNTKEY__ con la chiave recuperata in precedenza. __FILEPATH__ è il percorso del file da caricare, mentre __BLOBPATH__ è il percorso nel contenitore.

    Ad esempio, se si vuole che il file venga visualizzato in HDInsight in corrispondenza di wasb://example/data/filename.txt,__BLOBPATH__ sarà `example/data/filename.txt`.

##Passaggi successivi

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE]Alcune funzionalità di Ambari sono disabilitate, perché vengono gestite dal servizio cloud HDInsight, ad esempio, l'aggiunta o la rimozione di host dal cluster o l'aggiunta di nuovi servizi.

<!---HONumber=AcomDC_0114_2016-->