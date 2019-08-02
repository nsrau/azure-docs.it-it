---
title: Eccezione OutOfMemoryError per Apache Spark in Azure HDInsight
description: Eccezione OutOfMemoryError per Apache Spark in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620987"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>Scenario: Eccezione OutOfMemoryError per Apache Spark in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

L'applicazione Apache Spark non è riuscita con un'eccezione non gestita OutOfMemoryError. È possibile che venga visualizzato un messaggio di errore simile al seguente:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>Causa

La causa più probabile di questa eccezione non è la memoria heap sufficiente. L'applicazione Spark richiede una quantità sufficiente di memoria heap Java Virtual Machines (JVM) quando viene eseguita come esecutori o driver.

## <a name="resolution"></a>Risoluzione

1. Determinare le dimensioni massime dei dati che possono essere gestiti dall'applicazione Spark. Eseguire una stima delle dimensioni in base al valore massimo della dimensione dei dati di input, i dati intermedi prodotti dalla trasformazione dei dati di input e dei dati di output prodotte ulteriormente la trasformazione dei dati intermedi. Se la stima iniziale non è sufficiente, aumentare leggermente le dimensioni e scorrere fino a quando non si verificano errori di memoria.

1. Assicurarsi che il cluster HDInsight da utilizzare disponga di sufficienti risorse in termini di memoria e core per supportare l'applicazione Spark. Ciò può essere determinato visualizzando la sezione Cluster Metrics (Metriche cluster) dell'interfaccia utente YARN del cluster e confrontando i valori di Memory Used (memoria usata) e Memory Total (memoria totale), VCores Used (VCore utilizzati) e VCores Total (VCore totali).

    ![visualizzazione memoria core yarn](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Impostare le configurazioni di Spark seguenti sui valori appropriati. Bilanciare i requisiti dell'applicazione con le risorse disponibili nel cluster. Questi valori non devono superare il 90% della memoria e dei core disponibili visualizzati da YARN e devono soddisfare anche i requisiti minimi di memoria dell'applicazione Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Memoria totale usata da tutti gli esecutori =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Memoria totale usata dal driver =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* [Panoramica sulla gestione della memoria di Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debug di un'applicazione Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
