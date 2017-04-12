---
title: Usare Pig di Hadoop con Curl in HDInsight | Documentazione Microsoft
description: Informazioni su come usare Curl per l&quot;esecuzione di processi Pig Latin in un cluster Hadoop in Azure HDInsight.
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
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ed5df94ec3455803cb3ea60f3a958132e0312ede
ms.lasthandoff: 04/12/2017


---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Eseguire processi Pig con Hadoop in HDInsight mediante Curl

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento spiega come usare Curl per l'esecuzione di processi Pig Latin in un cluster Azure HDInsight. Il linguaggio di programmazione Pig Latin consente di descrivere le trasformazioni applicate ai dati di input per produrre l'output desiderato.

Curl viene usato per illustrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati di processi Pig. Ciò avviene mediante l'API REST WebHCat, nota in precedenza come Templeton, fornita dal cluster HDInsight.

> [!NOTE]
> Se si ha già familiarità con l'uso di server Hadoop basati su Linux ma non si è esperti di HDInsight, vedere [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Linux o basato su Windows)

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Eseguire processi Pig mediante Curl

> [!NOTE]
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password da amministratore per il cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI (Uniform Resource Identifier) usato per inviare le richieste al server.
>
> Per i comandi riportati in questa sezione, sostituire **USERNAME** con l'utente da autenticare nel cluster e **PASSWORD** con la password dell'account utente. Sostituire **CLUSTERNAME** con il nome del cluster.
>
> L'API REST viene protetta tramite l' [autenticazione dell'accesso di base](http://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS (Secure HTTP) per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Dovrebbe essere visualizzato un messaggio simile al seguente:

        {"status":"ok","version":"v1"}

    I parametri usati in questo comando sono i seguenti:

    * **-u**: il nome utente e la password usati per autenticare la richiesta.
    * **-G**: indica che si tratta di una richiesta GET.

     L'inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà uguale per tutte le richieste. Il percorso, **/status**, indica che la richiesta deve restituire lo stato di WebHCat, noto anche come Templeton, per il server.

2. Usare il seguente codice per inviare un processo Pig Latin al cluster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    I parametri usati in questo comando sono i seguenti:

    * **-d**: dato che `-G` non viene usato, la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.

    * **user.name**: l'utente che esegue il comando.
    * **execute**: le istruzioni Pig Latin da eseguire.
    * **statusdir**: la directory in cui verrà scritto lo stato del processo.

    > [!NOTE]
    > Si noti che gli spazi tra le istruzioni Pig Latin vengono sostituiti dal carattere `+` se è in uso Curl.

    Questo comando dovrebbe restituire un ID processo utilizzabile per verificare lo stato del processo, ad esempio:

        {"id":"job_1415651640909_0026"}

3. Per verificare lo stato del processo, usare il seguente comando. Sostituire **JOBID** con il valore restituito nel passaggio precedente. Se ad esempio il valore restituito è `{"id":"job_1415651640909_0026"}`, **JOBID** sarà `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Se il processo è stato completato, lo stato sarà **SUCCEEDED**.

    > [!NOTE]
    > Questa richiesta Curl restituisce un documento JSON (JavaScript Object Notation) con informazioni sul processo e jq viene usato per recuperare il valore di stato.

## <a id="results"></a>Visualizzare risultati

Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio predefinito usato dal cluster. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso **/example/pigcurl**.

L'archivio di backup per HDInsight può essere Archiviazione di Azure o Azure Data Lake Store. A seconda dell'archivio usato, sono disponibili diversi modi per ottenere i dati. Per altre informazioni su come usare Archiviazione di Azure e Azure Data Lake Store, vedere la sezione [HDFS, archiviazione BLOB e Data Lake Store](hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store) del documento su HDInsight in Linux.

## <a id="summary"></a>Riepilogo

Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Pig nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere le [informazioni di riferimento su WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Pig in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

