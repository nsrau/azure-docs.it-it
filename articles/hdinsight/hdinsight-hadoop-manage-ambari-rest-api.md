---
title: Monitorare e gestire Hadoop con l'API REST Ambari - Azure HDInsight
description: Informazioni sull'uso di Ambari per monitorare e gestire i cluster Hadoop in Azure HDInsight. In questo documento si apprenderà come usare l'API REST Ambari inclusa nei cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: d15b61c70f1587cdd1c0d76d2e3eab81294674fc
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761139"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gestire i cluster HDInsight mediante l'API REST Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Informazioni sull'uso dell'API REST Apache Ambari per gestire e monitorare i cluster Apache Hadoop in Azure HDInsight.

## <a id="whatis"></a>Che cos'è Apache Ambari?
[Apache Ambari](https://ambari.apache.org) semplifica la gestione e monitoraggio dei cluster Hadoop, fornendo un'interfaccia utente supportato da web facile da usare relativi [le API REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari viene fornito per impostazione predefinita con i cluster HDInsight basati su Linux.

## <a name="prerequisites"></a>Prerequisiti
* **Un cluster Hadoop in HDInsight**. Visualizzare [Introduzione a HDInsight su Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash in Ubuntu in Windows 10**.  Gli esempi in questo articolo usano la shell di Bash in Windows 10. Visualizzare [sottosistema Windows per la Guida all'installazione di Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) per i passaggi di installazione.  Altri [shell Unix](https://www.gnu.org/software/bash/) funzioneranno anche.  Gli esempi, con alcune lievi modifiche, è possono lavorare in un prompt dei comandi di Windows.  In alternativa, è possibile usare Windows PowerShell.

* **jq**, un processore JSON della riga di comando.  Vedere [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  In alternativa, è possibile usare [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>URI di base per l'API REST Ambari

 È l'URI di base per l'API REST Ambari in HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, dove `CLUSTERNAME` è il nome del cluster.  I nomi dei cluster negli URI vengono **distinzione maiuscole/minuscole**.  A differenza delle altre occorrenze nell'URI, nel nome del cluster nella parte del nome di dominio completo (FQDN) dell'URI (CLUSTERNAME.azurehdinsight.net) non viene fatta distinzione tra maiuscole e minuscole.

## <a name="authentication"></a>Authentication

La connessione ad Ambari su HDInsight richiede HTTPS. Usare il nome dell'account amministratore (il valore predefinito è **admin**) e la password forniti durante la creazione del cluster.

## <a name="examples"></a>Esempi

### <a name="setup-preserve-credentials"></a>Programma di installazione (Preserve credenziali)
Mantenere le credenziali per evitare la reentrancy li per ogni esempio.  Il nome del cluster verrà mantenuto in un passaggio separato.

**A. Bash**  
Modificare lo script seguente sostituendo `PASSWORD` con la password corrente.  Immettere quindi il comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificare il nome del cluster in modo corretto maiuscole e minuscole
Le effettive maiuscole e minuscole del nome del cluster possono essere diversa dal previsto a seconda del modo in cui è stato creato il cluster.  I passaggi indicati qui verranno visualizzare le maiuscole e minuscole effettiva e quindi archiviarla in una variabile per tutti gli esempi successivi.

Modificare gli script seguenti per sostituire `CLUSTERNAME` con il nome del cluster. Immettere quindi il comando. (Il nome del cluster per il nome FQDN non distinzione maiuscole/minuscole).

**A. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Analisi dei dati JSON

L'esempio seguente usa [jq](https://stedolan.github.io/jq/) oppure [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) analizzare il documento di risposta JSON e visualizzare solo il `health_report` informazioni dai risultati.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Ottenere il nome di dominio completo dei nodi del cluster

Quando si usa HDInsight, potrebbe essere necessario conoscere il nome di dominio completo di un nodo del cluster. È possibile recuperare con facilità l'FQDN per i diversi nodi del cluster usando gli esempi seguenti:

**Tutti i nodi**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Nodi head**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nodi di lavoro**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nodi Zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Ottenere l'indirizzo IP interno dei nodi del cluster

Gli indirizzi IP restituiti dagli esempi in questa sezione non sono direttamente accessibili tramite Internet. Sono accessibili solo all'interno della rete virtuale di Azure che contiene il cluster HDInsight.

Per altre informazioni sull'uso di HDInsight e delle reti virtuali vedere [Estendere le funzionalità di HDInsight usando una rete virtuale di Azure personalizzata](hdinsight-extend-hadoop-virtual-network.md).

Per trovare l'indirizzo IP, è necessario conoscere il nome di dominio completo (FQDN) interno dei nodi del cluster. Quando è stato ottenuto l'FQDN è possibile ottenere l'indirizzo IP dell'host. Negli esempi seguenti per prima cosa viene effettuata una query in Ambari per ottenere l'FQDN di tutti i nodi host, poi viene effettuata un'altra query in Ambari per ottenere l'indirizzo IP di ogni host.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Ottenere l'archiviazione predefinita

Quando si crea un cluster HDInsight è necessario usare un account di Archiviazione di Azure oppure Data Lake Storage come risorsa di archiviazione predefinita per il cluster. È possibile usare Ambari per recuperare queste informazioni dopo la creazione del cluster, ad esempio se si desidera leggere o scrivere dati nel contenitore all'esterno di HDInsight.

Negli esempi seguenti viene recuperata la configurazione di archiviazione predefinita dal cluster:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Questi esempi restituiscono la prima configurazione applicata al server (`service_config_version=1`) che contiene queste informazioni. Se si recupera un valore che è stato modificato dopo la creazione del cluster, potrebbe essere necessario elencare le versioni della configurazione e recuperare la versione più recente.

Il valore restituito è simile a uno degli esempi seguenti:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - Questo valore indica che il cluster usa un account di archiviazione di Azure come risorsa di archiviazione predefinita. Il valore `ACCOUNTNAME` è il nome dell'account di archiviazione. La porzione `CONTAINER` corrisponde al nome del contenitore BLOB nell'account di archiviazione. Il contenitore è la radice della risorsa di archiviazione compatibile con HDFS per il cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - Questo valore indica che il cluster usa Azure Data Lake Storage Gen2 come risorsa di archiviazione predefinita. I valori `ACCOUNTNAME` e `CONTAINER` hanno gli stessi significati descritti per Archiviazione di Azure in precedenza.

* `adl://home` - Questo valore indica che il cluster usa Azure Data Lake Storage Gen1 come risorsa di archiviazione predefinita.

    Per trovare il nome dell'account Data Lake Storage, usare gli esempi seguenti:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Il valore restituito è simile a `ACCOUNTNAME.azuredatalakestore.net`, dove `ACCOUNTNAME` è il nome dell'account Data Lake Storage.

    Per trovare la directory all'interno di Data Lake Storage contenente la risorsa di archiviazione per il cluster, usare gli esempi seguenti:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Il valore restituito è simile a `/clusters/CLUSTERNAME/`. Questo valore è un percorso all'interno dell'account Data Lake Storage. Questo percorso è la radice del file system compatibile con HDFS per il cluster.  

> [!NOTE]  
> Il [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet fornito dal [Azure PowerShell](/powershell/azure/overview) restituisce anche le informazioni sull'archiviazione per il cluster.


### <a name="get-all-configurations"></a> Ottenere tutte le configurazioni

Consente di ottenere le configurazioni disponibili per il cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

L'esempio restituisce un documento JSON contenente la configurazione corrente, identificata dal valore *tag*, per i componenti installati nel cluster. L'esempio di seguito rappresenta un estratto dei dati restituiti da un tipo di cluster Spark.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Ottenere la configurazione per il componente specifico

Consente di ottenere la configurazione del componente a cui si è interessati. Nell'esempio seguente, sostituire `INITIAL` con il valore del tag restituito dalla richiesta precedente.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Questo esempio restituisce un documento JSON che contiene la configurazione corrente del componente `livy2-conf`.

### <a name="update-configuration"></a>Aggiornare la configurazione

1. Creare `newconfig.json`.  
   Modificare e quindi immettere i comandi seguenti:

   * Sostituire `livy2-conf` con il componente desiderato.
   * Sostituire `INITIAL` con il valore effettivo per recuperare `tag` dalla [ottenere tutte le configurazioni](#get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **Powershell b.**  
     Lo script di PowerShell Usa [jq](https://stedolan.github.io/jq/).  Modificare `C:\HD\jq\jq-win64` riportato di seguito in modo da riflettere il percorso effettivo e la versione dei [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
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
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Modifica `newconfig.json`.  
   Aprire il documento `newconfig.json` e modificare o aggiungere i valori nell'oggetto `properties`. L'esempio seguente modifica il valore di `"livy.server.csrf_protection.enabled"` da `"true"` a `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Al termine delle modifiche, salvare il file.

3. Inviare `newconfig.json`.  
   Usare i comandi seguenti per inviare la configurazione aggiornata ad Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Questi comandi inviano il contenuto del file **newconfig.json** al cluster come nuova configurazione desiderata. La richiesta restituisce un documento JSON. L'elemento **versionTag** di questo documento deve corrispondere alla versione inviata, mentre l'oggetto **configs** conterrà le modifiche di configurazione richieste.

### <a name="restart-a-service-component"></a>Riavviare un componente del servizio

A questo punto, se si osserva l'interfaccia utente di Ambari Web, il servizio Spark indicherà che è necessario il riavvio per rendere effettiva la nuova configurazione. Usare la procedura seguente per riavviare il servizio.

1. Per abilitare la modalità di manutenzione per il servizio Spark2, usare quanto segue:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Verificare la modalità di manutenzione  

    Questi comandi inviano un documento JSON al server che attiva la modalità di manutenzione. È possibile verificare che il servizio sia in modalità di manutenzione usando la richiesta seguente:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Viene restituito il valore `ON`.

3. Per disattivare il servizio Spark2 successivamente, usare quanto segue:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
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

4. Verificare di richiesta.  
    Modificare il comando seguente sostituendo `29` con il valore effettivo per `id` restituiti nel passaggio precedente.  Il comando seguente recupera lo stato della richiesta:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    La risposta `COMPLETED` indica che la richiesta è stata completata.

5. Dopo aver completato la richiesta precedente, usare quanto segue per avviare il servizio Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    Il servizio ora usa la nuova configurazione.

6. Infine, usare il codice seguente per disattivare la modalità di manutenzione.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Apache Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

