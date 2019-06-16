---
title: Usare MapReduce e Curl con Apache Hadoop in HDInsight - Azure
description: Informazioni su come eseguire in modalità remota processi MapReduce con Apache Hadoop in HDInsight mediante Curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: e4968310459097fc6a00f7c453846fe61726c3d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64716132"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Esecuzione di processi MapReduce con Apache Hadoop in HDInsight usando REST

Informazioni su come usare l'API di REST WebHCat Apache Hive per eseguire processi MapReduce in un Apache Hadoop nel cluster HDInsight. Curl viene usato per illustrare come sia possibile interagire con HDInsight usando richieste HTTP non elaborate per eseguire processi MapReduce.

> [!NOTE]  
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere il documento [Informazioni sull'uso di HDInsight in Linux](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Prerequisiti

* Un cluster Hadoop in HDInsight
* Windows PowerShell o [Curl](https://curl.haxx.se/) e [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Eseguire un processo MapReduce

> [!NOTE]  
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore del cluster HDInsight. È necessario specificare il nome del cluster come parte dell'URI usato per inviare le richieste al server.
>
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](https://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Per configurare l'accesso al cluster usato dagli script in questo documento, usare uno dei comandi seguenti:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Per configurare il nome del cluster, usare uno dei comandi seguenti:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Si riceverà una risposta simile al testo JSON seguente:

        {"status":"ok","version":"v1"}

    I parametri usati in questo comando sono i seguenti:

   * **-u**: indica il nome utente e la password usati per autenticare la richiesta
   * **-G**: indica che questa operazione è una richiesta GET

   La parte iniziale dell'URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** , è uguale per tutte le richieste.

4. Per inviare un processo MapReduce, usare il seguente comando:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

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

   * **-d**: `-G` non viene usato, quindi la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.
     * **user.name**: l'utente che esegue il comando
     * **jar**: il percorso del file con estensione jar che contiene la classe da eseguire
     * **class**: la classe che contiene la logica MapReduce
     * **arg**: gli argomenti da passare al processo MapReduce In questo caso, il file di testo di input e la directory usata per l'output

   Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo:

       job_1415651640909_0026

5. Per verificare lo stato del processo, usare il seguente comando:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
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

    Se il processo è stato completato, lo stato restituito è `SUCCEEDED`.

   > [!NOTE]  
   > Questa richiesta curl restituisce un documento JSON con informazioni sul processo. Jq viene usato per recuperare solo il valore di stato.

6. Dopo che lo stato del processo risulta essere `SUCCEEDED`, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output. In questo esempio la località è `/example/curl`. Questo indirizzo archivia l'output del processo nella risorsa di archiviazione predefinita dei cluster in `/example/curl`.

È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Per altre informazioni sull'uso dei BLOB dalla riga di comando di Azure, vedere il documento [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)
* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
