---
title: Usare MapReduce e Curl con Hadoop in HDInsight | Documentazione Microsoft
description: "Informazioni su come eseguire in modalità remota processi MapReduce con Hadoop in HDInsight mediante Curl."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 41733f76e5f0c82c5321158d6a919f35f6faee3a
ms.lasthandoff: 03/21/2017


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Esecuzione di processi MapReduce con Hadoop in HDInsight mediante Curl
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In questo documento si apprenderà come usare Curl per l'esecuzione di processi MapReduce in un cluster Hadoop in HDInsight.

Curl viene usato per illustrare come sia possibile interagire con HDInsight usando richieste HTTP non elaborate per eseguire processi MapReduce. Ciò avviene mediante l'API REST WebHCat, nota in precedenza come Templeton, fornita dal cluster HDInsight.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere [Informazioni utili su Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop in HDInsight
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Eseguire processi MapReduce mediante Curl
> [!NOTE]
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore del cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI usato per inviare le richieste al server.
> 
> Per i comandi riportati in questa sezione, sostituire **USERNAME** con l'utente da autenticare nel cluster e **PASSWORD** con la password dell'account utente. Sostituire **CLUSTERNAME** con il nome del cluster.
> 
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](http://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS per essere certi che le credenziali vengano inviate in modo sicuro al server.
> 
> 

1. Da una riga di comando usare il seguente comando per verificare che sia possibile connettersi al cluster HDInsight:
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    Dovrebbe essere visualizzato un messaggio simile al seguente:
   
        {"status":"ok","version":"v1"}
   
    I parametri usati in questo comando sono i seguenti:
   
   * **-u**: il nome utente e la password usati per autenticare la richiesta.
   * **-G**: indica che si tratta di una richiesta GET.
     
     L'inizio dell'URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà uguale per tutte le richieste.
2. Per inviare un processo MapReduce, usare il seguente comando:
   
        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
   
    La fine dell'URI (/mapreduce/jar) indica a WebHCat che la richiesta avvierà un processo MapReduce da una classe in un file con estensione jar. I parametri usati in questo comando sono i seguenti:
   
   * **-d**: `-G` non viene usato, quindi la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.
     
     * **user.name**: l'utente che esegue il comando.
     * **jar**: il percorso del file con estensione jar che contiene la classe da eseguire.
     * **class**: la classe che contiene la logica MapReduce.
     * **arg**: gli argomenti da passare al processo MapReduce, in questo caso il file di testo di input e la directory usata per l'output.
     
     Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo:
     
       {"id":"job_1415651640909_0026"}
3. Per verificare lo stato del processo, usare il seguente comando. Sostituire **JOBID** con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è `{"id":"job_1415651640909_0026"}`, JOBID sarà `job_1415651640909_0026`.
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    Se il processo è stato completato, lo stato sarà "SUCCEEDED".
   
   > [!NOTE]
   > Questa richiesta Curl restituisce un documento JSON con informazioni sul processo. jq viene usato per recuperare il valore di stato.
   > 
   > 
4. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output, in questo caso **wasbs:///example/curl**. Questo indirizzo consente di archiviare l'output del processo nella directory **example/curl** del contenitore di archiviazione predefinito usato dal cluster HDInsight.

È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure](../cli-install-nodejs.md). Ad esempio, per elencare i file contenuti in **example/curl**, usare il seguente comando:

    azure storage blob list <container-name> example/curl

Per scaricare un file, usare il comando seguente:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [!NOTE]
> È necessario specificare il nome dell'account di archiviazione contenente il BLOB usando i parametri `-a` e `-k` oppure impostare le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**. Per altre informazioni, vedere [come caricare dati in HDInsight](hdinsight-upload-data.md) .
> 
> 

## <a id="summary"></a>Riepilogo
Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Hive nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Passaggi successivi
Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)


