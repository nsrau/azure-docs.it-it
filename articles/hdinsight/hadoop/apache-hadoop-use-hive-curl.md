---
title: Usare Hive di Apache Hadoop con Curl in HDInsight - Azure
description: Informazioni su come inviare in modalità remota i processi Apache Pig ad Azure HDInsight usando curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: e1fbeb48acdfd9d09cad2616aed9793e2ff513ad
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736095"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Eseguire query Apache Hive con Apache Hadoop in HDInsight tramite REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come usare l'API REST WebHCat per eseguire query Apache Hive con Apache Hadoop nel cluster Azure HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Un client REST. Questo documento usa [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) in Windows PowerShell e [curl](https://curl.haxx.se/) su [bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Se si usa bash, sarà necessario anche JQ, un processore JSON da riga di comando.  Vedere [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>URI di base per l'API REST

Il Uniform Resource Identifier di base (URI) per l'API REST in HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`è, `CLUSTERNAME` dove è il nome del cluster.  I nomi dei cluster negli URI fanno **distinzione tra maiuscole e**minuscole.  Mentre il nome del cluster nella parte relativa al nome di dominio completo (FQDN) dell'URI`CLUSTERNAME.azurehdinsight.net`() non fa distinzione tra maiuscole e minuscole, altre occorrenze nell'URI fanno distinzione tra maiuscole e minuscole.

## <a name="authentication"></a>Authentication

Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight. L'API REST viene protetta tramite l' [autenticazione di base](https://en.wikipedia.org/wiki/Basic_access_authentication). Per essere certi che le credenziali vengano inviate in modo sicuro al server, eseguire sempre le richieste usando il protocollo Secure HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Impostazione (Mantieni credenziali)
Mantenere le credenziali per evitare di immetterle nuovamente per ciascun esempio.  Il nome del cluster verrà mantenuto in un passaggio separato.

**A. Bash**  
Modificare lo script seguente sostituendo `PASSWORD` con la password effettiva.  Immettere quindi il comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell** eseguire il codice seguente e immettere le credenziali nella finestra popup:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identificare il nome corretto del cluster con maiuscole/minuscole
L'uso effettivo di maiuscole e minuscole nel nome del cluster può differire dal previsto, a seconda della modalità di creazione del cluster.  I passaggi qui illustrano la combinazione di maiuscole e minuscole e quindi lo archiviano in una variabile per tutti gli esempi successivi.

Modificare gli script riportati di seguito `CLUSTERNAME` per sostituire con il nome del cluster. Immettere quindi il comando. (Il nome del cluster per il nome di dominio completo non fa distinzione tra maiuscole e minuscole).

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

## <a id="curl"></a>Eseguire una query Hive

1. Per verificare che sia possibile connettersi al cluster HDInsight, usare uno dei comandi seguenti:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Si riceverà un messaggio simile al testo seguente:

    ```json
    {"status":"ok","version":"v1"}
    ```

    I parametri usati in questo comando sono i seguenti:

    * `-u`: il nome utente e la password usati per autenticare la richiesta.
    * `-G`: indica che è un'operazione GET.

1. La parte iniziale dell'URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, è uguale per tutte le richieste. Il percorso, `/status`, indica che la richiesta deve restituire uno stato di WebHCat (noto anche come Templeton) per il server. È inoltre possibile richiedere la versione di Hive usando il seguente comando:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Questa richiesta restituisce una risposta simile al testo seguente:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Usare quanto segue per creare una tabella denominata **log4jLogs**:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Questa richiesta usa il metodo POST, che invia dati come parte della richiesta all'API REST. I valori di dati seguenti vengono inviati insieme alla richiesta:

     * `user.name`: l'utente che esegue il comando.
     * `execute`: le istruzioni HiveQL da eseguire.
     * `statusdir`: la directory in cui è scritto lo stato del processo.

   Le istruzioni eseguono queste azioni:

   * `DROP TABLE`: se la tabella esiste già, viene eliminata.
   * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

     > [!NOTE]  
     > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo di caricamento dati automatizzato o un'altra operazione MapReduce.
     >
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

   * `ROW FORMAT`: indica il modo in cui sono formattati i dati. I campi in ogni log sono separati da uno spazio.
   * `STORED AS TEXTFILE LOCATION`: indica la posizione in cui sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
   * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna **t4** include il valore **[ERROR]** . L'istruzione dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.

     > [!NOTE]  
     > Si noti che gli spazi tra le istruzioni HiveQL vengono sostituiti dal carattere `+` se è in uso Curl. I valori tra virgolette che contengono uno spazio, ad esempio il delimitatore, non devono essere sostituiti da `+`.

      Questo comando resituisce un'ID del comando che può essere usata per controllare lo stato del processo.

1. Per verificare lo stato del processo, usare il seguente comando:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Se il processo è stato completato, lo stato è **SUCCEEDED**.

1. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso `/example/rest`. Questo indirizzo archivia l'output nella directory `example/curl` nell'archiviazione predefinita dei cluster.

    È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure, vedere il documento [Usa interfaccia della riga di comando di Azure con Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive con HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)

Per altre informazioni sull'API REST usata in questo documento, vedere il documento [Informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).