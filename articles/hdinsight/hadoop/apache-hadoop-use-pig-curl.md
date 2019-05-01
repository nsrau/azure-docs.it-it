---
title: Usare Apache Pig di Hadoop con REST in HDInsight - Azure
description: Informazioni su come usare REST per eseguire processi Pig Latin in un cluster Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 690a4bf08c7bfd6ccc039fdd04a3dda26b5a9301
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717852"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>Eseguire processi Apache Pig con Apache Hadoop in HDInsight tramite REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Informazioni su come eseguire processi Apache Pig Latin inviando richieste REST a un cluster Azure HDInsight. Per illustrare come poter interagire con HDInsight usando l'API REST WebHCat, viene usato Curl.

> [!NOTE]  
> Se si ha già familiarità con l'uso di server Apache Hadoop basati su Linux ma non si è esperti di HDInsight, vedere [Informazioni sull'uso di HDInsight in Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Prerequisiti

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Linux o basato su Windows)

  > [!IMPORTANT]  
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Eseguire processi Apache Pig mediante Curl


> [!NOTE]
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](https://en.wikipedia.org/wiki/Basic_access_authentication). Per essere certi che le credenziali vengano inviate al server in modo sicuro, eseguire sempre le richieste usando il protocollo Secure HTTP (HTTPS).
>
> Quando si usano i comandi riportati in questa sezione, sostituire `USERNAME` con l'utente da autenticare nel cluster e `PASSWORD` con la password dell'account utente. Sostituire `CLUSTERNAME` con il nome del cluster.
>


1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Dovrebbe essere visualizzata la risposta JSON seguente:

        {"status":"ok","version":"v1"}

    I parametri usati in questo comando sono i seguenti:

   * **-u**: Il nome utente e la password usati per autenticare la richiesta
   * **-G**: Indica che è una richiesta GET

     La parte iniziale dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, è uguale per tutte le richieste. Il percorso, **/status**, indica che la richiesta deve restituire lo stato di WebHCat, noto anche come Templeton, per il server.

2. Usare il seguente codice per inviare un processo Pig Latin al cluster:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    I parametri usati in questo comando sono i seguenti:

   * **-d**: Dato che `-G` non viene usato, la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.

   * **user.name**: L'utente che esegue il comando
   * **eseguire**: Le istruzioni Pig Latin da eseguire
   * **statusdir**: La directory in cui è scritto lo stato del processo

     > [!NOTE]  
     > Si noti che gli spazi tra le istruzioni Pig Latin vengono sostituiti dal carattere `+` se è in uso Curl.

     Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo, ad esempio:

       {"id":"job_1415651640909_0026"}

3. Per verificare lo stato del processo, usare il comando seguente:

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Sostituire `JOBID` con il valore restituito nel passaggio precedente. Se, ad esempio, il valore restituito è `{"id":"job_1415651640909_0026"}`, `JOBID` sarà `job_1415651640909_0026`.

    Se il processo è stato completato, lo stato è **SUCCEEDED**.

    > [!NOTE]  
    > Questa richiesta Curl restituisce un documento JSON (JavaScript Object Notation) con informazioni sul processo e jq viene usato per recuperare il valore di stato.

## <a id="results"></a>Visualizzare risultati

Quando lo stato del processo viene modificato in **SUCCEEDED**, è possibile recuperare i risultati del processo. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso `/example/pigcurl`.

HDInsight usa l'Archiviazione di Microsoft Azure o Azure Data Lake Storage come archivio dati predefinito. In base all'archivio usato, sono disponibili vari modi per ottenere i dati. Per altre informazioni, vedere la sezione relativa all'archiviazione del documento [Informazioni sull'uso di HDInsight in Linux](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-storage).

## <a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Pig nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight:

* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
