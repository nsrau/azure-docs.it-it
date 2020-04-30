---
title: Creare un'app di Machine Learning con Apache Spark MLlib e Azure sinapsi Analytics
description: Informazioni su come usare Apache Spark MLlib per creare un'app di Machine Learning che analizza un set di dati usando la classificazione tramite la regressione logistica.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 25d11d2cf41f8653c5a54007f121c1251bb24b1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096300"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Creare un'app di Machine Learning con Apache Spark MLlib e Azure sinapsi Analytics

Questo articolo illustra come usare Apache Spark [MLlib](https://spark.apache.org/mllib/) per creare un'applicazione di Machine Learning che esegue un'analisi predittiva semplice su un set di dati aperto di Azure. Spark fornisce librerie di Machine Learning predefinite. In questo esempio viene usata la *classificazione* tramite la regressione logistica.

MLlib è una libreria Spark di base che offre molte utilità utili per le attività di Machine Learning, incluse le utilità adatte a:

- Classificazione
- Regressione
- Clustering
- Modellazione di argomenti
- Scomposizione di valori singolari e analisi in componenti principali
- Testing e calcolo ipotetici di statistiche di esempio

## <a name="understand-classification-and-logistic-regression"></a>Informazioni sulla classificazione e la regressione logistica

La *classificazione*, un'attività comune di apprendimento automatico, è il processo di ordinamento dei dati in categorie. È compito di un algoritmo di classificazione determinare come assegnare *etichette* ai dati di input forniti dall'utente. Ad esempio, è possibile pensare a un algoritmo di Machine Learning che accetta informazioni sulle azioni come input e divide le scorte in due categorie: le scorte da vendere e le scorte che è opportuno tenere.

La *regressione logistica* è un algoritmo che è possibile usare per la classificazione. L'API di regressione logistica di Spark è utile per la *classificazione binaria*, ovvero la classificazione di dati di input in uno di due gruppi. Per altre informazioni sulle regressioni logistiche, vedere [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Riepilogando, il processo di regressione logistica genera una *funzione logistica* che può essere usata per stimare la probabilità che un vettore di input appartenga a un gruppo o all'altro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Esempio di analisi predittiva sui dati dei taxi di NYC

In questo esempio, si usa Spark per eseguire un'analisi predittiva dei dati di Tip Trip in taxi di New York. I dati sono disponibili tramite i set di dati [aperti di Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo subset del set di dati contiene informazioni sulle corse di taxi gialle, incluse le informazioni su ogni corsa, l'ora di inizio e di fine e le località, il costo e altri attributi interessanti.

> [!IMPORTANT]
> Potrebbero essere previsti addebiti aggiuntivi per il pull dei dati dal percorso di archiviazione.

Nei passaggi seguenti si sviluppa un modello per stimare se una particolare corsa include una mancia o meno.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Creare una pipeline di apprendimento automatico MLlib Apache Spark

1. Creare un notebook usando il kernel PySpark. Per le istruzioni, vedere [creare un notebook](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importare i tipi richiesti per l'applicazione. Copiare e incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**oppure eseguire la cella usando l'icona di riproduzione Blu a sinistra del codice.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto di Spark viene creato automaticamente quando si esegue la prima cella di codice.

## <a name="construct-the-input-dataframe"></a>Creare il frame di dati di input

Poiché i dati non elaborati sono in formato parquet, è possibile usare il contesto Spark per eseguire direttamente il pull del file in memoria come frame di dati. Mentre nel codice riportato di seguito vengono utilizzate le opzioni predefinite, è possibile forzare il mapping dei tipi di dati e di altri attributi dello schema, se necessario.

1. Eseguire le righe seguenti per creare un dataframe di Spark incollando il codice in una nuova cella. La prima sezione assegna le informazioni di accesso di archiviazione di Azure alle variabili. La seconda sezione consente a Spark di leggere dall'archivio BLOB in remoto. L'ultima riga di codice legge parquet, ma a questo punto non sono stati caricati dati.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Il pull di tutti questi dati genera circa 1,5 miliardi righe. A seconda delle dimensioni del pool Spark (anteprima), i dati non elaborati potrebbero essere troppo grandi o richiedere troppo tempo per funzionare. È possibile filtrare i dati fino a un valore inferiore. Se necessario, aggiungere le righe seguenti per filtrare i dati fino a circa 2 milioni righe per un'esperienza più reattiva. Usare questi parametri per eseguire il pull di una settimana di dati.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Lo svantaggio del semplice filtro è che, dal punto di vista statistico, può introdurre distorsioni nei dati. Un altro approccio consiste nell'usare il campionamento incorporato in Spark. Il codice seguente riduce il set di dati fino a circa 2000 righe, se applicato dopo il codice precedente. Questo passaggio di campionamento può essere utilizzato al posto del filtro semplice o insieme al filtro semplice.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. È ora possibile esaminare i dati per vedere cosa è stato letto. In genere è preferibile rivedere i dati con un subset anziché il set completo a seconda delle dimensioni del set di dati. Il codice seguente offre due modi per visualizzare i dati: il primo è Basic e il secondo fornisce un'esperienza di griglia molto più ricca, oltre alla possibilità di visualizzare i dati graficamente.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. A seconda delle dimensioni della dimensione del set di dati generata e della necessità di sperimentare o eseguire il notebook più volte, potrebbe essere consigliabile memorizzare nella cache il set di dati in locale nell'area di lavoro. Esistono tre modi per eseguire la memorizzazione nella cache esplicita:

   - Salvare il frame di frame in locale come file
   - Salvare il frame di frame come vista o tabella temporanea
   - Salvare il frame di frame come tabella permanente

I primi due approcci sono inclusi negli esempi di codice seguenti.

La creazione di una tabella o di una vista temporanea fornisce percorsi di accesso diversi ai dati, ma dura solo per la durata della sessione dell'istanza di Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Informazioni sui dati

In genere, è possibile eseguire una fase di *analisi esplorativa dei dati* (EDA) a questo punto per sviluppare una comprensione dei dati. Il codice seguente mostra tre visualizzazioni diverse dei dati correlati ai suggerimenti che portano a conclusioni sullo stato e sulla qualità dei dati.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Grafico](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![a dispersione](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![tracciato della casella istogramma](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Preparazione dei dati

I dati nel relativo formato non elaborato non sono spesso adatti per il passaggio diretto a un modello. È necessario eseguire una serie di azioni sui dati per ottenerli in uno stato in cui il modello può utilizzarlo.

Nel codice riportato di seguito vengono eseguite quattro classi di operazioni:

- Rimozione dei valori outlier/non corretti tramite il filtro.
- Rimozione delle colonne, che non sono necessarie.
- La creazione di nuove colonne derivate dai dati non elaborati per far funzionare il modello in modo più efficace, talvolta chiamato conteggi.
- Assegnazione di etichette, quando si sta intraprendendo la classificazione binaria (sarà presente una mancia o meno in un determinato viaggio), è necessario convertire l'importo della mancia in un valore 0 o 1.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Viene quindi eseguito un secondo passaggio sui dati per aggiungere le funzionalità finali.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Creare un modello di regressione logistica

L'ultima attività è la conversione dei dati con etichetta in un formato che possa essere analizzato dalla regressione logistica. L'input per un algoritmo di regressione logistica deve essere un set di *coppie di vettori di funzionalità etichetta*, in cui il *vettore di funzionalità* è un vettore di numeri che rappresentano il punto di input. Quindi, è necessario convertire le colonne categoriche in numeri. Le `trafficTimeBins` colonne `weekdayString` e devono essere convertite in rappresentazioni di tipo Integer. Esistono diversi approcci per eseguire la conversione. Tuttavia, l'approccio adottato in questo esempio è *OneHotEncoding*, un approccio comune.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

In questo modo viene generato un nuovo frame di dati con tutte le colonne nel formato corretto per il training di un modello.

## <a name="train-a-logistic-regression-model"></a>Eseguire il training di un modello di regressione logistica

La prima attività consiste nel suddividere il set di dati in un set di training e in un set di testing o di convalida. La divisione qui è arbitraria ed è necessario ricorrere a diverse impostazioni di suddivisione per verificare se influiscano sul modello.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Ora che sono presenti due dataframe, l'attività successiva consiste nel creare la formula del modello ed eseguirla nel frame di training, quindi convalidare i dataframe di testing. È consigliabile provare con versioni diverse della formula del modello per vedere l'effetto di combinazioni diverse.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

L'output di questa cella è

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Creare una rappresentazione visiva della stima

È ora possibile creare una visualizzazione finale per comprendere meglio i risultati di questo test. Una [curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) è un modo per esaminare il risultato.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Curva ROC per il modello di suggerimento di regressione logistica](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Curva ROC per il modello di suggerimento di regressione logistica")

## <a name="shut-down-the-spark-instance"></a>Arrestare l'istanza di Spark

Al termine dell'esecuzione dell'applicazione, arrestare il notebook per rilasciare le risorse chiudendo la scheda o selezionando **Termina sessione** dal pannello stato nella parte inferiore del notebook.

## <a name="see-also"></a>Vedere anche

- [Panoramica: Apache Spark in Azure sinapsi Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione ufficiale di Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Parte della documentazione ufficiale di Apache Spark si basa sull'uso della console Spark, che non è disponibile in Spark di Azure Synapse. Usare al suo posto un [notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) o [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
