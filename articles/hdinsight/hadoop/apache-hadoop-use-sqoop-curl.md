---
title: Usare Curl per esportare i dati con Apache Sqoop in HDInsight di Azure
description: Informazioni su come inviare in modalità remota processi Apache Sqoop a HDInsight mediante Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 345f492c5b2c754cbbcfa150561ee06b5a4154a5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126954"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Eseguire processi Apache Sqoop in HDInsight mediante Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Curl per l'esecuzione di processi Apache Sqoop in un cluster Apache Hadoop in HDInsight. Questo articolo illustra come esportare i dati da archiviazione di Azure e importarlo in un database di SQL Server mediante Curl. Questo articolo è una continuazione del [usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

Curl viene usato per illustrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati di processi Sqoop. Ciò avviene mediante l'API REST WebHCat, nota in precedenza come Templeton, fornita dal cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Completamento della [configurare l'ambiente di testing](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) dalla [usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* Un client di eseguire query sul database SQL di Azure. È consigliabile usare [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) oppure [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl è uno strumento per il trasferimento di dati da o verso un cluster HDInsight.

* [jq](https://stedolan.github.io/jq/). L'utilità jq viene usata per elaborare i dati JSON restituiti dalle richieste REST.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Inviare processi Apache Sqoop mediante Curl

Usare Curl per esportare i dati usando i processi Apache Sqoop da archiviazione di Azure a SQL Server.

> [!NOTE]  
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI (Uniform Resource Identifier) usato per inviare le richieste al server.

Per i comandi in questa sezione, sostituire `USERNAME` con l'utente da autenticare nel cluster e sostituire `PASSWORD` con la password per l'account utente. Sostituire `CLUSTERNAME` con il nome del cluster.
 
L'API REST viene protetta tramite l' [autenticazione di base](https://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS (Secure HTTP) per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Dovrebbe essere visualizzato un messaggio simile al seguente:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Sostituire `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` con i valori appropriati di prerequisiti. Usare quanto segue per inviare un processo sqoop:

    ```cmd
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

3. Per verificare lo stato del processo, usare il seguente comando. Sostituire `JOBID` con il valore restituito nel passaggio precedente. Ad esempio, se il valore restituito è stata `{"id":"job_1415651640909_0026"}`, quindi `JOBID` sarebbe `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Se il processo è stato completato, lo stato sarà **SUCCEEDED**.
   
   > [!NOTE]  
   > Questa richiesta curl restituisce un documento JSON (JavaScript Object Notation) con informazioni sul processo. jq viene usato per recuperare il valore di stato.

4. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso `wasb:///example/data/sqoop/curl`. Questo indirizzo archivia l'output del processo nel `example/data/sqoop/curl` directory nel contenitore di archiviazione predefinito usato dal cluster HDInsight.

    È possibile usare il portale di Azure per accedere ai BLOB stderr e stdout.

5. Per verificare che siano stati esportati i dati, usare le query seguenti dal client SQL per visualizzare i dati esportati:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitazioni
* Esportazione di massa: con HDInsight basato su Linux, attualmente il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti di massa.
* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="summary"></a>Riepilogo
Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Sqoop nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere la <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guida relativa all'API REST di Apache Sqoop</a>.

## <a name="next-steps"></a>Passaggi successivi
[Usare Apache Sqoop con Apache Hadoop in HDInsight](hdinsight-use-sqoop.md)

Per altri articoli su HDInsight relativi a curl:
 
* [Creare i cluster Apache Hadoop tramite l'API REST di Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Eseguire query Apache Hive con Apache Hadoop in HDInsight tramite REST](apache-hadoop-use-hive-curl.md)
* [Esecuzione di processi MapReduce con Apache Hadoop in HDInsight usando REST](apache-hadoop-use-mapreduce-curl.md)
* [Eseguire processi Apache Pig con Apache Hadoop in HDInsight tramite cURL](apache-hadoop-use-pig-curl.md)
