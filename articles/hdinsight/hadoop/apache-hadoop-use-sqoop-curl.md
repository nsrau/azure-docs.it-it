---
title: Usare curl per esportare i dati con Apache Sqoop in Azure HDInsight
description: Informazioni su come inviare in modalità remota i processi di Apache Sqoop ad Azure HDInsight usando curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7bd0afe4d0ea01671c996a0f536151d943e4fca7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84013011"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Eseguire processi Apache Sqoop in HDInsight con curl

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Curl per l'esecuzione di processi Apache Sqoop in un cluster Apache Hadoop in HDInsight. Questo articolo illustra come esportare i dati da archiviazione di Azure e importarli in un database di SQL Server usando curl. Questo articolo riprende l'articolo [Usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

Curl viene usato per illustrare come è possibile interagire con HDInsight tramite richieste HTTP non elaborate per eseguire, monitorare e recuperare i risultati di processi Sqoop. Ciò avviene mediante l'API REST WebHCat, nota in precedenza come Templeton, fornita dal cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Il completamento della [configurazione dell'ambiente di test](./hdinsight-use-sqoop.md#create-cluster-and-sql-database), come indicato nell'articolo [Usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* Un client per eseguire una query sul database SQL di Azure. Prendere in considerazione l'uso di [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) o [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl è uno strumento per il trasferimento di dati da o verso un cluster HDInsight.

* [jq](https://stedolan.github.io/jq/). L'utilità jq viene usata per elaborare i dati JSON restituiti dalle richieste REST.

* Familiarità con Sqoop. Per altre informazioni, vedere il [Manuale dell'utente di Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Inviare processi Apache Sqoop mediante Curl

Usare curl per esportare i dati usando i processi Apache Sqoop da archiviazione di Azure a SQL Server.

> [!NOTE]  
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI (Uniform Resource Identifier) usato per inviare le richieste al server.

Per i comandi in questa sezione, sostituire `USERNAME` con l'utente da autenticare nel cluster e sostituire `PASSWORD` con la password per l'account utente. Sostituire `CLUSTERNAME` con il nome del cluster.

L'API REST viene protetta tramite l' [autenticazione di base](https://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS (Secure HTTP) per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Per semplicità d'uso, impostare le variabili seguenti. Questo esempio è basato su un ambiente Windows, modificato in base alle esigenze dell'ambiente in uso.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Dovrebbe essere visualizzato un messaggio simile al seguente:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Usare quanto segue per inviare un processo sqoop:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    I parametri usati in questo comando sono i seguenti:

   * **-d** : poiché `-G` non viene usato, per impostazione predefinita la richiesta è il metodo post. `-d` specifica i valori di dati che vengono inviati con la richiesta.

       * **user.name** : l'utente che esegue il comando.

       * **command** : il comando Sqoop da eseguire.

       * **statusdir** : la directory in cui verrà scritto lo stato del processo.

     Questo comando restituirà un ID processo che può essere usato per controllare lo stato del processo.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Per verificare lo stato del processo, usare il seguente comando. Sostituire `JOBID` con il valore restituito nel passaggio precedente. Se, ad esempio, il valore restituito è `{"id":"job_1415651640909_0026"}` , `JOBID` sarà `job_1415651640909_0026` . Rivedere la posizione di `jq` in base alle esigenze.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Se il processo è stato completato, lo stato sarà **SUCCEEDED**.

   > [!NOTE]  
   > Questa richiesta curl restituisce un documento JSON (JavaScript Object Notation) con informazioni sul processo. jq viene usato per recuperare il valore di stato.

1. Dopo che lo stato del processo risulta essere **SUCCEEDED**, è possibile recuperare i risultati del processo dall'archivio BLOB di Azure. Il parametro `statusdir` passato con la query contiene il percorso del file di output; in questo caso `wasb:///example/data/sqoop/curl`. Questo indirizzo archivia l'output del processo nella directory del `example/data/sqoop/curl` contenitore di archiviazione predefinito usato dal cluster HDInsight.

    È possibile usare il portale di Azure per accedere ai BLOB stderr e stdout.

1. Per verificare che i dati siano stati esportati, usare le query seguenti del client SQL per visualizzare i dati esportati:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitazioni

* Esportazione bulk: con HDInsight basato su Linux, il connettore Sqoop usato per esportare i dati in Microsoft SQL Server o nel database SQL di Azure attualmente non supporta gli inserimenti bulk.
* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="summary"></a>Summary

Come illustrato in questo documento, è possibile usare una richiesta HTTP non elaborata per eseguire, monitorare e visualizzare i risultati dei processi Sqoop nel cluster HDInsight.

Per altre informazioni sull'interfaccia REST usata in questo articolo, vedere la <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">guida relativa all'API REST di Apache Sqoop</a>.

## <a name="next-steps"></a>Passaggi successivi

[Usare Apache Sqoop con Apache Hadoop in HDInsight](hdinsight-use-sqoop.md)

Per altri articoli su HDInsight relativi a curl:

* [Creare i cluster Apache Hadoop tramite l'API REST di Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Eseguire query Apache Hive con Apache Hadoop in HDInsight tramite REST](apache-hadoop-use-hive-curl.md)
* [Esecuzione di processi MapReduce con Apache Hadoop in HDInsight usando REST](apache-hadoop-use-mapreduce-curl.md)