---
title: Risolvere i problemi di Spark tramite Azure HDInsight | Microsoft Docs
description: Risposte alle domande frequenti sull'uso di Apache Spark e Azure HDInsight.
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
ms.date: 8/31/2017
ms.author: arijitt
ms.openlocfilehash: 84b78fe4eb60162f3cad0ae4ae7f98864d5bbf2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Risolvere i problemi di Spark tramite Azure HDInsight

Informazioni sui problemi principali che possono verificarsi quando si usano i payload di Apache Spark in Apache Ambari unitamente alle risoluzioni.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>Come configurare un'applicazione Spark tramite Ambari nei cluster

### <a name="resolution-steps"></a>Procedura per la risoluzione

I valori di configurazione per questa procedura sono stati impostati in precedenza in HDInsight. Per determinare le configurazioni di Spark da impostare e i rispettivi valori, vedere [Causa dell'eccezione OutOfMemoryError in un'applicazione Spark](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. Nell'elenco di cluster selezionare **Spark2**.

    ![Selezionare un cluster dall'elenco](media/hdinsight-troubleshoot-spark/update-config-1.png)

2. Selezionare la scheda **Configs** (Configurazioni).

    ![Selezionare la scheda Configs (Configurazioni)](media/hdinsight-troubleshoot-spark/update-config-2.png)

3. Nell'elenco delle configurazioni selezionare **Custom-spark2-defaults**.

    ![Selezionare custom-spark-defaults](media/hdinsight-troubleshoot-spark/update-config-3.png)

4. Ricercare l'impostazione del valore che si desidera modificare, come **spark.executor.memory**. In questo caso il valore **4608m** è troppo alto.

    ![Selezionare il campo spark.executor.memory](media/hdinsight-troubleshoot-spark/update-config-4.png)

5. Configurare il valore sull'impostazione consigliata. Il valore **2048m** è consigliato per questa impostazione.

    ![Cambiare il valore in 2048m](media/hdinsight-troubleshoot-spark/update-config-5.png)

6. Salvare il valore, quindi salvare la configurazione. Sulla barra degli strumenti selezionare**Save** (Salva).

    ![Salvare l'impostazione e la configurazione](media/hdinsight-troubleshoot-spark/update-config-6a.png)

    Si riceve una notifica se una delle configurazioni richiede attenzione. Annotare gli elementi e quindi selezionare **Proceed Anyway** (Continuare comunque). 

    ![Selezionare Proceed Anyway (Continuare comunque)](media/hdinsight-troubleshoot-spark/update-config-6b.png)

    Immettere una nota sulle modifiche apportate alla configurazione, quindi selezionare **Save** (Salva).

    ![Immettere una nota sulle modifiche apportate](media/hdinsight-troubleshoot-spark/update-config-6c.png)

7. Ogni volta che viene salvata una configurazione, viene chiesto di riavviare il servizio. Selezionare **Restart** (Riavvia).

    ![Selezionare Restart (Riavvia)](media/hdinsight-troubleshoot-spark/update-config-7a.png)

    Confermare il riavvio.

    ![Selezionare la conferma del riavvio completo](media/hdinsight-troubleshoot-spark/update-config-7b.png)

    È possibile esaminare i processi in esecuzione.

    ![Esaminare i processi in esecuzione](media/hdinsight-troubleshoot-spark/update-config-7c.png)

8. È possibile aggiungere configurazioni. Nell'elenco delle configurazioni selezionare **Custom-spark2-defaults** e quindi selezionare **Add Property** (Aggiungi proprietà).

    ![Selezionare Add property (Aggiungi proprietà)](media/hdinsight-troubleshoot-spark/update-config-8.png)

9. Definire una nuova proprietà. È possibile definire una singola proprietà usando una finestra di dialogo per impostazioni specifiche, ad esempio il tipo di dati. In alternativa, è possibile definire più proprietà usando una definizione per riga. 

    In questo esempio la proprietà **spark.driver.memory** è stata definita con un valore di **4g**.

    ![Definire una nuova proprietà](media/hdinsight-troubleshoot-spark/update-config-9.png)

10. Salvare la configurazione e quindi riavviare il servizio come descritto nei passaggi 6 e 7.

Queste modifiche si applicano a tutto il cluster ma è possibile eseguirne l'override quando si invia il processo Spark.

### <a name="additional-reading"></a>Informazioni aggiuntive

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Come configurare un'applicazione Spark tramite un notebook di Jupyter nei cluster

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Per determinare le configurazioni di Spark da impostare e i rispettivi valori, vedere [Causa dell'eccezione OutOfMemoryError in un'applicazione Spark](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Nella prima cella del notebook Jupyter, dopo la direttiva **%%configure**, specificare le configurazioni di Spark in un formato JSON valido. Modificare i valori effettivi in base alla necessità:

    ![Aggiungere una configurazione](media/hdinsight-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Informazioni aggiuntive

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>Come configurare un'applicazione Spark tramite Livy nei cluster

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Per determinare le configurazioni di Spark da impostare e i rispettivi valori, vedere [Causa dell'eccezione OutOfMemoryError in un'applicazione Spark](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Inviare l'applicazione Spark a Livy usando un client REST come cURL. Usare un comando simile al seguente. Modificare i valori effettivi in base alla necessità:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Informazioni aggiuntive

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>Come configurare un'applicazione Spark tramite spark-submit nei cluster

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Per determinare le configurazioni di Spark da impostare e i rispettivi valori, vedere [Causa dell'eccezione OutOfMemoryError in un'applicazione Spark](#what-causes-a-spark-application-outofmemoryerror-exception).

2. Avviare spark-shell usando un comando simile al seguente. Modificare il valore effettivo delle configurazioni in base alla necessità: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Informazioni aggiuntive

[Invio del processo Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>Causa dell'eccezione OutOfMemoryError in un'applicazione Spark

### <a name="detailed-description"></a>Descrizione dettagliata

L'applicazione Spark termina con un errore quando si verificano i seguenti tipi di eccezioni non rilevate:

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

### <a name="probable-cause"></a>Possibile causa

La causa più probabile per questa eccezione è costituita dall'allocazione di memoria heap insufficiente alle macchine virtuali Java (JVM). Queste macchine virtuali Java vengono avviate come executor o driver come parte dell'applicazione Spark. 

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Determinare le dimensioni massime dei dati che possono essere gestiti dall'applicazione Spark. È possibile ottenere una stima in base alle dimensioni massime dei dati di input, i dati intermedi prodotti tramite la trasformazione dei dati di input e i dati di output prodotti quando l'applicazione trasforma ulteriormente i dati intermedi. Questo processo può essere iterativo se non si riesce a ottenere una stima iniziale formale. 

2. Assicurarsi che il cluster HDInsight da usare abbia risorse sufficienti in termini di memoria e di core per l'applicazione Spark. Per eseguire questa verifica, visualizzare la sezione del cluster relativa alle metriche nell'interfaccia utente YARN e confrontare i valori di **Memory Used** (Memoria in uso) e **Memory Total** (Memoria totale) e i valori di **VCores Used** (VCore in uso) e **VCores Total** (VCore totali).

3. Impostare i valori appropriati per le configurazioni seguenti di Spark, che non devono superare il 90% della memoria e dei core disponibili. I valori devono essere compresi nei requisiti di memoria dell'applicazione Spark: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Per calcolare la memoria totale usata da tutti gli esecutori: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Per calcolare la memoria totale usata dal driver:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Informazioni aggiuntive

- [Spark memory management overview (Panoramica della gestione della memoria Spark)](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Debug a Spark application on an HDInsight cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/) (Eseguire il debug di un'applicazione Spark in un cluster HDInsight)

