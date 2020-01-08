---
title: Usare una shell Spark interattiva in Azure HDInsight
description: Una shell Spark interattiva fornisce un processo di lettura-esecuzione-stampa per eseguire i comandi di Spark uno alla volta e visualizzare i risultati.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/12/2019
ms.openlocfilehash: f088b8210b8170d22e84d131f0a72f5f8caa3b92
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435219"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Eseguire Apache Spark dalla shell Spark

Una shell [Apache Spark](https://spark.apache.org/) interattiva fornisce un ambiente REPL (Read-Execute-Print Loop, ciclo di lettura-esecuzione-stampa) per eseguire i comandi di Spark uno alla volta e visualizzare i risultati. Questo processo è utile per lo sviluppo e il debug. Spark fornisce una shell per ognuno dei linguaggi supportati: Scala, Python e R.

## <a name="run-an-apache-spark-shell"></a>Eseguire una Shell Apache Spark

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERname con il nome del cluster e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Spark fornisce le shell per scala (Spark-Shell) e Python (pyspark). Nella sessione SSH, immettere uno dei comandi seguenti:

    ```bash
    spark-shell
    pyspark
    ```

    È ora possibile immettere i comandi Spark nel linguaggio appropriato.

1. Alcuni comandi di esempio di base:

    ```scala
    // Load data
    var data = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load("/HdiSamples/HdiSamples/SensorSampleData/building/building.csv")

    // Show data
    data.show()

    // Select certain columns
    data.select($"BuildingID", $"Country").show(10)

    // exit shell
    :q
    ```

## <a name="sparksession-and-sparkcontext-instances"></a>Istanze di SparkSession e SparkContext

Per impostazione predefinita, quando si esegue la shell Spark vengono create automaticamente le istanze di SparkSession e SparkContext.

Per accedere all'istanza di SparkSession, immettere `spark`. Per accedere all'istanza di SparkContext, immettere `sc`.

## <a name="important-shell-parameters"></a>Parametri importanti della shell

Il comando della shell Spark (`spark-shell`o `pyspark`) supporta molti parametri della riga di comando. Per visualizzare un elenco completo dei parametri, avviare la shell Spark con l'opzione `--help`. Alcuni di questi parametri possono essere applicati solo a `spark-submit`, a cui viene eseguito il wrapping della shell di Spark.

| opzione | description | esempio |
| --- | --- | --- |
| -master MASTER_URL | Specifica l'URL master. In HDInsight questo valore è sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Elenco delimitato da virgole di file JAR locali da includere nei percorsi di classe di driver ed executor. In HDInsight questo elenco è composto da percorsi al file system predefinito in Archiviazione di Azure o Data Lake Storage. | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Elenco delimitato da virgole di coordinate Maven da includere nei percorsi di classe di driver ed executor. Viene eseguita la ricerca nel repository Maven locale, quindi nel repository Maven centrale e in eventuali repository remoti aggiuntivi specificati con `--repositories`. Il formato per le coordinate è *IDgruppo*:*IDelemento*:*versione*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Solo per Python, elenco delimitato da virgole di file ZIP, EGG o PY da inserire in PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Introduzione ad Apache Spark in HDInsight](apache-spark-overview.md) per una panoramica.
- Vedere [Creare un cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md) per usare cluster Spark e SparkSQL.
- Vedere [Informazioni sullo streaming strutturato Apache Spark](apache-spark-streaming-overview.md) per scrivere applicazioni che elaborano i dati di streaming con Spark.
