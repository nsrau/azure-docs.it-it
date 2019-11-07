---
title: "Esercitazione: Compilare un'app di apprendimento automatico Spark - Azure HDInsight"
description: Esercitazione - Istruzioni dettagliate su come compilare applicazioni di Machine Learning Apache Spark nei cluster HDInsight Spark usando Jupyter notebook.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: 6e46d7403e251bccd69467cfcdaa1d5073b4e454
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494557"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Esercitazione: Compilare un'applicazione di apprendimento automatico Apache Spark in Azure HDInsight

In questa esercitazione si apprenderà come usare [Jupyter Notebook](https://jupyter.org/) per creare un'applicazione di apprendimento automatico [Apache Spark](https://spark.apache.org/) per Azure HDInsight.

[MLib](https://spark.apache.org/docs/latest/ml-guide.html) è la libreria di Machine Learning scalabile di Spark costituita da utilità e algoritmi di apprendimento comuni, tra cui classificazione, regressione, clustering, filtro collaborativo, riduzione della dimensionalità, nonché primitive di ottimizzazione sottostanti.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Sviluppare un'applicazione di apprendimento automatico Apache Spark

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Vedere [Creare un cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiarità nell'uso di Jupyter Notebook con Spark in HDInsight. Per altre informazioni, vedere l'articolo su come [caricare i dati ed eseguire query con Apache Spark in HDInsight](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Informazioni sul set di dati

L'applicazione usa i dati di **HVAC.csv** di esempio disponibili in tutti i cluster per impostazione predefinita. Il file si trova in `\HdiSamples\HdiSamples\SensorSampleData\hvac`. I dati illustrano la temperatura di destinazione e la temperatura effettiva di alcuni edifici con sistemi HVAC installati. La colonna **System** rappresenta l'ID del sistema e la colonna **SystemAge** il numero di anni in cui il sistema HVAC è stato installato nell'edificio. Usando i dati è possibile stimare se un edificio è caldo o freddo in base alla temperatura di destinazione, a un ID di sistema e all'età di sistema.

![Snapshot dei dati usati per l'esempio di apprendimento automatico Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Snapshot dei dati usati per l'esempio di apprendimento automatico Spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Sviluppare un'applicazione di Machine Learning Spark usando Spark MLlib

In questa applicazione si usa una [pipeline ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) di Spark per eseguire una classificazione di documento. Le pipeline ML offrono un set uniforme di API di alto livello basate su frame di dati che consentono agli utenti di creare e ottimizzare pipeline pratiche di Machine Learning. Nella pipeline il documento è stato suddiviso in parole, le parole sono state convertite in un vettore di funzionalità numerico e infine è stato creato un modello di stima usando le etichette e i vettori di funzionalità. Procedere come descritto di seguito per creare l'applicazione.

1. Creare un notebook di Jupyter usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

1. Importare i tipi richiesti per questo scenario. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC+INVIO**.

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

1. Caricare i dati (hvac.csv), analizzarli e usarli per eseguire il training del modello.

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
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    Nel frammento di codice definire una funzione che confronta la temperatura effettiva con quella di destinazione. Se la temperatura effettiva è maggiore, l’edificio è caldo ed è contrassegnato dal valore **1.0**. In caso contrario, l'edificio è freddo ed è contrassegnato dal valore **0.0**.

1. Configurare la pipeline di machine learning Spark che è costituita da tre fasi: tokenizer, hashingTF, e lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Per altre informazioni sulla pipeline e sul suo funzionamento, vedere [Pipeline di apprendimento automatico di Apache Spark](https://spark.apache.org/docs/latest/ml-pipeline.html).

1. Adattare la pipeline al documento di formazione.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Verificare il documento di formazione per controllare lo stato di avanzamento con l'applicazione.

    ```PySpark
    training.show()
    ```

    L'output è simile a:

    ```output
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

    ![Snapshot dei dati di output per l'esempio di apprendimento automatico Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Snapshot dei dati di output per l'esempio di apprendimento automatico Spark")

    Si noti come la temperatura effettiva è inferiore alla temperatura di destinazione. Questo dato indica che l'edificio è freddo. Nell'output di training, il valore per **label** nella prima riga è **0.0**, che indica che l'edificio non è caldo.

1. Preparazione per l'esecuzione del training modello rispetto a un set di dati. A tale scopo, passare un ID di sistema e gli anni di utilizzo del sistema (indicati come **SystemInfo** nell'output di training) in modo che il modello possa prevedere se l'edificio con queste caratteristiche sarà più caldo (contrassegnato da 1.0) o più freddo (contrassegnato da 0.0).

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

1. Infine, eseguire stime sui dati di test.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    L'output è simile a:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Nella prima riga di stima, si noterà che per un sistema HVAC con ID 20 e 25 anni di utilizzo 25 anni, l’edificio è caldo (**stima = 1.0**). Il primo valore per DenseVector (0.49999) corrisponde alla stima 0.0 e il secondo valore (0.5001) corrisponde alla stima 1.0. Nell'output, anche se il secondo valore è solo leggermente superiore, viene illustrato il modello **stima=1.0**.

1. Arrestare il notebook per rilasciare le risorse. Per fare ciò, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Questa azione spegne e chiude il notebook.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Usare la libreria Anaconda scikit-learn per Machine Learning Spark

I cluster Apache Spark in HDInsight includono librerie Anaconda. Include inoltre la libreria **scikit-learn** per Machine Learning. La libreria include inoltre diversi set di dati che è possibile usare per compilare applicazioni di esempio direttamente da un notebook Jupyter. Per esempi sull'uso della libreria scikit-learn, vedere [ https://scikit-learn.org/stable/auto_examples/index.html ](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il cluster creato con i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nella casella **Ricerca** in alto digitare **HDInsight**.

1. Selezionare **Cluster HDInsight** in **Servizi**.

1. Nell'elenco di cluster HDInsight visualizzato selezionare **...** accanto al cluster creato per questa esercitazione.

1. Selezionare **Elimina**. Selezionare **Sì**.

![Eliminazione di un cluster HDInsight nel portale di Azure](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "Eliminare un cluster HDInsight")

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Jupyter Notebook per creare un'applicazione di Machine Learning Apache Spark per Azure HDInsight. Passare all'esercitazione successiva per imparare a usare la rete IntelliJ IDEA per i processi Spark.

> [!div class="nextstepaction"]
> [Creare un'applicazione di Scala Maven usando IntelliJ](./apache-spark-create-standalone-application.md)
