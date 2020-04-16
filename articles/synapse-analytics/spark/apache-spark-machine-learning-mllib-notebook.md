---
title: Creare un'app di apprendimento automatico con Apache Spark MLlib e Azure Synapse Analytics
description: Informazioni su come usare Apache Spark MLlib per creare un'app di apprendimento automatico che analizza un set di dati usando la classificazione tramite regressione logistica.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430006"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Creare un'app di apprendimento automatico con Apache Spark MLlib e Azure Synapse Analytics

In questo articolo viene illustrato come usare Apache Spark MLlib per creare un'applicazione di Apprendimento automatico che esegue un'analisi predittiva semplice in un set di dati aperto di Azure.In this article, you learn how to use Apache Spark [MLlib](https://spark.apache.org/mllib/) to create a machine learning application that does simple predictive analysis on an Azure open dataset. Spark fornisce librerie di apprendimento automatico integrate. In questo esempio viene utilizzata *la classificazione* tramite regressione logistica.

MLlib è una libreria Spark di base che fornisce molte utilità utili per le attività di apprendimento automatico, incluse le utilità adatte per:MLlib is a core Spark library that provides many utilities that are useful for machine learning tasks, including utilities that are suitable for:

- Classificazione
- Regressione
- Clustering
- Modellazione di argomenti
- Scomposizione di valori singolari e analisi in componenti principali
- Testing e calcolo ipotetici di statistiche di esempio

## <a name="understand-classification-and-logistic-regression"></a>Informazioni sulla classificazione e la regressione logistica

La *classificazione*, un'attività comune di apprendimento automatico, è il processo di ordinamento dei dati in categorie. È compito di un algoritmo di classificazione capire come assegnare *etichette* ai dati di input forniti. Ad esempio, è possibile pensare a un algoritmo di apprendimento automatico che accetta le informazioni sulle azioni come input e divide le azioni in due categorie: le azioni che è necessario vendere e le scorte che è necessario mantenere.

*La regressione logistica* è un algoritmo che è possibile utilizzare per la classificazione. L'API di regressione logistica di Spark è utile per la *classificazione binaria*, ovvero la classificazione di dati di input in uno di due gruppi. Per altre informazioni sulle regressioni logistiche, vedere [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Riepilogando, il processo di regressione logistica genera una *funzione logistica* che può essere usata per stimare la probabilità che un vettore di input appartenga a un gruppo o all'altro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Esempio di analisi predittiva sui dati di NYC Taxi

In questo esempio si usa Spark per eseguire alcune analisi predittive sui dati dei suggerimenti per viaggi in taxi da New York. I dati sono disponibili tramite [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo sottoinsieme del set di dati contiene informazioni sui viaggi in taxi gialli, incluse informazioni su ogni viaggio, l'ora di inizio e di fine e le posizioni, il costo e altri attributi interessanti.

> [!IMPORTANT]
> Potrebbero essere previsti costi aggiuntivi per estrarre questi dati dalla posizione di archiviazione.

Nei passaggi seguenti viene sviluppato un modello per prevedere se un determinato viaggio include o meno un suggerimento.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Creare una pipeline di apprendimento automatico MLlib Apache Spark

1. Creare un notebook utilizzando il kernel PySpark. Per istruzioni, consultate [Creare un blocco appunti.](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook)
2. Importare i tipi richiesti per l'applicazione. Copiare e incollare il codice riportato di seguito in una cella vuota, quindi premere **MAIUSC e INVIO**oppure eseguire la cella utilizzando l'icona di riproduzione blu a sinistra del codice.

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

    Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creato automaticamente quando si esegue la prima cella di codice.

## <a name="construct-the-input-dataframe"></a>Creare il frame di dati di input

Poiché i dati non elaborati sono in formato Parquet, è possibile usare il contesto Spark per estrarre direttamente il file come frame di dati. Mentre il codice seguente utilizza le opzioni predefinite, è possibile forzare il mapping dei tipi di dati e altri attributi dello schema, se necessario.

1. Eseguire le righe seguenti per creare un frame di dati Spark incollando il codice in una nuova cella. La prima sezione assegna le informazioni di accesso all'archiviazione di Azure alle variabili. La seconda sezione consente a Spark di leggere dall'archiviazione BLOB in remoto. L'ultima riga di codice legge il parquet, ma a questo punto non viene caricato alcun dato.

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

2. Il pull di tutti questi dati genera circa 1,5 miliardi di righe. A seconda delle dimensioni del pool Spark (anteprima), i dati non elaborati potrebbero essere troppo grandi o richiedere troppo tempo per operare. È possibile filtrare questi dati in modo più piccolo. Se necessario, aggiungere le righe seguenti per filtrare i dati a circa 2 milioni di righe per un'esperienza più reattiva. Usare questi parametri per estrarre una settimana di dati.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Lo svantaggio del semplice filtraggio è che, da una prospettiva statistica, può introdurre distorsioni nei dati. Un altro approccio consiste nell'utilizzare il campionamento integrato in Spark. Il codice seguente riduce il set di dati fino a circa 2000 righe, se applicato dopo il codice precedente. Questo passaggio di campionamento può essere utilizzato al posto del filtro semplice o in combinazione con il filtro semplice.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Ora è possibile esaminare i dati per vedere cosa è stato letto. In genere è preferibile esaminare i dati con un subset anziché il set completo a seconda delle dimensioni del set di dati. Il codice seguente offre due modi per visualizzare i dati: il primo è di base e il secondo fornisce un'esperienza di griglia molto più ricca, nonché la capacità di visualizzare i dati graficamente.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. A seconda delle dimensioni del set di dati generato e della necessità di sperimentare o eseguire il blocco appunti più volte, potrebbe essere consigliabile memorizzare il set di dati nella cache locale nell'area di lavoro. Esistono tre modi per eseguire la memorizzazione nella cache esplicita:There are three ways to do perform explicit caching:

   - Salvare il frame di dati in locale come file
   - Salvare il frame di dati come tabella o vista temporanea
   - Salvare il frame di dati come tabella permanente

I primi 2 di questi approcci sono inclusi negli esempi di codice seguenti.

La creazione di una tabella o vista temporanea fornisce percorsi di accesso diversi ai dati, ma dura solo per la durata della sessione dell'istanza Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Informazioni sui dati

Normalmente si passerebbe attraverso una fase di *analisi esplorativa* dei dati (EDA) a questo punto per sviluppare una comprensione dei dati. Il codice seguente mostra tre diverse visualizzazioni dei dati correlati ai suggerimenti che portano a conclusioni sullo stato e sulla qualità dei dati.

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

![Grafico a](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![dispersione del grafico a dispersione del grafico con riquadro di istogramma](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Preparazione dei dati

I dati nella sua forma grezza spesso non sono adatti per il passaggio diretto a un modello. È necessario eseguire una serie di azioni sui dati per portarlo in uno stato in cui il modello può utilizzarli.

Nel codice seguente vengono eseguite quattro classi di operazioni:

- Rimozione di outlier/valori non corretti tramite il filtro.
- La rimozione delle colonne, che non sono necessarie.
- La creazione di nuove colonne derivate dai dati non elaborati per rendere il modello più efficace, a volte chiamato featurization.
- Etichettatura, come si sta intraprendendo la classificazione binaria (ci sarà un suggerimento o non in un determinato viaggio) c'è la necessità di convertire l'importo di suggerimento in un valore 0 o 1.

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

L'ultima attività è la conversione dei dati con etichetta in un formato che possa essere analizzato dalla regressione logistica. L'input per un algoritmo di regressione logistica deve essere un set di *coppie vettoriali label-feature*, in cui il *vettore di entità geografiche* è un vettore di numeri che rappresentano il punto di input. Quindi, dobbiamo convertire le colonne categoriche in numeri. Le `trafficTimeBins` `weekdayString` colonne e devono essere convertite in rappresentazioni integer. Esistono diversi approcci per eseguire la conversione, tuttavia l'approccio adottato in questo esempio è *OneHotEncoding*, un approccio comune.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Il risultato è un nuovo frame di dati con tutte le colonne nel formato corretto per eseguire il training di un modello.

## <a name="train-a-logistic-regression-model"></a>Eseguire il training di un modello di regressione logisticaTrain a logistic regression model

La prima attività consiste nel suddividere il set di dati in un set di training e in un set di test o convalida. La divisione qui è arbitraria e si dovrebbe giocare con diverse impostazioni di divisione per vedere se influenzano il modello.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Ora che sono presenti due frame di dati, l'attività successiva consiste nel creare la formula del modello ed eseguirla nel frame di dati di training, quindi eseguire la convalida in base al frame di dati di test. È consigliabile sperimentare con versioni diverse della formula del modello per vedere l'impatto di diverse combinazioni.

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

È ora possibile creare una visualizzazione finale per comprendere meglio i risultati di questo test. Una [curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) è un modo per rivedere il risultato.

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

![Curva ROC per il modello di punta di regressione logistica](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Curva ROC per il modello di punta di regressione logistica")

## <a name="shut-down-the-spark-instance"></a>Arrestare l'istanza di Spark

Al termine dell'esecuzione dell'applicazione, arrestare il blocco appunti per rilasciare le risorse chiudendo la scheda o selezionare **Termina sessione** dal riquadro di stato nella parte inferiore del blocco appunti.

## <a name="see-also"></a>Vedere anche

- [Panoramica: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- [.NET per la documentazione di Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione ufficiale di Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Alcune delle documentazioni ufficiali di Apache Spark si basasullivano sull'uso della console Spark, che non è disponibile in Azure Synapse Spark.Some of the official Apache Spark documentation relies on using the Spark console, which is not available on Azure Synapse Spark. Usare invece il [blocco appunti](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) o le esperienze [IntelliJ.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
