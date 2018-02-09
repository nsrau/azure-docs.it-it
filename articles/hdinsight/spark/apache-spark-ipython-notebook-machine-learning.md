---
title: Compilare applicazioni di Machine Learning Apache Spark in Azure HDInsight | Microsoft Docs
description: Istruzioni dettagliate su come compilare applicazioni di Machine Learning Apache Spark nei cluster HDInsight Spark usando il notebook di Jupyter
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 74dcd368d8696df26c5ad294c5657161fbe7f408
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Compilare applicazioni di Machine Learning Apache Spark in Azure HDInsight

Informazioni su come compilare un'applicazione di Machine Learning Apache Spark usando un cluster Spark in HDInsight. Questo articolo illustra come usare il notebook di Jupyter disponibile con il cluster per compilare e testare questa applicazione. L'applicazione usa i dati di HVAC.csv di esempio disponibili in tutti i cluster per impostazione predefinita.

[MLib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) è la libreria di Machine Learning scalabile di Spark costituita da utilità e algoritmi di apprendimento comuni, tra cui classificazione, regressione, clustering, filtro collaborativo, riduzione della dimensionalità, nonché primitive di ottimizzazione sottostanti.

**Prerequisiti:**

È necessario disporre di quanto segue:

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Informazioni sul set di dati

I dati seguenti illustrano la temperatura di destinazione e la temperatura effettiva di alcuni edifici con sistemi HVAC installati. La colonna **System** rappresenta l'ID del sistema e la colonna **SystemAge** il numero di anni in cui il sistema HVAC è stato installato nell'edificio. I dati di questa esercitazione vengono usati per stimare se un edificio è caldo o freddo in base alla temperatura di destinazione, in base a un ID di sistema e agli anni di utilizzo del sistema.

![Snapshot dei dati usati per l'esempio Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Snapshot dei dati usati per l'esempio Machine Learning Spark")

Il file di dati **HVAC.csv** si trova in **\HdiSamples\HdiSamples\SensorSampleData\hvac** in tutti i cluster HDInsight.

## <a name="app"></a>Scrivere un'applicazione di Machine Learning Spark usando Spark MLlib
In questa applicazione si usa una [pipeline ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) di Spark per eseguire una classificazione di documento. Le pipeline ML offrono un set uniforme di API di alto livello basate su frame di dati che consentono agli utenti di creare e ottimizzare pipeline pratiche di Machine Learning. Nella pipeline il documento è stato suddiviso in parole, le parole sono state convertite in un vettore di funzionalità numerico e infine è stato creato un modello di stima usando le etichette e i vettori di funzionalità. Procedere come descritto di seguito per creare l'applicazione.

1. Creare un notebook di Jupyter usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Importare i tipi richiesti per questo scenario. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC+INVIO**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. Caricare i dati (hvac.csv), analizzarli e usarli per eseguire il training del modello. 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    Nel frammento di codice definire una funzione che confronta la temperatura effettiva con quella di destinazione. Se la temperatura effettiva è maggiore, l’edificio è caldo ed è contrassegnato dal valore **1.0**. In caso contrario, l'edificio è freddo ed è contrassegnato dal valore **0.0**. 

4. Configurare la pipeline di machine learning Spark che è costituita da tre fasi: tokenizer, hashingTF, e lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Per altre informazioni su una pipeline e funzionamento vedere <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">pipeline machine learning di Spark</a>.

5. Adattare la pipeline al documento di formazione.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Verificare il documento di formazione per controllare lo stato di avanzamento con l'applicazione.
   
    ```PySpark
    training.show()
    ```
   
    Questo dovrebbe fornire un output simile al seguente:

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Confrontare l'output con il file CSV non elaborato. Ad esempio, la prima riga del file con estensione csv ha dati:

    ![Snapshot dei dati di output per l'esempio Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Snapshot dei dati di output per l'esempio Machine Learning Spark")

    Si noti come la temperatura effettiva è inferiore alla temperatura di destinazione. Questo dato indica che l'edificio è freddo. Nell'output di training, il valore per **label** nella prima riga è **0.0**, che indica che l'edificio non è caldo.

7. Preparazione per l'esecuzione del training modello rispetto a un set di dati. A tale scopo, passare un ID di sistema e gli anni di utilizzo del sistema (indicati come **SystemInfo** nell'output di training) in modo che il modello possa prevedere se l'edificio con queste caratteristiche sarà più caldo (contrassegnato da 1.0) o più freddo (contrassegnato da 0.0).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Infine, eseguire stime sui dati di test. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    L'output dovrebbe essere simile al seguente:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Nella prima riga di stima, si noterà che per un sistema HVAC con ID 20 e validità del sistema di 25 anni, l’edificio sarà caldo (**stima = 1.0**). Il primo valore per DenseVector (0.49999) corrisponde alla stima 0.0 e il secondo valore (0.5001) corrisponde alla stima 1.0. Nell'output, anche se il secondo valore è solo leggermente superiore, viene illustrato il modello **stima=1.0**.
10. Arrestare il notebook per rilasciare le risorse. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Questa operazione consente di arrestare e chiudere il notebook.

## <a name="anaconda"></a>Usare la libreria Anaconda scikit-learn per Machine Learning Spark
I cluster Apache Spark in HDInsight includono librerie Anaconda. Include inoltre la libreria **scikit-informazioni** per machine learning. La libreria include inoltre diversi set di dati che è possibile usare per compilare applicazioni di esempio direttamente da un notebook di Jupyter. Per esempi sull'uso della libreria scikit-learn, vedere [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
