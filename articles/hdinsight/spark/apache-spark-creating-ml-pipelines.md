---
title: Creare una pipine di Machine Learning di Apache Spark - HDInsight | Microsoft Docs
description: Usare la libreria di Machine Learning di Apache Spark per creare pipeline di dati.
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
ms.date: 01/19/2018
ms.author: maxluk
ms.openlocfilehash: 238ab5f940fbea836b75e20b015ae16f22eef3e9
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-spark-machine-learning-pipeline"></a>Creare una pipeline di Machine Learning Spark

La libreria scalabile per il Machine Learning (MLlib) di Spark introduce funzionalità di modellazione in un ambiente distribuito. Il pacchetto di Spark [`spark.ml`](http://spark.apache.org/docs/latest/ml-pipeline.html) è un set di API di alto livello basate su DataFrame. Queste API consentono di creare e ottimizzare pipeline di Machine Learning pratiche.  La funzionalità di *Machine Learning di Spark* fa riferimento a questa API basata su DataFrame MLlib e non all'API precedente per pipeline basate su RDD.

Una pipeline di Machine Learning è un flusso di lavoro completo che combina più algoritmi di Machine Learning. Possono essere molti i passaggi richiesti per elaborare i dati e apprendere da essi, che richiedono una sequenza di algoritmi. Le pipeline definiscono le fasi e l'ordinamento di un processo di Machine Learning. In MLlib, le fasi di una pipeline sono rappresentate da una sequenza specifica di PipelineStages, in cui vengono eseguite attività da un trasformatore e da un estimatore.

Un trasformatore è un algoritmo che trasforma un DataFrame in un altro tramite il metodo `transform()`. Ad esempio, un trasformatore di funzionalità potrebbe leggere una colonna di un DataFrame, eseguirne il mapping a un'altra colonna e restituire un nuovo DataFrame con la colonna mappata aggiunta.

Un estimatore è un'astrazione di algoritmi di apprendimento ed è responsabile per l'adattamento o il training di un set di dati per produrre un trasformatore. Un estimatore implementa un metodo denominato `fit()`, che accetta un DataFrame e produce un DataFrame che è un trasformatore.

Ogni istanza senza stato di un trasformatore o un estimatore ha un identificatore univoco, usato per specificare i parametri. Entrambi usano un'API uniforme per specificare questi parametri.

## <a name="pipeline-example"></a>Esempio di pipeline

Per dimostrare un uso pratico di una pipeline di Machine Learning, questo esempio usare il file di dati di esempio `HVAC.csv` precaricato nello spazio di archiviazione predefinito per il cluster HDInsight, ovvero Archiviazione di Azure o Data Lake Store. Per visualizzare il contenuto del file, passare alla directory `/HdiSamples/HdiSamples/SensorSampleData/hvac`. `HVAC.csv` contiene un set di orari con temperatura prevista ed effettiva per sistemi di climatizzazione (*riscaldamento, ventilazione e aria condizionata*) in vari edifici. L'obiettivo è eseguire il training del modello per i dati e generare una temperatura previsionale per un determinato edificio.

Il codice seguente:

1. Definisce un `LabeledDocument`, che archivia `BuildingID`, `SystemInfo` (identificatore ed età del sistema) e `label` (1.0 se l'edificio è troppo caldo, 0.0 in caso contrario).
2. Crea una funzione di analisi personalizzata `parseDocument` che accetta una riga di dati e determina se l'edificio è caldo (hot) confrontando la temperatura prevista con quella effettiva.
3. Applica il parser durante l'estrazione dei dati di origine.
4. Crea dati di training.

```python
# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

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
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Questa pipeline di esempio prevede tre fasi: `Tokenizer` e `HashingTF` (entrambi trasformatori) e `Logistic Regression` (un estimatore).  I dati estratti e analizzati nel DataFrame `training` passano attraverso la pipeline quando viene chiamato `pipeline.fit(training)`.

1. La prima fase, `Tokenizer`, divide la colonna di input `SystemInfo` (costituita dai valori dell'identificatore e dell'età del sistema) in una colonna di output `words`. Questa nuova colonna `words` viene aggiunta al DataFrame. 
2. La seconda fase, `HashingTF`, converte la nuova colonna `words` in vettori di funzionalità. Questa nuova colonna `features` viene aggiunta al DataFrame. Queste prime due fasi sono trasformatori. 
3. La terza fase, `LogisticRegression`, è un estimatore, quindi la pipeline chiama il metodo `LogisticRegression.fit()` per produrre un `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Per vedere le nuove colonne `words` e `features` aggiunte dai trasformatori `Tokenizer` e `HashingTF` e un esempio dell'estimatore `LogisticRegression`, eseguire un metodo `PipelineModel.transform()` sul DataFrame originale. Nel codice di produzione, il passaggio successivo sarebbe il passaggio in un DataFrame di test per convalidare il training.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

L'oggetto `model` può ora essere usato per eseguire stime. Per un esempio completo dell'applicazione di Machine Learning e istruzioni dettagliate per la sua esecuzione, vedere [Compilare applicazioni di Machine Learning Apache Spark in Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Vedere anche 

* [Data science tramite Scala e Spark in Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
