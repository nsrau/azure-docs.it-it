---
title: Usare una shell Spark interattiva in Azure HDInsight | Microsoft Docs
description: Una shell Spark interattiva fornisce un processo di lettura-esecuzione-stampa per eseguire i comandi di Spark uno alla volta e visualizzare i risultati.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: maxluk
ms.openlocfilehash: 55ff40165a1b25949cb202d9a5174e1f52fe4630
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="run-spark-from-the-spark-shell"></a>Eseguire Spark dalla shell Spark

Una shell Spark interattiva fornisce un ambiente REPL (Read-Execute-Print Loop, ciclo di lettura-esecuzione-stampa) per eseguire i comandi di Spark uno alla volta e visualizzare i risultati. Questo processo è utile per lo sviluppo e il debug. Spark fornisce una shell per ognuno dei linguaggi supportati: Scala, Python e R.

## <a name="get-to-a-spark-shell-with-ssh"></a>Accedere a una Shell Spark con SSH

Accedere a una shell Spark in HDInsight connettendosi al nodo head primario del cluster tramite SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

È possibile ottenere il comando SSH completo per il cluster dal portale di Azure:

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Passare al riquadro per il cluster HDInsight Spark.
3. Selezionare Secure Shell (SSH).

    ![Riquadro di HDInsight nel portale di Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Copiare il comando SSH visualizzato ed eseguirlo nel terminale.

    ![Riquadro SSH di HDInsight nel portale di Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Per informazioni dettagliate sull'uso di SSH per connettersi a HDInsight, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-a-spark-shell"></a>Eseguire una shell Spark

Spark fornisce shell per Scala (spark-shell), Python (pyspark) e R (sparkR). Nella sessione SSH in corrispondenza del nodo head del cluster HDInsight immettere uno dei comandi seguenti:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

È ora possibile immettere i comandi Spark nel linguaggio appropriato.

## <a name="sparksession-and-sparkcontext-instances"></a>Istanze di SparkSession e SparkContext

Per impostazione predefinita, quando si esegue la shell Spark vengono create automaticamente le istanze di SparkSession e SparkContext.

Per accedere all'istanza di SparkSession, immettere `spark`. Per accedere all'istanza di SparkContext, immettere `sc`.

## <a name="important-shell-parameters"></a>Parametri importanti della shell

Il comando della shell Spark (`spark-shell`, `pyspark` o `sparkR`) supporta numerosi parametri della riga di comando. Per visualizzare un elenco completo dei parametri, avviare la shell Spark con l'opzione `--help`. Si noti che alcuni di questi parametri potrebbero applicarsi solo a `spark-submit`, di cui la shell Spark esegue il wrapping.

| opzione | descrizione | esempio |
| --- | --- | --- |
| -master MASTER_URL | Specifica l'URL master. In HDInsight questo valore è sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Elenco delimitato da virgole di file JAR locali da includere nei percorsi di classe di driver ed executor. In HDInsight questo elenco è composto da percorsi al file system predefinito in Archiviazione di Azure o Data Lake Store. | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Elenco delimitato da virgole di coordinate Maven da includere nei percorsi di classe di driver ed executor. Viene eseguita la ricerca nel repository Maven locale, quindi nel repository Maven centrale e in eventuali repository remoti aggiuntivi specificati con `--repositories`. Il formato per le coordinate è *IDgruppo*:*IDelemento*:*versione*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Solo per Python, elenco delimitato da virgole di file ZIP, EGG o PY da inserire in PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Introduzione a Spark in HDInsight](apache-spark-overview.md) per una panoramica.
- Vedere [Creare un cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md) per usare cluster Spark e SparkSQL.
- Vedere [Informazioni sullo streaming strutturato Spark](apache-spark-streaming-overview.md) per scrivere applicazioni che elaborano i dati di streaming con Spark.

