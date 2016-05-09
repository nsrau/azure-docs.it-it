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
   ms.date="04/19/2016"
   ms.author="larryfr"/>

#Gestire i cluster HDInsight mediante l'API REST Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari semplifica la gestione e il monitoraggio di un cluster Hadoop grazie a un'interfaccia utente Web facile da usare e alle API REST. Ambari è incluso nei cluster HDInsight basati su Linux e viene usato per monitorare il cluster e modificare la configurazione. In questo documento vengono illustrati i concetti fondamentali sull'utilizzo dell'API REST Ambari tramite l'esecuzione di attività comuni, ad esempio l'individuazione del nome di dominio completo dei nodi cluster o dell'account di archiviazione predefinito usato dal cluster.

> [AZURE.NOTE] Le informazioni contenute in questo articolo sono specifiche solo per i cluster HDInsight basati su Linux. Per i cluster HDInsight basati su Windows, è disponibile solo un sottoinsieme di funzionalità di monitoraggio tramite l'API REST Ambari. Vedere [Monitorare Hadoop basato su Windows in HDInsight con l'API Ambari](hdinsight-monitor-use-ambari-api.md).

##Prerequisiti

* [cURL](http://curl.haxx.se/): cURL è un'utilità multipiattaforma che consente di utilizzare le API REST dalla riga di comando. In questo documento viene usata per comunicare con l'API REST Ambari.
* [jq](https://stedolan.github.io/jq/): jq è un'utilità da riga di comando multipiattaforma per l'utilizzo di documenti JSON. In questo documento viene usata per analizzare i documenti JSON restituiti dall'API REST Ambari.
* [Interfaccia della riga di comando di Azure](../xplat-cli-install.md): è un'utilità da riga di comando multipiattaforma per lavorare con i servizi Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a id="whatis"></a>Cos'è Ambari?

[Apache Ambari](http://ambari.apache.org) semplifica la gestione di Hadoop grazie a un'interfaccia utente Web intuitiva che può essere usata per effettuare il provisioning, la gestione e il monitoraggio dei cluster Hadoop. Gli sviluppatori possono integrare queste funzionalità nelle proprie applicazioni usando le [API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari viene fornito per impostazione predefinita con i cluster HDInsight basati su Linux.

##API REST

L'URI di base per l'API REST Ambari su HDInsight è https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, dove __CLUSTERNAME__ è il nome del cluster.

> [AZURE.IMPORTANT] A differenza delle altre occorrenze nell'URI, nel nome del cluster nella parte del nome di dominio completo (FQDN) dell'URI (CLUSTERNAME.azurehdinsight.net) non viene fatta distinzione tra maiuscole e minuscole. Ad esempio, se il cluster è denominato MyCluster, gli URI seguenti sono validi:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> Gli URI seguenti restituiscono un errore perché nella seconda occorrenza del nome non vengono usate le maiuscole/minuscole corrette.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

La connessione ad Ambari su HDInsight richiede HTTPS. È necessario anche eseguire l'autenticazione ad Ambari utilizzando il nome dell'account admin (il valore predefinito è __admin__,) e la password fornita al momento della creazione del cluster.

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

> [AZURE.NOTE] L'elemento __Host/host\_status/ALERT__ è racchiuso tra virgolette per indicare che '/' fa parte del nome dell'elemento. Per altre informazioni sull'uso di jq, vedere il [sito Web jq](https://stedolan.github.io/jq/).

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
    
> [AZURE.NOTE] Verrà restituita la prima configurazione applicata al server (`service_config_version=1`) che conterrà queste informazioni. Se si recupera un valore che è stato modificato dopo la creazione del cluster, potrebbe essere necessario elencare le versioni della configurazione e recuperare la versione più recente.

Verrà restituito un valore analogo al seguente, dove __CONTAINER__ è il contenitore predefinito e __ACCOUNTNAME__ è il nome dell'account di archiviazione di Azure:

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

È quindi possibile usare queste informazioni con l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) per caricare o scaricare dati dal contenitore.

1. Ottenere il gruppo di risorse per l'account di archiviazione. Sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione recuperato da Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Verrà restituito il nome del gruppo di risorse per l'account.
    
    > [AZURE.NOTE] Se il comando non restituisce risultati, può essere necessario modificare l'interfaccia della riga di comando di Azure in modalità Gestione risorse di Azure ed eseguire nuovamente il comando. Per passare alla modalità Gestione risorse di Azure, usare il comando seguente.
    >
    > `azure config mode arm`
    
2. Ottenere la chiave per l'account di archiviazione. Sostituire __GROUPNAME__ con il gruppo di risorse del passaggio precedente. Sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Verrà restituita la chiave primaria per l'account.
    
3. Usare il comando di caricamento per archiviare un file nel contenitore:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Sostituire __ACCOUNTNAME__ con il nome dell'account di archiviazione. Sostituire __ACCOUNTKEY__ con la chiave recuperata in precedenza. __FILEPATH__ è il percorso del file da caricare, mentre __BLOBPATH__ è il percorso nel contenitore.

    Ad esempio, se si vuole che il file venga visualizzato in HDInsight in corrispondenza di wasb://example/data/filename.txt,__BLOBPATH__ sarà `example/data/filename.txt`.

##Esempio: Aggiornare la configurazione di Ambari

1. Ottenere la configurazione corrente, archiviata da Ambari come "configurazione desiderata":

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Verrà restituito un documento JSON contenente la configurazione corrente identificata dal valore _tag_ dei componenti installati nel cluster. Ad esempio, di seguito è riportato un estratto dei dati restituiti da un tipo di cluster Spark.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    È necessario copiare il nome del componente, ad esempio __spark\_thrift\_sparkconf__, e il valore __tag__ dall'elenco.
    
2. Recuperare la configurazione del componente e del tag usando il comando seguente. Sostituire __spark-thrift-sparkconf__ e __INITIAL__ con il componente e il tag di cui si vuole recuperare la configurazione.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl recupera il documento JSON, quindi jq viene usato per apportare alcune modifiche per creare un modello che è possibile usare per aggiungere o modificare i valori di configurazione. In particolare, esegue le operazioni seguenti:
    
    * Crea un valore univoco contenente la stringa "version" e la data che viene archiviato in __newtag__
    * Crea un documento radice per la nuova configurazione desiderata
    * Ottiene il contenuto della matrice items e lo aggiunge sotto l'elemento __desired\_config__.
    * Elimina gli elementi __href__, __version__ e __Config__ poiché non sono necessari per l'invio di una nuova configurazione
    * Aggiunge un nuovo elemento __tag__ e ne imposta il valore su __version#################__, dove la parte numerica è basata sulla data corrente. Ogni configurazione deve avere un tag univoco.
    
    Infine i dati vengono salvati nel documento __newconfig.json__. La struttura del documento sarà simile a quella riportata di seguito:
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Aprire il documento __newconfig.json__ e modificare o aggiungere i valori nell'oggetto __properties__. Ad esempio, modificare il valore di __"spark.yarn.am.memory"__ da __"1g"__ a __"3g"__ e aggiungere un nuovo elemento per __"spark.kryoserializer.buffer.max"__ con valore __"256m"__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Al termine delle modifiche, salvare il file.

4. Usare il codice seguente per inviare la configurazione aggiornata ad Ambari..

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Questo comando invia tramite pipe il contenuto del file __newconfig.json__ alla richiesta curl che lo invia al cluster come nuova configurazione desiderata. Verrà restituito un documento JSON. L'elemento __versionTag__ di questo documento deve corrispondere alla versione inviata, mentre l'oggetto __configs__ conterrà le modifiche di configurazione richieste.

###Esempio: Riavviare un componente del servizio

A questo punto, se si osserva l'interfaccia utente di Ambari Web, il servizio Spark richiederà il riavvio per rendere effettiva la nuova configurazione. Usare la procedura seguente per riavviare il servizio.

1. Usare il codice seguente per attivare la modalità di manutenzione del servizio Spark.

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Viene inviato un documento JSON al server, contenuto nell'istruzione `echo`, che attiva la modalità di manutenzione. È possibile verificare che il servizio sia in modalità di manutenzione usando la richiesta seguente.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Viene restituito il valore `"ON"`.

3. Successivamente, usare il codice seguente per disattivare il servizio.

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Viene ricevuta una risposta simile alla seguente:
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    Il valore `href` restituito dall'URI usa l'indirizzo IP interno del nodo del cluster. Per usarlo dall'esterno del cluster, sostituire la parte '10.0.0.18:8080' con il nome FQDN del cluster. Ad esempio, il codice seguente recupera lo stato della richiesta.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Se il valore restituisce `"COMPLETED"`, la richiesta è stata completata.

4. Dopo aver completato la richiesta precedente, usare il codice seguente per avviare il servizio.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Dopo il riavvio, il servizio userà le nuove impostazioni di configurazione.

5. Infine, usare il codice seguente per disattivare la modalità di manutenzione.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##Passaggi successivi

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Alcune funzionalità di Ambari sono disabilitate, perché vengono gestite dal servizio cloud HDInsight, ad esempio, l'aggiunta o la rimozione di host dal cluster o l'aggiunta di nuovi servizi.

<!---HONumber=AcomDC_0427_2016-->