---
title: Usare MapReduce e Curl con Hadoop in HDInsight - Azure | Microsoft Docs
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
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: f6d30b531562e657b3dab2c80637959186a09774
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>Esecuzione di processi MapReduce con Hadoop in HDInsight tramite REST

Informazioni su come usare l'API REST WebHCat per l'esecuzione di processi MapReduce in un cluster Hadoop in HDInsight. Curl viene usato per illustrare come sia possibile interagire con HDInsight usando richieste HTTP non elaborate per eseguire processi MapReduce.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux, ma non si ha esperienza con HDInsight, vedere il documento [Informazioni utili su Hadoop basato su Linux in HDInsight](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Prerequisiti

* Un cluster Hadoop in HDInsight
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Eseguire processi MapReduce mediante Curl

> [!NOTE]
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore del cluster HDInsight. È necessario specificare il nome del cluster come parte dell'URI usato per inviare le richieste al server.
>
> Per i comandi in questa sezione, sostituire **admin** con l'utente per l'autenticazione nel cluster. Sostituire **CLUSTERNAME** con il nome del cluster. Quando richiesto, immettere la password per l'account dell'utente.
>
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](http://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS per essere certi che le credenziali vengano inviate in modo sicuro al server.


1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Si riceverà una risposta simile al testo JSON seguente:

        {"status":"ok","version":"v1"}

    I parametri usati in questo comando sono i seguenti:

   * **-u**: il nome utente e la password usati per autenticare la richiesta.
   * **-G**: indica che questa operazione è una richiesta GET

   L'inizio dell'URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà uguale per tutte le richieste.

2. Per inviare un processo MapReduce, usare il seguente comando:

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    La fine dell'URI (/mapreduce/jar) indica a WebHCat che la richiesta avvia un processo MapReduce da una classe in un file con estensione jar. I parametri usati in questo comando sono i seguenti:

   * **-d**: `-G` non viene usato, quindi la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.
    * **user.name**: l'utente che esegue il comando.
    * **jar**: il percorso del file con estensione jar che contiene la classe da eseguire.
    * **class**: la classe che contiene la logica MapReduce.
    * **arg**: gli argomenti da passare al processo MapReduce. In questo caso, il file di testo di input e la directory usata per l'output

   Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo:

       {"id":"job_1415651640909_0026"}

3. Per verificare lo stato del processo, usare il seguente comando:

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Sostituire **JOBID** con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è `{"id":"job_1415651640909_0026"}`, JOBID sarà `job_1415651640909_0026`.

    Se il processo è stato completato, lo stato restituito è `SUCCEEDED`.

   > [!NOTE]
   > Questa richiesta curl restituisce un documento JSON con informazioni sul processo. Jq viene usato per recuperare solo il valore di stato.

4. Dopo che lo stato del processo risulta essere `SUCCEEDED`, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output. In questo esempio la località è `/example/curl`. Questo indirizzo archivia l'output del processo nella risorsa di archiviazione predefinita dei cluster in `/example/curl`.

È possibile elencare e scaricare questi file usando l' [Interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Per altre informazioni sull'uso dei BLOB dalla riga di comando di Azure, vedere il documento [Uso dell'Interfaccia della riga di comando di Azure 2.0 con Archiviazione di Azure](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).