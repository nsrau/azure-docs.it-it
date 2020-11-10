---
title: 'Esercitazione: Eseguire esperimenti con ML automatizzato di Azure'
description: Esercitazione su come eseguire esperimenti di Machine Learning usando Apache Spark e ML automatizzato di Azure
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: 979e360bb920fc3b34a201b1287b50b141bffa9b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313626"
---
# <a name="tutorial-run-experiments-using-azure-automated-ml-and-apache-spark"></a>Esercitazione: Eseguire esperimenti con ML automatizzato di Azure e Apache Spark

Azure Machine Learning offre un ambiente basato sul cloud che consente di sottoporre a training, distribuire, automatizzare, gestire e monitorare i modelli di Machine Learning. 

In questa esercitazione si userà il servizio [Machine Learning automatizzato](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) di Azure Machine Learning per creare un modello di regressione e prevedere i prezzi delle corse dei taxi di New York. Questo processo accetta impostazioni di configurazione e dati di training e scorre automaticamente le combinazioni di diversi metodi di normalizzazione/standardizzazione delle caratteristiche, modelli e impostazioni di iperparametri per ottenere il modello ottimale.

In questa esercitazione si apprenderà come eseguire le attività seguenti:
- Scaricare i dati usando Apache Spark e i set di dati aperti di Azure
- Trasformare e pulire i dati usando i dataframe di Apache Spark
- Eseguire il training di un modello di regressione di Machine Learning automatizzato
- Calcolare l'accuratezza del modello

### <a name="before-you-begin"></a>Prima di iniziare

- Creare un pool di Apache Spark serverless seguendo questa [guida di avvio rapido](../quickstart-create-apache-spark-pool-studio.md).
- Completare l'esercitazione sulla [configurazione dell'area di lavoro di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup), se non è già disponibile un'area di lavoro di Azure Machine Learning. 

### <a name="understand-regression-models"></a>Informazioni sui modelli di regressione

I *modelli di regressione* prevedono valori di output numerici basati su predittori indipendenti. Nella regressione l'obiettivo è di stabilire la relazione tra le variabili indipendenti del predittore stimando il modo in cui una variabile influisce sulle altre.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Esempio di analisi di regressione sui dati dei taxi di New York

In questo esempio si userà Spark per eseguire un'analisi sui dati relativi alle mance offerte per le corse dei taxi di New York. I dati sono disponibili tramite [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo subset del set di dati contiene informazioni sulle corse in taxi, incluse informazioni su ogni corsa, l'ora di partenza e di arrivo, i percorsi, i costi e altri attributi interessanti.

> [!IMPORTANT]
> 
> Per il pull dei dati dalla posizione di archiviazione potrebbero essere previsti addebiti aggiuntivi. Nei passaggi successivi verrà sviluppato un modello per prevedere i prezzi delle corse dei taxi di New York. 
> 

##  <a name="download-and-prepare-the-data"></a>Scaricare e preparare i dati

1. Creare un notebook usando il kernel PySpark. Per le istruzioni, vedere [Creare un notebook](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creata automaticamente quando si esegue la prima cella di codice.
   >

2. Poiché i dati non elaborati sono in formato Parquet, è possibile usare il contesto di Spark per eseguire direttamente il pull del file in memoria come dataframe. Creare un dataframe di Spark recuperando i dati tramite l'API dei set di dati aperti. In questo caso, verranno usate le proprietà dello *schema in lettura* dei dataframe di Spark per dedurre i tipi di dati e lo schema. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. A seconda delle dimensioni del pool di Spark (anteprima), i dati non elaborati potrebbero essere troppo grandi o richiedere troppo tempo per poter essere usati. È possibile filtrare i dati fino a ottenere un valore inferiore usando i filtri ```start_date``` e ```end_date```. Il filtro viene applicato e restituisce un mese di dati. Una volta filtrato il dataframe, verrà anche eseguita la funzione ```describe()``` sul nuovo dataframe per visualizzare le statistiche riepilogative per ogni campo. 

   In base alle statistiche riepilogative, si possono notare alcune irregolarità e alcuni outlier nei dati. Le statistiche indicano, ad esempio, che la distanza minima delle corse è minore di 0. È necessario filtrare questi punti dati irregolari per rimuoverli.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. A questo punto, dal set di dati verrà selezionato un set di colonne e verranno create varie funzionalità basate sul tempo dal campo data/ora di inizio corsa. Verranno inoltre filtrati gli outlier identificati nel passaggio precedente e quindi verranno rimosse le ultime colonne non necessarie per il training.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
Come si può notare, in questo modo verrà creato un nuovo dataframe con colonne aggiuntive per il giorno del mese, l'ora di inizio corsa, il giorno della settimana e il tempo totale della corsa. 

![Immagine del dataframe dei taxi.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Generare i set di dati di test e di convalida

Una volta ottenuto il set di dati finale, è possibile dividere i dati in set di training e di test usando la funzione ```random_ split ``` di Spark. Usando i pesi forniti, questa funzione divide in modo casuale i dati in set di dati di training per il training del modello e in set di dati di convalida per i test.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Questo passaggio assicura che per testare il modello vengano usati punti dati non usati per eseguirne il training. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Connettersi a un'area di lavoro di Azure Machine Learning
In Azure Machine Learning **Workspace** è una classe che accetta le informazioni sulla sottoscrizione e sulle risorse di Azure. Crea inoltre una risorsa cloud per monitorare le esecuzioni del modello e tenerne traccia. In questo passaggio verrà creato un oggetto Workspace dall'area di lavoro di Azure Machine Learning esistente.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Convertire il dataframe in un set di dati di Azure Machine Learning
Per inviare un esperimento remoto, è necessario convertire il set di dati in un oggetto ```TabularDatset``` di Azure Machine Learning. Un oggetto [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) rappresenta i dati in formato tabulare analizzando i file forniti.

Il codice seguente ottiene l'area di lavoro esistente e l'archivio dati predefinito di Azure Machine Learning. Passa quindi l'archivio dati e i percorsi dei file al parametro path per creare un nuovo oggetto ```TabularDataset```. 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![Immagine del set di dati caricato.](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-automl-experiment"></a>Inviare un esperimento di ML automatizzato

#### <a name="define-training-settings"></a>Definire le impostazioni di training

1. Per inviare un esperimento, è necessario definire le relative impostazioni dei parametri e del modello per il training. L'elenco completo delle impostazioni è disponibile [qui](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

2. A questo punto, le impostazioni di training definite verranno passate come parametro \*\*kwargs a un oggetto AutoMLConfig. Poiché si sta eseguendo il training in Spark, sarà anche necessario passare il contesto di Spark, accessibile automaticamente tramite la variabile ```sc```. Inoltre, si specificheranno i dati di training e il tipo di modello, che in questo caso è regressione.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

#### <a name="train-the-automatic-regression-model"></a>Eseguire il training del modello di regressione automatica 
A questo punto verrà creato un oggetto Experiment nell'area di lavoro di Azure Machine Learning. Un esperimento funge da contenitore per le singole esecuzioni. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

Una volta completato l'esperimento, l'output restituirà i dettagli sulle iterazioni completate. Per ogni iterazione, vengono visualizzati il tipo di modello, la durata dell'esecuzione e l'accuratezza del training. Il campo BEST tiene traccia del miglior punteggio di training dell'esecuzione in base del tipo di metrica.

![Screenshot dell'output del modello.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> Una volta inviato, l'esperimento AutoML eseguirà varie iterazioni e tipi di modello. Questa esecuzione richiede in genere 1-1,5 ore. 

#### <a name="retrieve-the-best-model"></a>Recuperare il modello migliore
Per selezionare il modello migliore dalle iterazioni, si userà la funzione ```get_output``` per restituire l'esecuzione migliore e il modello adattato. Il codice seguente recupera l'esecuzione migliore e il modello adattato per qualsiasi metrica registrata o per un'iterazione specifica.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Testare l'accuratezza del modello

1. Per testare l'accuratezza del modello, si userà il modello migliore per eseguire previsioni relative alle tariffe dei taxi con il set di dati di test. La funzione```predict``` usa il modello migliore e prevede i valori di y, ovvero la tariffa, dal set di dati di convalida. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. La radice dell'errore quadratico medio viene usata spesso come misura delle differenze tra i valori campione previsti dal modello e i valori osservati. Verrà calcolata la radice dell'errore quadratico medio dei risultati confrontando il dataframe y_test con i valori previsti dal modello. 

   La funzione ```mean_squared_error``` calcola l'errore quadratico medio tra due matrici di valori. Viene quindi eseguita la radice quadrata del risultato. Questa metrica indica approssimativamente la differenza tra le previsioni e i valori effettivi delle tariffe dei taxi.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   La radice dell'errore quadratico medio è una misura valida del livello di accuratezza con cui il modello prevede la risposta. Nei risultati si nota che la previsione delle tariffe dei taxi eseguita dal modello in base alle caratteristiche del set di dati è piuttosto valida, in genere entro $ 2,00 in eccesso o in difetto

3. Eseguire il codice seguente per calcolare l'errore percentuale assoluto medio. Questa metrica esprime l'accuratezza come percentuale dell'errore. Calcola una differenza assoluta tra ogni valore previsto ed effettivo e quindi somma tutte le differenze. Alla fine esprime la somma come percentuale del totale dei valori effettivi.

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Dalle due metriche dell'accuratezza si nota che la previsione delle tariffe dei taxi eseguita del modello in base alle caratteristiche del set di dati è piuttosto valida. 

4. Dopo aver adattato un modello di regressione lineare, è ora necessario determinare quanto si adatta ai dati. A questo scopo, verranno tracciati i valori delle tariffe effettive rispetto all'output previsto. Verrà inoltre calcolata la misura del coefficiente di determinazione per capire quanto sono vicini i dati alla linea di regressione adattata.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![Screenshot del tracciato di regressione.](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Nei risultati è possibile osservare che la misura del coefficiente di determinazione rappresenta il 95% della varianza. Questo dato viene anche confermato dal tracciato dei valori effettivi rispetto a quelli osservati. Maggiore è la varianza rappresentata dal modello di regressione, più vicini saranno i punti dati alla linea di regressione adattata.  

## <a name="register-model-to-azure-machine-learning"></a>Registrare il modello in Azure Machine Learning
Una volta convalidato il modello migliore, è possibile registrarlo in Azure Machine Learning. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Visualizzare i risultati in Azure Machine Learning
Infine, è anche possibile accedere ai risultati delle iterazioni passando all'esperimento nell'area di lavoro di Azure Machine Learning. Qui sarà possibile approfondire i dettagli sullo stato dell'esecuzione, sui modelli provati e su altre metriche del modello. 

![Screenshot dell'area di lavoro di Azure Machine Learning.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Passaggi successivi
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Esercitazione su MLlib di Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
