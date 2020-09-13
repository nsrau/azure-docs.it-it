---
title: Eseguire esperimenti usando Azure Automated ML
description: Eseguire esperimenti di Machine Learning usando Apache Spark e Azure Automated ML
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: d4df4ea96f8dafa2f0eb26e27fcc08ab4ec89003
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033575"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>Eseguire esperimenti usando Azure Automated ML e Apache Spark

Azure Machine Learning è un ambiente basato su cloud che consente di eseguire il training, distribuire, automatizzare, gestire e tenere traccia dei modelli di machine learning. 

In questa esercitazione viene usato [Machine Learning automatico](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) in Azure Machine Learning per creare un modello di regressione per stimare i prezzi delle tariffe di NYC taxi. Questo processo accetta i dati di training e le impostazioni di configurazione e scorre automaticamente le combinazioni dei diversi metodi di normalizzazione/standardizzazione delle funzionalità, i modelli e le impostazioni degli iperparametri per arrivare al modello migliore.

In questa esercitazione si apprenderà come eseguire le attività seguenti:
- Scaricare i dati usando Apache Spark e i set di dati aperti di Azure
- Trasformare e pulire i dati usando Apache Spark dataframes
- Eseguire il training di un modello di regressione di Machine Learning automatizzato
- Calcolare l'accuratezza del modello

### <a name="before-you-begin"></a>Prima di iniziare
- Per creare un pool di Apache Spark, seguire l' [esercitazione creare un pool di Apache Spark](../quickstart-create-apache-spark-pool-studio.md).
- Se non si dispone di un'area di lavoro Azure Machine Learning esistente, completare l' [esercitazione sull'impostazione dell'area di lavoro Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) . 

### <a name="understand-regression-models"></a>Informazioni sui modelli di regressione
I *modelli di regressione* stimano i valori di output numerici in base a predittori indipendenti. Nella regressione l'obiettivo è di stabilire la relazione tra le variabili indipendenti del predittore stimando il modo in cui una variabile influisce sulle altre.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Esempio di analisi di regressione sui dati dei taxi di NYC
In questo esempio, si userà Spark per eseguire un'analisi sui dati dei viaggi di taxi da New York. I dati sono disponibili tramite [set di dati aperti di Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Questo subset del set di dati contiene informazioni sulle corse in taxi, incluse informazioni su ogni corsa, l'ora di partenza e di arrivo, i percorsi, i costi e altri attributi interessanti.

> [!IMPORTANT]
> 
> Per il pull dei dati dalla posizione di archiviazione potrebbero essere previsti addebiti aggiuntivi. Nei passaggi seguenti si svilupperà un modello per stimare i prezzi delle tariffe di NYC taxi. 
> 

##  <a name="download-and-prepare-the-data"></a>Scaricare e preparare i dati

1. Creare un notebook usando il kernel PySpark. Per istruzioni, vedere [creare un notebook](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > Dato che è stato usato il kernel PySpark, non è necessario creare contesti in modo esplicito. Il contesto Spark viene creata automaticamente quando si esegue la prima cella di codice.
   >

2. Poiché i dati non elaborati sono in formato Parquet, è possibile usare il contesto di Spark per eseguire direttamente il pull del file in memoria come dataframe. Creare un frame di dati Spark recuperando i dati tramite l'API set di dati aperti. In questo caso verrà usato lo schema di dataframe di Spark sulle proprietà di *lettura* per dedurre i tipi di oggetto e lo schema. 
   
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

3. A seconda delle dimensioni del pool di Spark (anteprima), i dati non elaborati potrebbero essere troppo grandi o richiedere troppo tempo per poter essere usati. È possibile filtrare i dati in base a un valore più piccolo usando i ```start_date``` ```end_date``` filtri e. Viene applicato un filtro che restituisce un mese di dati. Una volta ottenuto il frame di dati filtrato, si eseguirà anche la ```describe()``` funzione sul nuovo dataframe per visualizzare le statistiche di riepilogo per ogni campo. 

   In base alle statistiche di riepilogo, è possibile osservare alcune irregolarità e outlier nei dati. Le statistiche, ad esempio, indicano che la distanza minima tra le corse è minore di 0. È necessario filtrare questi punti dati irregolari.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. A questo punto, verranno generate funzionalità dal set di dati selezionando un set di colonne e creando varie funzionalità basate sul tempo dal campo pickup DateTime. Verranno anche filtrati gli outlier identificati dal passaggio precedente e quindi verranno rimosse le ultime colonne che non sono necessarie per il training.
   
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
   
Come si può notare, verrà creato un nuovo dataframe con colonne aggiuntive per il giorno del mese, l'ora di prelievo, il giorno della settimana e il tempo totale di viaggio. 

![Immagine del dataframe di taxi.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Genera set di risultati di test e convalida

Una volta ottenuto il set di dati finale, è possibile suddividere i dati in set di training e di test usando la ```random_ split ``` funzione Spark. Utilizzando i pesi specificati, questa funzione suddivide in modo casuale i dati nel set di dati di training per il training del modello e il set di dati di convalida per il test.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Questo passaggio garantisce che i punti dati verifichino il modello finito che non è stato utilizzato per eseguire il training del modello. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Connettersi a un area di lavoro di Azure Machine Learning
In Azure Machine Learning, un'  **area di lavoro** è una classe che accetta la sottoscrizione di Azure e le informazioni sulle risorse. Crea inoltre una risorsa cloud per monitorare le esecuzioni del modello e tenerne traccia. In questo passaggio verrà creato un oggetto dell'area di lavoro dall'area di lavoro Azure Machine Learning esistente.
   
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

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Convertire un frame di dati in un set di dati Azure Machine Learning
Per inviare un esperimento remoto, è necessario convertire il set di dati in un Azure Machine Learning ```TabularDatset``` . Un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) rappresenta i dati in formato tabulare tramite l'analisi dei file forniti.

Il codice seguente ottiene l'area di lavoro esistente e l'oggetto predefinito Azure Machine Learning archivio dati predefinito. Passa quindi l'archivio dati e i percorsi dei file al parametro Path per crearne uno nuovo ```TabularDataset``` . 

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

## <a name="submit-an-auto-ml-experiment"></a>Invia un esperimento di Machine Learning automatico

#### <a name="define-training-settings"></a>Definire le impostazioni di training

1. Per inviare un esperimento, è necessario definire il parametro dell'esperimento e le impostazioni del modello per il training. [Qui](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)è possibile visualizzare l'elenco completo delle impostazioni.

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

2. A questo punto, le impostazioni di training definite verranno passate come \* \* parametro kwarg a un oggetto AutoMLConfig. Poiché si sta eseguendo il training in Spark, sarà anche necessario passare il contesto di Spark che è automaticamente accessibile dalla ```sc``` variabile. Inoltre, si specificheranno i dati di training e il tipo di modello, che in questo caso è la regressione.

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
A questo punto, verrà creato un oggetto esperimento nell'area di lavoro Azure Machine Learning. Un esperimento funge da contenitore per le singole esecuzioni. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

Una volta completato l'esperimento, l'output restituirà i dettagli sulle iterazioni completate. Per ogni iterazione, vengono visualizzati il tipo di modello, la durata dell'esecuzione e l'accuratezza del training. Il campo migliore tiene traccia del Punteggio di training migliore in base al tipo di metrica.

![Screenshot dell'output del modello.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> Una volta inviato, l'esperimento AutoML eseguirà varie iterazioni e tipi di modello. Questa esecuzione richiede in genere 1,5 ore. 

#### <a name="retrieve-the-best-model"></a>Recuperare il modello migliore
Per selezionare il modello migliore dalle iterazioni, si userà la ```get_output``` funzione per restituire il modello di esecuzione e di montaggio migliore. Il codice riportato di seguito recupererà il modello di esecuzione e adattato migliore per qualsiasi metrica registrata o un'iterazione particolare.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Testare l'accuratezza del modello

1. Per testare l'accuratezza del modello, si userà il modello migliore per eseguire stime relative ai taxi per il set di dati di test. La ```predict``` funzione usa il modello migliore e stima i valori di y (importo della tariffa) dal set di dati di convalida. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. L'errore Root-Mean-Square (valori RMSE) è una misura di uso frequente delle differenze tra i valori di esempio stimati da un modello e i valori osservati. Verrà calcolato il radice dell'errore quadratico medio dei risultati confrontando il y_test dataframe con i valori stimati dal modello. 

   La funzione ```mean_squared_error``` accetta due matrici e calcola l'errore quadratico medio tra di essi. Prendiamo quindi la radice quadrata del risultato. Questa metrica indica approssimativamente la distanza delle stime dei taxi dai valori effettivi delle tariffe.

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
   
   L'errore Root-Mean-Square è una misura corretta per la stima della risposta da parte del modello. Dai risultati si noterà che il modello è abbastanza valido per la stima delle tariffe taxi dalle funzionalità del set di dati, in genere entro +-$2,00

3. Eseguire il codice seguente per calcolare l'errore assoluto percentuale medio (MAPE). Questa metrica esprime l'accuratezza come percentuale dell'errore. Questa operazione viene eseguita calcolando una differenza assoluta tra ogni valore stimato ed effettivo e sommando tutte le differenze. Quindi esprime tale somma come percentuale del totale dei valori effettivi.

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
   Dalle due metriche di accuratezza delle stime, si noterà che il modello è abbastanza efficace per prevedere le tariffe dei taxi dalle funzionalità del set di dati. 

4. Dopo aver adattato un modello di regressione lineare, è ora necessario determinare il modo in cui il modello si adatta ai dati. A tale scopo, si tracciano i valori effettivi della tariffa rispetto all'output stimato. Verrà inoltre calcolata la misura R-Squared per comprendere il modo in cui i dati vengono chiusi nella linea di regressione adattata.

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

   Nei risultati è possibile osservare che la misura R-Squared rappresenta il 95% della varianza. Questa operazione viene convalidata anche dal tracciato effettivo dei versetti osservati. Maggiore è la varianza rappresentata dal modello di regressione, più vicino sarà il punto dati a una linea di regressione adattata.  

## <a name="register-model-to-azure-machine-learning"></a>Registrare il modello in Azure Machine Learning
Una volta convalidato il modello migliore, è possibile registrare il modello in Azure Machine Learning. Dopo aver registrato il modello, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Visualizza risultati in Azure Machine Learning
Infine, è anche possibile accedere ai risultati delle iterazioni passando all'esperimento nell'area di lavoro di Azure Machine Learning. Qui sarà possibile approfondire i dettagli sullo stato dell'esecuzione, dei modelli tentati e di altre metriche del modello. 

![Screenshot dell'area di lavoro AML.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Passaggi successivi
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Esercitazione Apache Spark MLlib](./apache-spark-machine-learning-mllib-notebook.md)
