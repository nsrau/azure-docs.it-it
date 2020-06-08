---
title: Operazioni di Apache Spark supportate da Hive Warehouse Connector in Azure HDInsight
description: Informazioni sulle diverse funzionalità di Hive Warehouse Connector in Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 999c58871ed811d91fd96d158ea6f65db6c718f3
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853753"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Operazioni di Apache Spark supportate da Hive Warehouse Connector in Azure HDInsight

Questo articolo illustra le operazioni basate su Spark supportate da Hive Warehouse Connector (HWC). Tutti gli esempi riportati di seguito verranno eseguiti tramite la shell Apache Spark.

## <a name="prerequisite"></a>Prerequisito

Completare la procedura di [configurazione di Hive Warehouse Connector](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup).

## <a name="getting-started"></a>Introduzione

Per avviare una sessione della shell Spark, seguire questa procedura:

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster Apache Spark. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nella sessione ssh eseguire il comando seguente per prendere nota della versione `hive-warehouse-connector-assembly`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Modificare il codice seguente con la versione `hive-warehouse-connector-assembly` identificata in precedenza. Eseguire quindi il comando per avviare la shell Spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Dopo l'avvio della shell Spark, è possibile avviare un'istanza di Hive Warehouse Connector usando i comandi seguenti:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Creazione di DataFrame Spark con query Hive

I risultati di tutte le query che usano la libreria HWC vengono restituiti come DataFrame. Gli esempi seguenti illustrano come creare una query Hive di base.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

I risultati della query sono DataFrame Spark, che possono essere usati con le librerie Spark come MLIB e SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Scrittura di DataFrame Spark nelle tabelle Hive

Spark non supporta in modo nativo la scrittura nelle tabelle ACID gestite di Hive. Tuttavia, usando HWC, è possibile scrivere qualsiasi DataFrame in una tabella Hive. È possibile vedere come opera questa funzionalità nell'esempio seguente:

1. Creare una tabella denominata `sampletable_colorado` e specificarne le colonne usando il comando seguente:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrare la tabella `hivesampletable` dove la colonna `state` è uguale a `Colorado`. Questa query Hive restituisce un DataFrame Spark che viene salvato nella tabella Hive `sampletable_colorado` con la funzione `write`.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Verificare i risultati con il comando seguente:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![Hive Warehouse Connector mostra la tabella Hive](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)


## <a name="structured-streaming-writes"></a>Operazioni di scrittura di flussi strutturati

Con Hive Warehouse Connector, è possibile usare i flussi Spark per scrivere dati nelle tabelle Hive.

> [!IMPORTANT]
> Le operazioni di scrittura di flussi strutturati non sono supportate nei cluster Spark 4.0 abilitati per ESP.

Seguire la procedura descritta di seguito per inserire dati da un flusso Spark sulla porta localhost 9999 in una tabella Hive tramite Hive Warehouse Connector.

1. Dalla shell Spark aperta, avviare un flusso Spark con il comando seguente:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generare i dati per il flusso Spark creato, seguendo questa procedura:
    1. Aprire una seconda sessione SSH nello stesso cluster Spark.
    1. Al prompt dei comandi digitare `nc -lk 9999`. Questo comando usa l'utilità netcat per inviare i dati dalla riga di comando alla porta specificata.

1. Tornare alla prima sessione SSH e creare una nuova tabella Hive per conservare i dati di flusso. Nella shell Spark immettere il comando seguente:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Scrivere quindi i dati di flusso nella tabella appena creata usando il comando seguente:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Attualmente è necessario impostare le opzioni `metastoreUri` e `database` in modo manuale a causa di un problema noto di Apache Spark. Per altre informazioni su questo problema, vedere [SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Tornare alla seconda sessione SSH e immettere i valori seguenti:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Tornare alla prima sessione SSH e notare la breve attività. Per visualizzare i dati, usare il comando seguente:

    ```scala
    hive.table("stream_table").show()
    ```

Usare **CTRL + C** per arrestare netcat nella seconda sessione SSH. Usare `:q` per uscire dalla shell Spark nella prima sessione SSH.

## <a name="next-steps"></a>Passaggi successivi

* [Vedere le informazioni sull'integrazione di HWC con Apache Spark e Apache Hive](./apache-hive-warehouse-connector.md)
* [Usare Interactive Query in HDInsight](./apache-interactive-query-get-started.md)
* [Vedere le informazioni sull'integrazione di HWC con Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)