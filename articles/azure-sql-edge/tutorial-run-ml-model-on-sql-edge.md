---
title: Distribuire il modello di Machine Learning in SQL Edge di Azure usando ONNX
description: Nella terza delle tre parti di questa esercitazione di SQL Edge di Azure sulla stima delle impurità del minerale di ferro si eseguiranno i modelli di Machine Learning ONNX in SQL Edge.
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 64594267dc51fa42dabcc3083d18d631904a9cab
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593544"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Distribuire il modello di Machine Learning in SQL Edge di Azure usando ONNX 

Nella terza delle tre parti di questa esercitazione sulla stima delle impurità del minerale di ferro in SQL Edge di Azure si eseguiranno le operazioni seguenti:

1. Usare Azure Data Studio per connettersi al database SQL nell'istanza di SQL Edge di Azure.
2. Stimare le impurità del minerale di ferro con ONNX in SQL Edge di Azure.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Connettersi al database SQL nell'istanza di SQL Edge di Azure

1. Aprire Azure Data Studio.

2. Nella scheda di **benvenuto** avviare una nuova connessione con i dettagli seguenti:

   |_Campo_|_Valore_|
   |-------|-------|
   |Tipo di connessione| Microsoft SQL Server|
   |Server|Indirizzo IP pubblico indicato nella macchina virtuale creata per questa demo|
   |Username|sa|
   |Password|Password complessa usata durante la creazione dell'istanza di SQL Edge di Azure|
   |Database|Predefinito|
   |Gruppo di server|Predefinito|
   |Nome (facoltativo)|Specificare un nome facoltativo|

3. Fare clic su **Connetti**

4. Nella sezione **File** aprire un nuovo notebook o usare la scelta rapida da tastiera ALT+WINDOWS+N. 

5. Impostare il kernel su Python 3.

## <a name="predict-iron-ore-impurities-with-onnx"></a>Stimare le impurità del minerale di ferro con ONNX

Immettere il codice Python seguente nel notebook di Azure Data Studio ed eseguirlo.

1. Prima di tutto, installare e importare i pacchetti necessari.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. Definire l'area di lavoro di Azure AutoML e la configurazione dell'esperimento di AutoML per l'esperimento di regressione.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. Importare il set di dati in un frame di Panda. Ai fini del training del modello, usare il training set [Quality Prediction in a Mining Process](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) (Stima della qualità in un processo di estrazione) di Kaggle. Scaricare il file di dati e salvarlo in locale nel computer di sviluppo. Questi dati verranno usati per stimare il livello di impurità nel concentrato di minerale.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. Analizzare i dati per identificare eventuali indici di asimmetria. Durante questo processo, esaminare la distribuzione e le informazioni sull'asimmetria per ogni colonna nel frame di dati.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. Controllare e correggere il livello dell'indice di asimmetria nei dati.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. Controllare la correlazione delle altre funzionalità con la funzionalità di stima. Se la correlazione non è elevata, rimuovere tali funzionalità.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. Avviare l'esperimento di AzureML per trovare l'algoritmo migliore ed eseguirne il training. In questo caso si esegue il test con tutti gli algoritmi di regressione, con una metrica primaria della radice normalizzata dell'errore quadratico medio (NRMSE, Normalized Root Mean Squared Error). Per altre informazioni, vedere [Metrica primaria degli esperimenti di Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric). Il codice seguente avvierà un'esecuzione locale dell'esperimento di Machine Learning.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. Caricare il modello nel database di SQL Edge di Azure per il punteggio locale.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. Usare infine il modello di SQL Edge di Azure per eseguire stime con il modello sottoposto a training.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. Con Python, creare un grafico della percentuale stimata di silice rispetto alla quantità di ferro, al valore datetime e alla quantità di silice.

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di modelli ONNX in SQL Edge di Azure, vedere [Machine Learning e IA con ONNX in SQL Edge (anteprima)](onnx-overview.md).
