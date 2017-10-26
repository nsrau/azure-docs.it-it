---
title: Usare Pig di Hadoop con REST in HDInsight - Azure | Microsoft Docs
description: Informazioni su come usare REST per eseguire processi Pig Latin in un cluster Hadoop in Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: f07b6fd4f40616c0a4c2f2086647445d725a34e5
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Eseguire processi Pig con Hadoop in HDInsight tramite REST

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Informazioni su come eseguire processi Pig Latin inviando richieste REST a un cluster HDInsight di Azure. Per illustrare come poter interagire con HDInsight usando l'API REST WebHCat, viene usato Curl.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Prerequisiti

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Linux o basato su Windows)

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Eseguire processi Pig mediante Curl

> [!NOTE]
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](http://en.wikipedia.org/wiki/Basic_access_authentication). Per essere certi che le credenziali vengano inviate al server in modo sicuro, eseguire sempre le richieste usando il protocollo Secure HTTP (HTTPS).
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

    * **-u**: il nome utente e la password usati per autenticare la richiesta.
    * **-G**: indica che è una richiesta GET.

     L'inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà uguale per tutte le richieste. Il percorso, **/status**, indica che la richiesta deve restituire lo stato di WebHCat, noto anche come Templeton, per il server.

2. Usare il seguente codice per inviare un processo Pig Latin al cluster:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    I parametri usati in questo comando sono i seguenti:

    * **-d**: dato che `-G` non viene usato, la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.

    * **user.name**: l'utente che esegue il comando.
    * **execute**: le istruzioni Pig Latin da eseguire.
    * **statusdir**: la directory in cui è scritto lo stato del processo.

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

HDInsight usa l'archiviazione di Azure o Azure Data Lake Store come archivio dati predefinito. In base all'archivio usato, sono disponibili vari modi per ottenere i dati. Per altre informazioni, vedere la sezione relativa all'archiviazione del documento [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store).

## <a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Pig nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
