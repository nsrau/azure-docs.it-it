---
title: Creare un'app di Machine Learning con Apache Spark MLlib
description: Informazioni su come usare MLlib di Apache Spark per creare un'app di apprendimento automatico che analizza un set di dati usando una classificazione tramite la regressione logistica.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: e1ece0add7b0749cfd808b0a3ec7962dd43a302d
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719343"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Creare un'app di Machine Learning con MLlib di Apache Spark e Azure Synapse Analytics

Questo articolo illustra come usare [MLlib](https://spark.apache.org/mllib/) di Apache Spark per creare un'applicazione di apprendimento automatico che effettui analisi predittive semplici su un set di dati aperto. Spark offre librerie di apprendimento automatico predefinite. Questo esempio usa la *classificazione* tramite regressione logistica.

MLlib è una libreria Spark di base che offre diverse utilità in grado di agevolare le attività di apprendimento automatico, incluse utilità adatte a:

- Classificazione
- Regressione
- Clustering
- Modellazione di argomenti
- Scomposizione di valori singolari e analisi in componenti principali
- Testing e calcolo ipotetici di statistiche di esempio

## <a name="understand-classification-and-logistic-regression"></a>Informazioni sulla classificazione e la regressione logistica

La *classificazione*, un'attività comune di apprendimento automatico, è il processo di ordinamento dei dati in categorie. È il processo eseguito da un algoritmo di classificazione per determinare come assegnare *etichette* ai dati di input specificati. Si pensi, ad esempio, a un algoritmo di apprendimento automatico che accetta informazioni sulle azioni come input e divide le azioni in due categorie: azioni da vendere e azioni da conservare.

La *regressione logistica* è un algoritmo usato per la classificazione. L'API di regressione logistica di Spark è utile per la *classificazione binaria*, ovvero la classificazione di dati di input in uno di due gruppi. Per altre informazioni sulle regressioni logistiche, vedere [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Riepilogando, il processo di regressione logistica genera una *funzione logistica* che può essere usata per stimare la probabilità che un vettore di input appartenga a un gruppo o all'altro.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Esempio di analisi predittiva di dati relativi alla rete taxi di New York

In questo esempio, viene usato Spark per eseguire un'analisi predittiva dei dati relativi alle mance sui taxi di New York. I dati sono disponibili tramite [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo subset del set di dati contiene informazioni sulle corse in taxi, incluse informazioni su ogni corsa, l'ora di partenza e di arrivo, i percorsi, i costi e altri attributi interessanti.

> [!IMPORTANT]
> Per il pull dei dati dalla posizione di archiviazione potrebbero essere previsti addebiti aggiuntivi.

Nei passaggi seguenti viene sviluppato un modello per stimare se una particolare corsa includa una mancia o meno.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Creare una pipeline di apprendimento automatico MLlib Apache Spark

1. Creare un notebook usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importare i tipi richiesti per l'applicazione. Copiare e incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**, oppure eseguire la cella tramite l'icona di riproduzione blu a sinistra del codice.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creata automaticamente quando si esegue la prima cella di codice.

## <a name="construct-the-input-dataframe"></a>Creare il frame di dati di input

Poiché i dati non elaborati sono in formato Parquet, è possibile usare il contesto di Spark per eseguire direttamente il pull del file in memoria come dataframe. Anche se nel codice riportato di seguito vengono usate le opzioni predefinite, se necessario è possibile forzare il mapping dei tipi di dati e di altri attributi dello schema.

1. Eseguire le righe seguenti per creare un dataframe di Spark incollando il codice in una nuova cella. I dati vengono recuperati tramite l'API set di dati aperti. Il pull di tutti questi dati genera circa 1,5 miliardi righe. A seconda delle dimensioni del pool di Spark (anteprima), i dati non elaborati potrebbero essere troppo grandi o richiedere troppo tempo per poter essere usati. È possibile filtrare i dati fino a ottenere un valore inferiore. Nell'esempio di codice seguente vengono utilizzati start_date e end_date per applicare un filtro che restituisce un singolo mese di dati.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Lo svantaggio della semplice applicazione di un filtro è che, da una prospettiva statistica, può introdurre distorsioni nei dati. Un altro approccio consiste nell'usare il campionamento incorporato in Spark. Il codice seguente riduce il set di dati fino a circa 2000 righe, se applicato dopo il codice precedente. Questo passaggio di campionamento può essere usato al posto del semplice filtro o assieme ad esso.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. È ora possibile esaminare i dati per vedere cosa è stato letto. In genere è preferibile rivedere i dati con un subset anziché il set completo, a seconda delle dimensioni del set di dati. Il codice seguente offre due modi per visualizzare i dati: il primo è un metodo di base, mentre il secondo offre un'esperienza di griglia molto più ricca, oltre alla possibilità di visualizzare i dati graficamente.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. A seconda delle dimensioni del set di dati generato e della necessità di sperimentare o eseguire il notebook più volte, potrebbe essere consigliabile memorizzare nella cache il set di dati in locale nell'area di lavoro. Esistono tre modi per eseguire la memorizzazione nella cache esplicita:

   - Salvare il dataframe in locale come file
   - Salvare il dataframe come tabella o vista temporanea
   - Salvare il dataframe come tabella permanente

Negli esempi di codice seguenti sono inclusi i primi due approcci.

La creazione di una vista o di una tabella temporanea offre percorsi di accesso diversi ai dati, ma è disponibile solo per la durata della sessione dell'istanza di Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Informazioni sui dati

In genere, a questo punto si procede attraverso una fase di *analisi esplorativa dei dati* per sviluppare informazioni sui dati. Il codice seguente mostra tre visualizzazioni diverse dei dati correlate ai suggerimenti, che portano a conclusioni sullo stato e sulla qualità dei dati.

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

# How many passengers tipped by various amounts
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

![Istogramma](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Tracciato a scatola e baffi](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![Tracciato a dispersione](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="prepare-the-data"></a>Preparare i dati

I dati nel relativo formato non elaborato spesso non sono adatti per il passaggio diretto a un modello. È necessario eseguire una serie di azioni sui dati per ottenerli in uno stato in cui il modello possa usarli.

Nel codice riportato di seguito vengono eseguite quattro classi di operazioni:

- Rimozione dei valori outlier/non corretti tramite filtro.
- Rimozione delle colonne non necessarie.
- Creazione di nuove colonne derivate dai dati non elaborati per far funzionare il modello in modo più efficace. Tale operazione è talvolta definita "ingegneria delle funzionalità".
- Assegnazione di etichette: poiché si sta intraprendendo la classificazione binaria (sarà presente una mancia o meno in una determinata corsa), è necessario convertire l'importo della mancia in un valore 0 o 1.

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

L'ultima attività è la conversione dei dati con etichetta in un formato che possa essere analizzato dalla regressione logistica. L'input per un algoritmo di regressione logistica deve essere un set di *coppie etichetta-vettore di funzionalità*, dove il *vettore di funzionalità* è un vettore di numeri che rappresenta il punto di ingresso. Quindi, è necessario convertire le colonne categoriche in numeri. Le colonne `trafficTimeBins` e `weekdayString` devono essere convertite in rappresentazioni di tipo integer. Esistono diversi approcci per eseguire la conversione. L'approccio adottato in questo esempio è *OneHotEncoding*, un approccio comune.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

In questo modo viene generato un nuovo dataframe con tutte le colonne nel formato corretto per eseguire il training di un modello.

## <a name="train-a-logistic-regression-model"></a>Eseguire il training di un modello di regressione logistica

La prima attività consiste nel suddividere il set di dati in un set di training e in un set di test o di convalida. La divisione qui è arbitraria ed è necessario provare diverse impostazioni di suddivisione per verificare se influiscano sul modello.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Ora che sono presenti due dataframe, l'attività successiva consiste nel creare la formula del modello ed eseguirla nel dataframe di training, quindi convalidare il dataframe di test. È consigliabile provare versioni diverse della formula del modello per vedere l'effetto di combinazioni diverse.

> [!Note]
> Per salvare il modello, è necessario il ruolo di Azure collaboratore dati BLOB di archiviazione. Nell'account di archiviazione passare a controllo di accesso (IAM) e selezionare **Aggiungi assegnazione ruolo**. Assegnare al server del database SQL un ruolo di Azure con collaboratore dati BLOB di archiviazione. Solo i membri con il privilegio di proprietario possono eseguire questo passaggio. Per informazioni sui diversi ruoli predefiniti di Azure, vedere questa [guida](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

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

L'output di questa cella è:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Creare una rappresentazione visiva della stima

È ora possibile creare una visualizzazione finale per comprendere meglio i risultati di questo test. Uno dei modi per rivedere i risultati è attraverso una [curva ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic).

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

![Curva ROC per il modello di mancia con regressione logistica](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "Curva ROC per il modello di mancia con regressione logistica")

## <a name="shut-down-the-spark-instance"></a>Arrestare l'istanza di Spark

Al termine dell'esecuzione dell'applicazione, arrestare il notebook per rilasciare le risorse chiudendo la scheda o selezionando **Termina sessione** dal pannello di stato nella parte inferiore del notebook.

## <a name="see-also"></a>Vedere anche

- [Panoramica: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di .NET per Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione ufficiale di Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Parte della documentazione ufficiale di Apache Spark si basa sull'uso della console Spark, che non è disponibile in Spark di Azure Synapse. Usare al suo posto un [notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) o [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
