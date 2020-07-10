---
title: Usare MapReduce e Curl con Apache Hadoop in HDInsight - Azure
description: Informazioni su come eseguire in modalità remota processi MapReduce con Apache Hadoop in HDInsight mediante Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: 407db727f521ea7731f0cbdbdd05c4338c9f452e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207724"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Esecuzione di processi MapReduce con Apache Hadoop in HDInsight usando REST

Informazioni su come usare l'API REST di Apache Hive WebHCat per eseguire processi MapReduce in un cluster HDInsight in un Apache Hadoop. Curl viene usato per illustrare come sia possibile interagire con HDInsight usando richieste HTTP non elaborate per eseguire processi MapReduce.

> [!NOTE]  
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere il documento [Informazioni sull'uso di HDInsight in Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [creare cluster Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

È possibile: 
  * Windows PowerShell o,
  * [Curl](https://curl.haxx.se/) con [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Eseguire un processo MapReduce

> [!NOTE]  
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore del cluster HDInsight. È necessario specificare il nome del cluster come parte dell'URI usato per inviare le richieste al server.
>
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](https://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS per essere certi che le credenziali vengano inviate in modo sicuro al server.

### <a name="curl"></a>Curl

1. Per semplicità d'uso, impostare le variabili seguenti. Questo esempio è basato su un ambiente Windows, modificato in base alle esigenze dell'ambiente in uso.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    I parametri usati in questo comando sono i seguenti:

   * **-u**: il nome utente e la password usati per autenticare la richiesta.
   * **-G**: indica che questa operazione è una richiesta GET

   La parte iniziale dell'URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`, è uguale per tutte le richieste.

    Si riceverà una risposta simile al testo JSON seguente:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Per inviare un processo MapReduce, usare il comando seguente. Modificare il percorso di **JQ** in base alle esigenze.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    La fine dell'URI (/mapreduce/jar) indica a WebHCat che la richiesta avvia un processo MapReduce da una classe in un file con estensione jar. I parametri usati in questo comando sono i seguenti:

   * **-d**: `-G` non viene usato, quindi la richiesta usa il metodo post per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.
     * **user.name**: l'utente che esegue il comando.
     * **jar**: il percorso del file con estensione jar che contiene la classe da eseguire.
     * **class**: la classe che contiene la logica MapReduce.
     * **arg**: gli argomenti da passare al processo MapReduce. In questo caso, il file di testo di input e la directory usata per l'output

    Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo: `job_1415651640909_0026` .

1. Per verificare lo stato del processo, usare il seguente comando. Sostituire il valore di `JOBID` con il valore **effettivo** restituito nel passaggio precedente. Rivedere il percorso di **JQ** in base alle esigenze.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Per semplicità d'uso, impostare le variabili seguenti. Sostituire `CLUSTERNAME` con il nome del cluster effettivo. Eseguire il comando e immettere la password di accesso del cluster quando richiesto.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Si riceverà una risposta simile al testo JSON seguente:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Per inviare un processo MapReduce, usare il seguente comando:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    La fine dell'URI (/mapreduce/jar) indica a WebHCat che la richiesta avvia un processo MapReduce da una classe in un file con estensione jar. I parametri usati in questo comando sono i seguenti:

    * **user.name**: l'utente che esegue il comando.
    * **jar**: il percorso del file con estensione jar che contiene la classe da eseguire.
    * **class**: la classe che contiene la logica MapReduce.
    * **arg**: gli argomenti da passare al processo MapReduce. In questo caso, il file di testo di input e la directory usata per l'output

   Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo: `job_1415651640909_0026` .

1. Per verificare lo stato del processo, usare il seguente comando:

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

### <a name="both-methods"></a>Entrambi i metodi

1. Se il processo è stato completato, lo stato restituito è `SUCCEEDED`.

1. Dopo che lo stato del processo risulta essere `SUCCEEDED`, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output. In questo esempio la località è `/example/curl`. Questo indirizzo archivia l'output del processo nella risorsa di archiviazione predefinita dei cluster in `/example/curl`.

È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure per lavorare con l'archiviazione BLOB di Azure, vedere [Guida introduttiva: creare, scaricare ed elencare BLOB con l'interfaccia](../../storage/blobs/storage-quickstart-blobs-cli.md)della riga di comando

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
