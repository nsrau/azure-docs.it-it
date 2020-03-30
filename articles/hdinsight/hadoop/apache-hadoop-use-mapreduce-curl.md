---
title: Usare MapReduce e Curl con Apache Hadoop in HDInsight - Azure
description: Informazioni su come eseguire in modalità remota processi MapReduce con Apache Hadoop in HDInsight mediante Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302713"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Esecuzione di processi MapReduce con Apache Hadoop in HDInsight usando REST

Informazioni su come usare l'API REST Apache Hive WebHCat per eseguire processi MapReduce in un Apache Hadoop nel cluster HDInsight.Learn how to use the Apache Hive WebHCat REST API to run MapReduce jobs on an Apache Hadoop on HDInsight cluster. Curl viene usato per illustrare come sia possibile interagire con HDInsight usando richieste HTTP non elaborate per eseguire processi MapReduce.

> [!NOTE]  
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere il documento [Informazioni sull'uso di HDInsight in Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [Creare cluster Apache Hadoop tramite il portale](../hdinsight-hadoop-create-linux-clusters-portal.md)di Azure.

È possibile: 
  * Windows PowerShell o,
  * [Ricciolo](https://curl.haxx.se/) con [jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Eseguire un processo MapReduce

> [!NOTE]  
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore del cluster HDInsight. È necessario specificare il nome del cluster come parte dell'URI usato per inviare le richieste al server.
>
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](https://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS per essere certi che le credenziali vengano inviate in modo sicuro al server.

### <a name="curl"></a>Curl

1. Per facilità d'uso, impostare le variabili riportate di seguito. Questo esempio è basato su un ambiente Windows, rivedere in base alle esigenze per l'ambiente.

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

    ```output
    {"version":"v1","status":"ok"}
    ```

1. Per inviare un processo MapReduce, utilizzare il comando seguente. Modificare il percorso in **jq** in base alle esigenze.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    La fine dell'URI (/mapreduce/jar) indica a WebHCat che la richiesta avvia un processo MapReduce da una classe in un file con estensione jar. I parametri usati in questo comando sono i seguenti:

   * **-d** `-G` : non viene utilizzato, pertanto la richiesta viene utilizzata per impostazione predefinita sul metodo POST. `-d` specifica i valori di dati che vengono inviati con la richiesta.
     * **user.name**: l'utente che esegue il comando.
     * **jar**: il percorso del file con estensione jar che contiene la classe da eseguire.
     * **class**: la classe che contiene la logica MapReduce.
     * **arg**: gli argomenti da passare al processo MapReduce. In questo caso, il file di testo di input e la directory usata per l'output

    Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo:

       job_1415651640909_0026

1. Per verificare lo stato del processo, usare il seguente comando. Sostituire il `JOBID` valore di per con il valore **effettivo** restituito nel passaggio precedente. Rivedere la posizione di **jq** in base alle esigenze.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Per facilità d'uso, impostare le variabili riportate di seguito. Sostituire `CLUSTERNAME` con il nome effettivo del cluster. Eseguire il comando e immettere la password di accesso al cluster quando richiesto.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:Use the following command to verify that you can connect to your HDInsight cluster:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Si riceverà una risposta simile al testo JSON seguente:

    ```output
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

   Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo:

       job_1415651640909_0026

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

È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Per altre informazioni sull'uso dell'interfaccia della riga di comando di Azure per usare l'archiviazione BLOB di Azure, vedere Guida introduttiva: Creare, scaricare ed elencare BLOB con l'interfaccia della riga di comando di Azure.For more information on using the Azure CLI to work with Azure Blob storage, see [Quickstart: Create, download, and list blobs with Azure CLI.](../../storage/blobs/storage-quickstart-blobs-cli.md)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
