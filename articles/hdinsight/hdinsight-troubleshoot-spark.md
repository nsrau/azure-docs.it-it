---
title: Risoluzione dei problemi di Spark - Azure HDInsight | Microsoft Docs
description: Usare le domande frequenti su Spark per ottenere risposte alle domande comuni sull'uso di Spark nella piattaforma HDInsight.
keywords: Azure HDInsight, Spark, domande frequenti, guida alla risoluzione dei problemi, problemi comuni, configurazione dell'applicazione, Ambari
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7655acd689c3f5b869eb0ddb5f186cff032ca8b6
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="spark-troubleshooting"></a>Risoluzione dei problemi di Spark

Questo articolo descrive i problemi principali che possono verificarsi quando si usano i payload di Spark in Apache Ambari unitamente alle risoluzioni.

## <a name="how-do-i-configure-a-spark-application-through-ambari-on-clusters"></a>Procedura per configurare un'applicazione Spark tramite Ambari nei cluster

### <a name="issue"></a>Problema:

Necessità di configurare in Ambari la quantità di memoria e il numero di core che un'applicazione Spark può usare.  

### <a name="resolution-steps"></a>Procedura per la risoluzione: 

I valori di configurazione indicati in questa procedura devono essere già stati impostati nei cluster HDInsight Spark. Fare riferimento a [Why did my Spark application fail with an OutOfMemoryError?](#why-did-my-spark-application-fail-with-an-outofmemoryerror) (Perché l'applicazione Spark è terminata con un'eccezione OutOfMemoryError?) per determinare quali configurazioni Spark è necessario impostare e su quali valori.

1. Scegliere **Spark2** dall'elenco dei cluster.

    ![Selezionare un cluster dall'elenco](media/hdinsight-troubleshoot-spark/update-config-1.png)

1. Fare clic sulla scheda **Configurazioni** .

    ![Scegliere la scheda Configurazioni](media/hdinsight-troubleshoot-spark/update-config-2.png)

1. Nell'elenco delle configurazioni scegliere **Custom-spark2-defaults**.

    ![Scegliere custom-spark-defaults](media/hdinsight-troubleshoot-spark/update-config-3.png)

1. Ricercare l'impostazione del valore che si desidera modificare, come **spark.executor.memory**. In questo caso, il valore 4608m è troppo alto.

    ![Selezionare il campo spark.executor.memory](media/hdinsight-troubleshoot-spark/update-config-4.png)

1. Impostare il valore consigliato. In questo caso, il valore consigliato per questa impostazione è 2048.

    ![Cambiare il valore in 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

1. Salvare il valore dell'impostazione, quindi salvare la configurazione. 

    Fare clic su **Save** sulla barra degli strumenti.

    ![Salvare l'impostazione e la configurazione](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    Si riceve una notifica se una delle configurazioni richiede attenzione. Prendere nota delle configurazioni, quindi fare clic su **Proceed Anyway** (Continuare comunque). 

    ![Fare clic su Proceed Anyway (Continuare comunque)](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    Immettere una nota sulle modifiche apportate alla configurazione, quindi fare clic su **Salva**.

    ![Immettere una nota sulle modifiche apportate](media/hdinsight-troubleshoot-spark/update-config-6c.png)

1. Ogni volta che viene salvata una configurazione, viene chiesto di riavviare il servizio. Fare clic su **Restart**.

    ![Fare clic su Restart](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    Confermare il riavvio.

    ![Fare clic su Confirm Restart All (Conferma riavvio completo)](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    È possibile esaminare i processi in esecuzione.

    ![Esaminare i processi in esecuzione](media/hdinsight-troubleshoot-spark/update-config-7c.png)

1. È possibile anche aggiungere configurazioni. Nell'elenco delle configurazioni selezionare **Custom-spark2-defaults**, come nel passaggio 3, quindi scegliere **Aggiungi proprietà**.

    ![Fare clic su Aggiungi proprietà](media/hdinsight-troubleshoot-spark/update-config-8.png)

1. Definire una nuova proprietà. È possibile definire una singola proprietà usando una finestra di dialogo per configurare impostazioni specifiche, come il tipo di dati, oppure definire più proprietà con una definizione per riga. 

    In questo esempio la proprietà **spark.driver.memory** è stata definita con un valore di 4g.

    ![Definire una nuova proprietà](media/hdinsight-troubleshoot-spark/update-config-9.png)

1. Salvare la configurazione e riavviare il servizio come descritto nei passaggi 6 e 7.

Queste modifiche si applicano a tutto il cluster ma possono essere ignorate al momento effettivo dell'invio del processo Spark.

### <a name="further-reading"></a>Altre informazioni:

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters"></a>Procedura per configurare un'applicazione Spark tramite il notebook di Jupyter nei cluster

### <a name="issue"></a>Problema:

Necessità di configurare la quantità di memoria e il numero di core che possono essere usati da un'applicazione Spark quando si usa il notebook Jupyter nei cluster HDInsight. 

1. Fare riferimento all'argomento [Why did my Spark application fail with OutOfMemoryError?](#spark-application-failure-outofmemory) (Perché l'applicazione Spark è terminata con un'eccezione OutOfMemoryError?) per determinare quali configurazioni Spark devono essere impostate e su quali valori.
1.  Specificare le configurazioni Spark in un formato JSON valido nella prima cella del notebook di Jupyter dopo la direttiva %%configure (modificare il valore effettivo secondo necessità): 

>![Aggiungere una configurazione](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="further-reading"></a>Altre informazioni:

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-livy-on-clusters"></a>Procedura per configurare un'applicazione Spark nei cluster tramite LIVY

### <a name="issue"></a>Problema:

Necessità di configurare, al momento dell'invio tramite LIVY, la quantità di memoria e il numero di core che un'applicazione Spark può usare nei cluster HDInsight. 

1. Fare riferimento all'argomento [Why did my Spark application fail with OutOfMemoryError?](#spark-application-failure-outofmemory) (Perché l'applicazione Spark è terminata con un'eccezione OutOfMemoryError?) per determinare quali configurazioni Spark devono essere impostate e su quali valori.
1. Inviare l'applicazione Spark a LIVY usando un client REST, come CURL, con un comando simile al seguente (modificare i valori effettivi secondo necessità):

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

### <a name="further-reading"></a>Altre informazioni:

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-through-spark-submit-on-clusters"></a>Procedura per configurare un'applicazione Spark tramite un script spark-submit nei cluster

### <a name="issue"></a>Problema: 

Necessità di configurare, al momento dell'invio tramite uno script spark-submit, la quantità di memoria e il numero di core che un'applicazione Spark può usare nei cluster HDInsight.

1. Fare riferimento all'argomento [Why did my Spark application fail with OutOfMemoryError?](#spark-application-failure-outofmemory) (Perché l'applicazione Spark è terminata con un'eccezione OutOfMemoryError?) per determinare quali configurazioni Spark devono essere impostate e su quali valori.
1. Avviare spark-shell usando un comando simile al seguente (modificare il valore effettivo delle configurazioni secondo necessità): 

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="further-reading"></a>Altre informazioni:

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)



## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Causa dell'eccezione OutOfMemoryError in un'applicazione Spark

### <a name="error"></a>Error:

Spark application failed with OutOfMemoryError exception (Applicazione Spark terminata con un'eccezione OutOfMemoryError).

### <a name="detailed-description"></a>Descrizione dettagliata:

L'applicazione Spark termina con un errore quando si verificano i seguenti tipi di eccezioni non rilevate.  
```apache
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

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

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

### <a name="probable-cause"></a>Possibile causa:

Questa eccezione è probabilmente dovuta a una quantità insufficiente di memoria heap allocata per le Java Virtual Machine (JVM) che vengono avviate come executor o driver dell'applicazione Spark. 

### <a name="resolution-steps"></a>Procedura per la risoluzione:

1. Determinare le dimensioni massime dei dati che possono essere gestiti dall'applicazione Spark. Un'ipotesi può basarsi sulle dimensioni massime dei dati di input, i dati intermedi prodotti trasformando i dati di input e i dati di output ottenuti dalla trasformazione ulteriore dei dati intermedi. Può trattarsi di un processo iterativo anche se l'ipotesi iniziale non è possibile. 
1. Assicurarsi che il cluster HDInsight da utilizzare disponga di sufficienti risorse in termini di memoria e core per supportare l'applicazione Spark. Ciò può essere determinato visualizzando la sezione Cluster Metrics (Metriche cluster) dell'interfaccia utente YARN del cluster e confrontando i valori di Memory Used (memoria usata) e Memory Total (memoria totale), VCores Used (VCore utilizzati) e VCores Total (VCore totali).

1. Impostare le seguenti configurazioni Spark su valori appropriati che non superino il 90% della memoria e dei core disponibili, visualizzati da YARN, verificando che siano compresi entro i requisiti di memoria di Spark: 

```apache
spark.executor.instances (Example: 8 for 8 executor count) 
spark.executor.memory (Example: 4g for 4 GB) 
spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
spark.executor.cores (Example: 2 for 2 cores per executor) 
spark.driver.memory (Example: 8g for 8GB) 
spark.driver.cores (Example: 4 for 4 cores)   
spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
```

Memoria totale usata da tutti gli esecutori = 
```apache
spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
```
Memoria totale usata dal driver = 
```apache
spark.driver.memory + spark.yarn.driver.memoryOverhead
```

### <a name="further-reading"></a>Altre informazioni:

- [Spark memory management overview (Panoramica della gestione della memoria Spark)](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Debugging Spark application on HDInsight clusters (Eseguire il debug dell'applicazione Spark nei cluster HDInsight)](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)










