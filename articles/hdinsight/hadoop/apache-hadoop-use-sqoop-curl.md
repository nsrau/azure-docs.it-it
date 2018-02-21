---
title: Usare Sqoop di Hadoop con Curl in HDInsight - Azure | Microsoft Docs
description: "Informazioni su come inviare in modalità remota processi Sqoop a HDInsight mediante Curl."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 08e7288d4d9d06f3a74464817892c2b03ae8a86c
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Esecuzione di processi Sqoop con Hadoop in HDInsight mediante Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Curl per l'esecuzione di processi Sqoop in un cluster Hadoop in HDInsight.

Curl viene usato per illustrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati di processi Sqoop. Ciò avviene mediante l'API REST WebHCat, nota in precedenza come Templeton, fornita dal cluster HDInsight.

## <a name="prerequisites"></a>prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Completare la procedura [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) per configurare un ambiente con un cluster HDInsight e un database SQL di Azure.
* [Curl](http://curl.haxx.se/). Curl è uno strumento per il trasferimento di dati da o verso un cluster HDInsight.
* [jq](http://stedolan.github.io/jq/). L'utilità jq viene usata per elaborare i dati JSON restituiti dalle richieste REST.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Inviare processi Sqoop mediante Azure Curl
> [!NOTE]
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI (Uniform Resource Identifier) usato per inviare le richieste al server.
> 
> Per i comandi riportati in questa sezione, sostituire **USERNAME** con l'utente da autenticare nel cluster e **PASSWORD** con la password dell'account utente. Sostituire **CLUSTERNAME** con il nome del cluster.
> 
> L'API REST viene protetta tramite l' [autenticazione di base](http://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS (Secure HTTP) per essere certi che le credenziali vengano inviate in modo sicuro al server.
> 
> 

1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Dovrebbe essere visualizzato un messaggio simile al seguente:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    I parametri usati in questo comando sono i seguenti:
   
   * **-u** : il nome utente e la password usati per autenticare la richiesta.
   * **-G** : indica che si tratta di una richiesta GET.
     
     L'inizio dell'URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, sarà uguale per tutte le richieste. Il percorso, **/status**, indica che la richiesta deve restituire uno stato di WebHCat (noto anche come Templeton) per il server. 
2. Usare quanto segue per inviare un processo sqoop:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    I parametri usati in questo comando sono i seguenti:

    * **-d**: poiché `-G` non viene usato, la richiesta userà il metodo POST per impostazione predefinita. `-d` specifica i valori di dati che vengono inviati con la richiesta.

        * **user.name** : l'utente che esegue il comando.

        * **command** : il comando Sqoop da eseguire.

        * **statusdir** : la directory in cui verrà scritto lo stato del processo.

    Questo comando restituisce un ID processo utilizzabile per verificare lo stato del processo.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Per verificare lo stato del processo, usare il seguente comando. Sostituire **JOBID** con il valore restituito nel passaggio precedente. Se ad esempio il valore restituito è `{"id":"job_1415651640909_0026"}`, **JOBID** sarà `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Se il processo è stato completato, lo stato sarà **SUCCEEDED**.
   
   > [!NOTE]
   > Questa richiesta curl restituisce un documento JSON (JavaScript Object Notation) con informazioni sul processo. jq viene usato per recuperare il valore di stato.
   > 
   > 
4. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output, in questo caso, **wasb:///example/data/sqoop/curl**. Questo indirizzo consente di archiviare l'output del processo nella directory **example/data/sqoop/curl** del contenitore di archiviazione predefinito usato dal cluster HDInsight.
   
    È possibile usare il portale di Azure per accedere ai BLOB stderr e stdout.  È anche possibile usare Microsoft SQL Server Management Studio per controllare i dati che vengono caricati nella tabella log4jlogs.

## <a name="limitations"></a>Limitazioni
* Esportazione di massa: con HDInsight basato su Linux, attualmente il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti di massa.
* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="summary"></a>Summary
Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Sqoop nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere la <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guida relativa all'API REST Sqoop</a>.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni generali su Hive con HDInsight:

* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Per altri articoli su HDInsight relativi a curl:
 
* [Creare i cluster Hadoop tramite l'API REST di Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Eseguire query Hive con Hadoop in HDInsight tramite REST](apache-hadoop-use-hive-curl.md)
* [Eseguire processi MapReduce con Hadoop in HDInsight tramite REST](apache-hadoop-use-mapreduce-curl.md)
* [Eseguire processi Pig con Hadoop in HDInsight mediante cUrl](apache-hadoop-use-pig-curl.md)



