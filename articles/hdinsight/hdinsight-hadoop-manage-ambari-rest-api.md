---
title: Monitorare e gestire Azure HDInsight con l&quot;API REST Ambari | Documentazione Microsoft
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
ms.date: 02/23/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2e26bd81c59fd53a0e8fc693dde30cb403995896
ms.openlocfilehash: 38d37e45c34c8c0a3bd2ed94f72944208292f466
ms.lasthandoff: 02/24/2017


---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gestire i cluster HDInsight mediante l'API REST Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari semplifica la gestione e il monitoraggio di un cluster Hadoop grazie a un'interfaccia utente Web facile da usare e alle API REST. Ambari è incluso nei cluster HDInsight che usano i sistemi operativi Linux e viene usato per monitorare il cluster e modificare la configurazione. Questo documento consente di apprendere le nozioni di base sull'uso dell'API REST Ambari.

## <a id="whatis"></a>Informazioni su Ambari

[Apache Ambari](http://ambari.apache.org) semplifica la gestione di Hadoop grazie a un'interfaccia utente Web intuitiva che può essere usata per effettuare il provisioning, la gestione e il monitoraggio dei cluster Hadoop. Gli sviluppatori possono integrare queste funzionalità nelle applicazioni usando le [API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari viene fornito per impostazione predefinita con i cluster HDInsight basati su Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Come usare l'API REST Ambari

> [!IMPORTANT]
> Le informazioni e gli esempi descritti in questo documento richiedono un cluster HDInsight che usa il sistema operativo Linux. Per altre informazioni, vedere [Guida introduttiva di HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

In questo documento vengono descritti gli esempi per Bourne shell (bash) e PowerShell. Gli esempi bash sono stati testati con GNU bash 4.3.11 ma dovrebbero funzionare anche con altre shell Unix. Gli esempi PowerShell sono stati testati con PowerShell 5.0 ma dovrebbero funzionare anche con PowerShell 3.0 o versione successiva.

Se si usa __Bourne shell__ (bash) è necessario disporre di:

* [cURL](http://curl.haxx.se/): cURL è un'utilità che può essere usata per lavorare con le API REST dalla riga di comando. In questo documento viene usata per comunicare con l'API REST Ambari.

Sia con bash che con PowerShell è necessario che sia installato anche [jq](https://stedolan.github.io/jq/). Jq è un'utilità per lavorare con documenti JSON. Viene usata in **tutti** gli esempi bash e in **uno** degli esempi PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>URI di base per l'API REST Ambari

L'URI di base per l'API REST Ambari in HDInsight è https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, dove **CLUSTERNAME** è il nome del cluster in uso.

> [!IMPORTANT]
> A differenza delle altre occorrenze nell'URI, nel nome del cluster nella parte del nome di dominio completo (FQDN) dell'URI (CLUSTERNAME.azurehdinsight.net) non viene fatta distinzione tra maiuscole e minuscole. Ad esempio, se il cluster è denominato `MyCluster`, son validi gli URI seguenti:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Gli URI seguenti restituiscono un errore perché nella seconda occorrenza del nome non vengono usate le maiuscole/minuscole corrette.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Autenticazione

La connessione ad Ambari su HDInsight richiede HTTPS. Quando si esegue l'autenticazione della connessione, è necessario usare il nome dell'account amministratore (il valore predefinito è **admin**) e la password fornita al momento della creazione del cluster.

## <a name="examples-authentication-and-parsing-json"></a>Esempi: autenticazione e analisi di JSON

Gli esempi seguenti illustrano come effettuare una richiesta GET all'API REST Ambari di base:

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Gli esempi bash in questo documento partono dai presupposti seguenti:
>
> * Il nome di accesso per il cluster è il valore predefinito di `admin`.
> * `$PASSWORD` contiene la password per il comando di accesso di HDInsight. È possibile impostare questo valore usando `PASSWORD='mypassword'`.
> * `$CLUSTERNAME` contiene il nome del cluster. È possibile impostare questo valore usando `set CLUSTERNAME='clustername'`

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Gli esempi PowerShell in questo documento partono dai presupposti seguenti:
>
> * `$creds` è un oggetto credenziale che contiene la password e l'accesso dell'amministratore per il cluster. È possibile impostare questo valore usando `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` e specificando le credenziali quando richiesto.
> * `$clusterName` è una stringa che contiene il nome del cluster. È possibile impostare questo valore usando `$clusterName="clustername"`.

Entrambi gli esempi restituiscono un documento JSON che inizia con informazioni simili a quelle nell'esempio seguente:

```json
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
    ...
```

### <a name="parsing-json-data"></a>Analisi dei dati JSON

L'esempio seguente usa `jq` per analizzare il documento di risposta JSON e visualizzare solo le informazioni `health_report` dai risultati.

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 e versioni successive mette a disposizione il cmdlet `ConvertFrom-Json`, che converte il documento JSON in un oggetto più facile da usare da PowerShell. L'esempio seguente usa `ConvertFrom-Json` per visualizzare solo le informazioni `health_report` dai risultati.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Anche se nella maggior parte degli esempi in questo documento viene usato `ConvertFrom-Json` per visualizzare gli elementi del documento di risposta, l'esempio sull'[aggiornamento della configurazione di Ambari](#example-update-ambari-configuration) usa jq. Jq viene usato in questo esempio per creare un nuovo modello dal documento di risposta JSON.

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Esempio: Ottenere il nome di dominio completo dei nodi del cluster

Quando si usa HDInsight, potrebbe essere necessario conoscere il nome di dominio completo di un nodo del cluster. È possibile recuperare con facilità l'FQDN per i diversi nodi del cluster usando gli esempi seguenti:

* **Tutti i nodi**

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **Nodi head**

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nodi di lavoro**

    ```bash
    curl -u admin:PASSWORD -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nodi Zookeeper**

    ```bash
    curl -u admin:PASSWORD -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Esempio: ottenere l'indirizzo IP interno dei nodi del cluster

> [!IMPORTANT]
> Gli indirizzi IP restituiti dagli esempi in questa sezione non sono direttamente accessibili tramite Internet. Sono accessibili solo all'interno della rete virtuale di Azure che contiene il cluster HDInsight.
>
> Per altre informazioni sull'uso di HDInsight e delle reti virtuali vedere [Estendere le funzionalità di HDInsight usando una rete virtuale di Azure personalizzata](hdinsight-extend-hadoop-virtual-network.md).

Per poter ottenere l'indirizzo IP è necessario conoscere l'FQDN per l'host. Quando è stato ottenuto l'FQDN è possibile ottenere l'indirizzo IP dell'host. Negli esempi seguenti per prima cosa viene effettuata una query in Ambari per ottenere l'FQDN di tutti i nodi host, poi viene effettuata un'altra query in Ambari per ottenere l'indirizzo IP di ogni host.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Esempio: ottenere la risorsa di archiviazione predefinita

Quando si crea un cluster HDInsight è necessario usare un account di archiviazione di Azure oppure Data Lake Store come risorsa di archiviazione predefinita per il cluster. È possibile usare Ambari per recuperare queste informazioni dopo la creazione del cluster, ad esempio se si desidera leggere o scrivere dati nel contenitore all'esterno di HDInsight.

Negli esempi seguenti viene recuperata la configurazione di archiviazione predefinita dal cluster:

```bash
curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Questi esempi restituiscono la prima configurazione applicata al server (`service_config_version=1`) che contiene queste informazioni. Se si recupera un valore che è stato modificato dopo la creazione del cluster, potrebbe essere necessario elencare le versioni della configurazione e recuperare la versione più recente.

Il valore restituito è simile a uno degli esempi seguenti:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - Questo valore indica che il cluster usa un account di archiviazione di Azure come risorsa di archiviazione predefinita. Il valore `ACCOUNTNAME` è il nome dell'account di archiviazione. La porzione `CONTAINER` corrisponde al nome del contenitore BLOB nell'account di archiviazione. Il contenitore è la radice della risorsa di archiviazione compatibile con HDFS per il cluster.

* `adl://home` - Questo valore indica che il cluster usa Azure Data Lake Store come risorsa di archiviazione predefinita.

    Per trovare il nome dell'account Data Lake Store, usare gli esempi seguenti:

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Il valore restituito è simile a `ACCOUNTNAME.azuredatalakestore.net`, dove `ACCOUNTNAME` è il nome dell'account Data Lake Store.

    Per trovare la directory all'interno di Data Lake Store contenente la risorsa di archiviazione per il cluster, usare gli esempi seguenti:

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Il valore restituito è simile a `/clusters/CLUSTERNAME/`. Questo valore è un percorso all'interno dell'account Data Lake Store. Questo percorso è la radice del file system compatibile con HDFS per il cluster. 

> [!NOTE]
> Il cmdlet `Get-AzureRmHDInsightCluster` messo a disposizione da [Azure PowerShell](https://docs.microsoft.com/powershell/) restituisce anche le informazioni di archiviazione per il cluster.


## <a name="example-get-configuration"></a>Esempio: ottenere una configurazione

1. Consente di ottenere le configurazioni disponibili per il cluster.

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    L'esempio restituisce un documento JSON contenente la configurazione corrente, identificata dal valore *tag*, per i componenti installati nel cluster. L'esempio di seguito rappresenta un estratto dei dati restituiti da un tipo di cluster Spark.
   
   ```json
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
   ```

2. Consente di ottenere la configurazione del componente a cui si è interessati. Nell'esempio seguente, sostituire `INITIAL` con il valore del tag restituito dalla richiesta precedente.

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Questo esempio restituisce un documento JSON che contiene la configurazione corrente del componente `core-site`.

## <a name="example-update-configuration"></a>Esempio: aggiornare la configurazione

1. Ottenere la configurazione corrente, archiviata da Ambari come "configurazione desiderata":

    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    L'esempio restituisce un documento JSON contenente la configurazione corrente, identificata dal valore *tag*, per i componenti installati nel cluster. L'esempio di seguito rappresenta un estratto dei dati restituiti da un tipo di cluster Spark.
   
    ```json
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
    ```
   
    È necessario copiare il nome del componente, ad esempio **spark\_thrift\_sparkconf**, e il valore **tag** dall'elenco.

2. Recuperare la configurazione del componente e del tag usando i comandi seguenti. Sostituire **spark-thrift-sparkconf** e **INITIAL** con il componente e il tag di cui si vuole recuperare la configurazione.
   
    ```bash
    curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```
   
    Jq viene usato per trasformare i dati recuperati da HDInsight in un nuovo modello di configurazione. In particolare, questi esempi eseguono le azioni seguenti:
   
    * Crea un valore univoco contenente la stringa "version" e la data, che viene archiviato in `newtag`.

    * Crea un documento radice per la nuova configurazione desiderata.

    * Ottiene i contenuti della matrice `.items[]` e li aggiunge sotto l'elemento **desired_config**.

    * Elimina gli elementi `href`, `version` e `Config` perché non sono necessari per l'invio di una nuova configurazione.

    * Aggiunge un elemento `tag` con un valore di `version#################`. La parte numerica si basa sulla data corrente. Ogni configurazione deve avere un tag univoco.
     
    Infine i dati vengono salvati nel documento `newconfig.json`. La struttura del documento deve essere simile all'esempio di seguito:
     
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

3. Aprire il documento `newconfig.json` e modificare o aggiungere i valori nell'oggetto `properties`. L'esempio seguente modifica il valore di `"spark.yarn.am.memory"` da `"1g"` a `"3g"`. Aggiunge anche `"spark.kryoserializer.buffer.max"` con un valore di `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Al termine delle modifiche, salvare il file.

4. Usare i comandi seguenti per inviare la configurazione aggiornata ad Ambari.
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Questi comandi inviano il contenuto del file **newconfig.json** al cluster come nuova configurazione desiderata. La richiesta restituisce un documento JSON. L'elemento **versionTag** di questo documento deve corrispondere alla versione inviata, mentre l'oggetto **configs** conterrà le modifiche di configurazione richieste.

### <a name="example-restart-a-service-component"></a>Esempio: Riavviare un componente del servizio

A questo punto, se si osserva l'interfaccia utente di Ambari Web, il servizio Spark indicherà che è necessario il riavvio per rendere effettiva la nuova configurazione. Usare la procedura seguente per riavviare il servizio.

1. Usare il codice seguente per attivare la modalità di manutenzione del servizio Spark:

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Questi comandi inviano un documento JSON al server che attiva la modalità di manutenzione. È possibile verificare che il servizio sia in modalità di manutenzione usando la richiesta seguente:
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    Viene restituito il valore `ON`.

2. Successivamente usare il codice seguente per disattivare il servizio:

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    La risposta restituita è simile all'esempio seguente:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > Il valore `href` restituito dall'URI usa l'indirizzo IP interno del nodo del cluster. Per usarlo dall'esterno del cluster, sostituire la parte '10.0.0.18:8080' con il nome FQDN del cluster. 
    
    Il comando seguente recupera lo stato della richiesta:

    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    La risposta `COMPLETED` indica che la richiesta è stata completata.

3. Dopo aver completato la richiesta precedente, usare il codice seguente per avviare il servizio.
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    Il servizio ora usa la nuova configurazione.

4. Infine, usare il codice seguente per disattivare la modalità di manutenzione.
   
    ```bash
    curl -u admin:$PASSWORD -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).


