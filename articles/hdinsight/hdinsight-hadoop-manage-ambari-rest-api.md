---
title: Monitorare e gestire i cluster HDInsight mediante l&quot;API REST Apache Ambari | Documentazione Microsoft
description: "Informazioni sull&quot;uso di Ambari per monitorare e gestire cluster HDInsight basati su Linux. In questo documento si apprenderà come usare l&quot;API REST Ambari inclusa nei cluster HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 11523a95a8eac687d391c23cc1e29fa0684a40ae
ms.openlocfilehash: 2723716eb1ec823809d34ce1698d25a0ed9d012b


---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gestire i cluster HDInsight mediante l'API REST Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari semplifica la gestione e il monitoraggio di un cluster Hadoop grazie a un'interfaccia utente Web facile da usare e alle API REST. Ambari è incluso nei cluster HDInsight basati su Linux e viene usato per monitorare il cluster e modificare la configurazione. Questo documento consente di apprendere le nozioni fondamentali sull'utilizzo dell'API REST Ambari attraverso l'esecuzione di attività comuni mediante cURL.

> [!IMPORTANT]
> Gli esempi inclusi in questo documento sono stati testati con PowerShell in Windows 10 e Bash. In molti casi, lo stesso comando funziona in entrambi i sistemi. Nei casi in cui è presente una differenza, viene indicato un esempio sia per PowerShell sia per Bash.

## <a name="prerequisites"></a>Prerequisiti

* [cURL](http://curl.haxx.se/): cURL è un'utilità multipiattaforma che consente di utilizzare le API REST dalla riga di comando. In questo documento viene usata per comunicare con l'API REST Ambari.

    > [!WARNING]
    > Se si usa PowerShell, è necessario rimuovere l'alias predefinito per il comando `curl` tramite il comando `remove-item alias:curl`. Se non si rimuove l'alias, si riceveranno messaggi di errore analoghi a "Impossibile elaborare il parametro perché il nome 'u' è ambiguo".

* [jq](https://stedolan.github.io/jq/): jq è un'utilità da riga di comando multipiattaforma per l'utilizzo di documenti JSON. In questo documento viene usata per analizzare i documenti JSON restituiti dall'API REST Ambari.

* [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (anteprima): è un'utilità da riga di comando multipiattaforma per l'uso dei servizi di Azure.
  
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## <a name="a-idwhatisawhat-is-ambari"></a><a id="whatis"></a>Cos'è Ambari?

[Apache Ambari](http://ambari.apache.org) semplifica la gestione di Hadoop grazie a un'interfaccia utente Web intuitiva che può essere usata per effettuare il provisioning, la gestione e il monitoraggio dei cluster Hadoop. Gli sviluppatori possono integrare queste funzionalità nelle applicazioni usando le [API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari viene fornito per impostazione predefinita con i cluster HDInsight basati su Linux.

## <a name="rest-api"></a>API REST

L'URI di base per l'API REST Ambari in HDInsight è https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, dove **CLUSTERNAME** è il nome del cluster in uso.

> [!IMPORTANT]
> A differenza delle altre occorrenze nell'URI, nel nome del cluster nella parte del nome di dominio completo (FQDN) dell'URI (CLUSTERNAME.azurehdinsight.net) non viene fatta distinzione tra maiuscole e minuscole. Ad esempio, se il cluster è denominato MyCluster, gli URI seguenti sono validi:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Gli URI seguenti restituiscono un errore perché nella seconda occorrenza del nome non vengono usate le maiuscole/minuscole corrette.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

La connessione ad Ambari su HDInsight richiede HTTPS. Quando si esegue l'autenticazione della connessione, è necessario usare il nome dell'account amministratore (il valore predefinito è **admin**) e la password fornita al momento della creazione del cluster.

L'esempio seguente fa uso di cURL per effettuare una richiesta GET nell'API REST. Sostituire **PASSWORD** con la password di amministratore per il cluster. Sostituire **CLUSTERNAME** con il nome del cluster:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"

La risposta è un documento JSON che inizia con informazioni simili a quelle seguenti:

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

Trattandosi di JSON, è più semplice usare un parser JSON per utilizzare i dati. Ad esempio, nell'esempio seguente si utilizza jq per visualizzare soltanto l'elemento `health_report` .

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Esempio: Ottenere il nome di dominio completo dei nodi del cluster

Quando si usa HDInsight, potrebbe essere necessario conoscere il nome di dominio completo di un nodo del cluster. È possibile recuperare con facilità il nome di dominio completo per i diversi nodi del cluster mediante il codice seguente:

* **Nodi head**: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`

* **Nodi di lavoro**: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`

* **Nodi Zookeeper**: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Si noti che ognuno di questi esempi segue lo stesso schema:

1. Eseguire la query di un componente noto per essere eseguito su tali nodi.

2. Recuperare gli elementi `host_name` , che contengono il nome di dominio completo per questi nodi.

L'elemento `host_components` del documento restituito contiene più elementi. Se si usa `.host_components[]`e quindi si specifica un percorso entro l'elemento, si creerà un ciclo in ogni elemento e verrà recuperato il valore dal percorso specifico. Se si vuole solo un valore, ad esempio la prima voce relativa al nome di dominio completo, è possibile restituire gli elementi come una raccolta e quindi selezionare una voce specifica:

    jq '[.host_components[].HostRoles.host_name][0]'

Viene restituito il primo nome di dominio completo dalla raccolta.

## <a name="example-get-the-default-storage-account-and-container"></a>Esempio: Ottenere l'account di archiviazione e il contenitore predefiniti

Quando si crea un cluster HDInsight, è necessario usare un account di archiviazione di Azure e un contenitore BLOB come risorsa di archiviazione predefinita per il cluster. È possibile usare Ambari per recuperare queste informazioni dopo la creazione del cluster, ad esempio, se si vuole scrivere dati a livello di codice direttamente nel contenitore.

Il codice seguente recupererà l'URI WASB della risorsa di archiviazione predefinita dei cluster:

```bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

Le regole di PowerShell per l'uso delle virgolette singole e doppie sono leggermente diverse. Usare il comando seguente in PowerShell:

```PowerShell
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["""fs.defaultFS"""] | select(. != null)'
```

> [!NOTE]
> Viene restituita la prima configurazione applicata al server (`service_config_version=1`) che contiene queste informazioni. Se si recupera un valore che è stato modificato dopo la creazione del cluster, potrebbe essere necessario elencare le versioni della configurazione e recuperare la versione più recente.

Viene restituito un valore analogo al seguente esempio, dove **CONTAINER** è il contenitore predefinito e **ACCOUNTNAME** è il nome dell'account di archiviazione di Azure:

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

È quindi possibile usare queste informazioni con l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) per caricare o scaricare dati dal contenitore.

1. Usare il comando seguente per ottenere l'ID univoco per l'account di archiviazione. Nel comando seguente sostituire **ACCOUNTNAME** con il nome dell'account di archiviazione recuperato da Ambari:

        az storage account list --query "[?name=='ACCOUNTNAME'].id" --out list

2. Usare il comando seguente per ottenere una chiave per l'account di archiviazione. Sostituire **STORAGEID** con l'ID account di archiviazione:

        az storage account keys list --ids STORAGEID --query "keys[?keyName=='key1'].value" --out list

    Verrà restituita la chiave primaria per l'account.

3. Usare il comando di caricamento per archiviare un file nel contenitore:
   
        az storage blob upload --account-name ACOUNTNAME --account-key ACCOUNTKEY -f FILEPATH -c CONTAINER -n BLOBPATH
   
    Sostituire **ACCOUNTNAME** con il nome dell'account di archiviazione. Sostituire **ACCOUNTKEY** con la chiave recuperata in precedenza. Sostituire __CONTAINER__ con il nome del contenitore. **FILEPATH** è il percorso del file da caricare, mentre **BLOBPATH** è il percorso nel contenitore.
   
    Ad esempio, per visualizzare il file in HDInsight in wasbs://example/data/filename.txt, **BLOBPATH** sarà `example/data/filename.txt`.

## <a name="example-update-ambari-configuration"></a>Esempio: Aggiornare la configurazione di Ambari

1. Ottenere la configurazione corrente, archiviata da Ambari come "configurazione desiderata":
   
        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
   
    L'esempio restituisce un documento JSON contenente la configurazione corrente, identificata dal valore *tag*, per i componenti installati nel cluster. L'esempio di seguito rappresenta un estratto dei dati restituiti da un tipo di cluster Spark.
   
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
   
    È necessario copiare il nome del componente, ad esempio **spark\_thrift\_sparkconf**, e il valore **tag** dall'elenco.

2. Recuperare la configurazione del componente e del tag usando il comando seguente. Sostituire **spark-thrift-sparkconf** e **INITIAL** con il componente e il tag di cui si vuole recuperare la configurazione.
   
    ```bash
    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```PowerShell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```
   
    CURL recupera il documento JSON, quindi jq viene usato per apportare modifiche ai dati al fine di creare un modello. Il modello viene quindi utilizzato per aggiungere o modificare i valori di configurazione. In particolare, esegue le operazioni seguenti:
   
    * Crea un valore univoco contenente la stringa "version" e la data, che viene archiviato in **newtag**.

    * Crea un documento radice per la nuova configurazione desiderata.

    * Ottiene i contenuti della matrice `.items[]` e li aggiunge sotto l'elemento **desired_config**.

    * Elimina gli elementi **href**, **version** e **Config** perché non sono necessari per l'invio di una nuova configurazione.

    * Aggiunge un nuovo elemento **tag** e ne imposta il valore su **version#################**. La parte numerica si basa sulla data corrente. Ogni configurazione deve avere un tag univoco.
     
        Infine i dati vengono salvati nel documento **newconfig.json**. La struttura del documento deve essere simile all'esempio di seguito:
     
     ```json
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
    ```

3. Aprire il documento **newconfig.json** e modificare o aggiungere i valori nell'oggetto **properties**. L'esempio seguente modifica il valore di **"spark.yarn.am.memory"** da **"1g"** in **"3g"** e aggiunge un nuovo elemento per **"spark.kryoserializer.buffer.max"** con valore **"256m"**.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Al termine delle modifiche, salvare il file.
4. Usare il comando seguente per inviare la configurazione aggiornata ad Ambari.
   
        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
   
    Questo comando invia tramite pipe il contenuto del file **newconfig.json** alla richiesta curl che lo invia al cluster come nuova configurazione desiderata. La richiesta cURL restituisce un documento JSON. L'elemento **versionTag** di questo documento deve corrispondere alla versione inviata, mentre l'oggetto **configs** conterrà le modifiche di configurazione richieste.

### <a name="example-restart-a-service-component"></a>Esempio: Riavviare un componente del servizio

A questo punto, se si osserva l'interfaccia utente di Ambari Web, il servizio Spark richiederà il riavvio per rendere effettiva la nuova configurazione. Usare la procedura seguente per riavviare il servizio.

1. Usare il codice seguente per attivare la modalità di manutenzione del servizio Spark:
   
        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
   
    Questo comando invia un documento JSON al server, contenuto nell'istruzione `echo` , che attiva la modalità di manutenzione.
    È possibile verificare che il servizio sia in modalità di manutenzione usando la richiesta seguente:
   
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
   
    Viene restituito il valore `"ON"`.

2. Successivamente, usare il codice seguente per disattivare il servizio:
    
    ```
    echo '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
    ```
    
    Questo comando restituisce una risposta simile a quella seguente.
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    Il valore `href` restituito dall'URI usa l'indirizzo IP interno del nodo del cluster. Per usarlo dall'esterno del cluster, sostituire la parte '10.0.0.18:8080' con il nome FQDN del cluster. Ad esempio, il comando seguente recupera lo stato della richiesta.
    
    ```
    curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    ```
    
    Se restituisce un valore di `"COMPLETED"` , allora la richiesta è stata completata.

3. Dopo aver completato la richiesta precedente, usare il codice seguente per avviare il servizio.
   
        echo '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
   
    Dopo il riavvio del servizio, le nuove impostazioni di configurazione diventano effettive.

4. Infine, usare il codice seguente per disattivare la modalità di manutenzione.
   
        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

## <a name="next-steps"></a>Passaggi successivi

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).




<!--HONumber=Dec16_HO1-->


