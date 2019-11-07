---
title: Registrare gli esperimenti di ML & metrica
titleSuffix: Azure Machine Learning
description: Monitora gli esperimenti di Azure ML e monitora le metriche di esecuzione per migliorare il processo di creazione del modello. Aggiungere la registrazione allo script di training e visualizzare i risultati registrati di un'esecuzione.  Usare Run. log, Run. start_logging o ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: d8a2c456c725a3170bc940bf17dec6b0c4ad2c3e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584535"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Monitorare le esecuzioni e le metriche dell'esperimento di Azure ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Migliorare il processo di creazione del modello tenendo traccia degli esperimenti e monitorando le metriche di esecuzione. In questo articolo si apprenderà come aggiungere codice di registrazione allo script di training, inviare un'esecuzione dell'esperimento, monitorare l'esecuzione e controllare i risultati in Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning possono anche registrare le informazioni provenienti da altre origini durante il training, ad esempio le esecuzioni automatiche di Machine Learning o il contenitore Docker che esegue il processo di training. Questi log non sono documentati. Se si verificano problemi e si contatta il supporto tecnico Microsoft, potrebbero essere in grado di utilizzare questi log durante la risoluzione dei problemi.

> [!TIP]
> Le informazioni contenute in questo documento sono destinate principalmente a data scientist e sviluppatori che desiderano monitorare il processo di training del modello. Se si è un amministratore interessato al monitoraggio dell'utilizzo delle risorse e degli eventi di Azure Machine Learning, ad esempio le quote, le esecuzioni di training completate o le distribuzioni del modello completate, vedere [monitoraggio Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Metriche disponibili per il rilevamento

Le metriche seguenti possono essere aggiunte a un'esecuzione durante il training di un esperimento. Per visualizzare un elenco più dettagliato di cosa è possibile monitorare in un'esecuzione, vedere la [documentazione di riferimento della classe Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Tipo| Funzione Python | Note|
|----|:----|:----|
|Valori scalari |Funzione:<br>`run.log(name, value, description='')`<br><br>Esempio:<br>run.log("accuracy", 0.95) |Registrare un valore numerico o stringa per l'esecuzione con il nome specificato. La registrazione di una metrica per un'esecuzione fa sì che tale metrica venga archiviata nel record esecuzione nell'esperimento.  È possibile registrare la stessa metrica più volte all'interno di un'esecuzione. Il risultato verrà considerato un vettore di tale metrica.|
|Elenchi|Funzione:<br>`run.log_list(name, value, description='')`<br><br>Esempio:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Registrare un elenco di valori per l'esecuzione con il nome specificato.|
|Riga|Funzione:<br>`run.log_row(name, description=None, **kwargs)`<br>Esempio:<br>run.log_row("Y over X", x=1, y=0.4) | L'uso di *log_row* crea una metrica con più colonne come descritto in kwargs. Ogni parametro denominato genera una colonna con il valore specificato.  *log_row* può essere chiamato una volta per registrare una tupla arbitraria o più volte in un ciclo per generare una tabella completa.|
|Tabella|Funzione:<br>`run.log_table(name, value, description='')`<br><br>Esempio:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registrare un oggetto dizionario per l'esecuzione con il nome specificato. |
|Immagini|Funzione:<br>`run.log_image(name, path=None, plot=None)`<br><br>Esempio:<br>`run.log_image("ROC", plt)` | Registrare un'immagine per il record esecuzione. Usare log_image per registrare un file di immagine o un tracciato matplotlib per l'esecuzione.  Queste immagini saranno visibili e confrontabili nel record esecuzione.|
|Assegnare un tag a un'esecuzione|Funzione:<br>`run.tag(key, value=None)`<br><br>Esempio:<br>run.tag("selected", "yes") | Assegnare all'esecuzione una chiave stringa e un valore di stringa facoltativo.|
|Caricare un file o una directory|Funzione:<br>`run.upload_file(name, path_or_stream)`<br> <br> Esempio:<br>run.upload_file("best_model.pkl", "./model.pkl") | Caricare un file per il record esecuzione. Consente di eseguire automaticamente il file di acquisizione nella directory di output specificata, che per impostazione predefinita corrisponde a "./outputs" per la maggior parte dei tipi di esecuzione.  Usare upload_file solo quando è necessario caricare altri file o non è specificata una directory di output. È consigliabile aggiungere `outputs` al nome, in modo che venga caricato nella directory outputs. È possibile elencare tutti i file associati a questo record esecuzione chiamando `run.get_file_names()`|

> [!NOTE]
> Le metriche per valori scalari, elenchi, righe e tabelle possono essere di tipo float, integer o string.

## <a name="choose-a-logging-option"></a>Scegliere un'opzione di registrazione

Se si vuole tenere traccia dell'esperimento o monitorarlo, è necessario aggiungere codice per avviare la registrazione quando si invia l'esecuzione. Di seguito sono elencati diversi modi per attivare l'invio dell'esecuzione:
* __Run.start_logging__: aggiungere funzioni di registrazione allo script di training e avviare una sessione di registrazione interattiva nell'esperimento specificato. **start_logging** crea un'esecuzione interattiva per l'uso in scenari come i notebook. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento.
* __ScriptRunConfig__: aggiungere funzioni di registrazione allo script di training e caricare l'intera cartella dello script con l'esecuzione.  **ScriptRunConfig** è una classe per l'impostazione di configurazioni per le esecuzioni dello script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

## <a name="set-up-the-workspace"></a>Configurare l'area di lavoro
Prima di aggiungere la registrazione e inviare un esperimento, è necessario configurare l'area di lavoro.

1. Caricare l'area di lavoro. Per ulteriori informazioni sull'impostazione della configurazione dell'area di lavoro, vedere [file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Opzione 1: usare start_logging

**start_logging** crea un'esecuzione interattiva per l'uso in scenari come i notebook. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento.

Nell'esempio seguente viene eseguito il training di un semplice modello sklearn Ridge localmente in un'istanza di Jupyter Notebook locale. Per altre informazioni sull'invio di esperimenti a diversi ambienti, vedere [configurare le destinazioni di calcolo per il training del modello con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Creare uno script di training in un'istanza di Jupyter Notebook locale. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Aggiungere il rilevamento degli esperimenti usando il Azure Machine Learning SDK e caricare un modello permanente nel record di esecuzione dell'esperimento. Il codice seguente aggiunge tag e log e consente di caricare un file di modello nell'esecuzione dell'esperimento.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Lo script termina con ```run.complete()```, che contrassegna l'esecuzione come completata.  Questa funzione viene usata in genere in scenari di notebook interattivi.

## <a name="option-2-use-scriptrunconfig"></a>Opzione 2: usare ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) è una classe per la configurazione delle configurazioni per le esecuzioni di script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

Questo esempio si espande a partire dal modello sklearn Ridge di base dell'esempio precedente. Esegue un semplice sweep di parametri su valori alfa del modello per acquisire le metriche e i modelli sottoposti a training nelle esecuzioni nell'ambito dell'esperimento. L'esempio viene eseguito localmente in un ambiente gestito dall'utente. 

1. Creare uno script di training `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. Lo script `train.py` fa riferimento a `mylib.py` che consente di ottenere l'elenco dei valori alfa da usare nel modello ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configurare un ambiente locale gestito dall'utente.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Inviare lo script ```train.py``` da eseguire nell'ambiente gestito dall'utente. L'intera cartella dello script viene inviata per il training, incluso il file ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Gestire un'esecuzione

L'articolo [avviare, monitorare e annullare le esecuzioni di training](how-to-manage-runs.md) evidenzia i flussi di lavoro Azure Machine Learning specifici per la gestione degli esperimenti.

## <a name="view-run-details"></a>Visualizzare i dettagli dell'esecuzione

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitorare l'esecuzione con il widget Jupyter notebook
Quando si usa il metodo **ScriptRunConfig** per inviare le esecuzioni, è possibile controllare lo stato di avanzamento dell'esecuzione con un [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Come per l'invio dell'esecuzione, il widget è asincrono e fornisce aggiornamenti in tempo reale ogni 10-15 secondi finché non viene completato il processo.

1. Visualizzare il widget di Jupyter durante l'attesa del completamento dell'esecuzione.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Screenshot del widget di Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

È anche possibile ottenere un collegamento alla stessa visualizzazione nell'area di lavoro.

```python
print(run.get_portal_url())
```

2. **[Per le esecuzioni di Machine Learning automatizzato]** Per accedere ai grafici di un'esecuzione precedente, Sostituire `<<experiment_name>>` con il nome dell'esperimento appropriato:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget di notebook di Jupyter per Machine Learning automatizzato](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Per visualizzare ulteriori dettagli di una pipeline, fare clic sulla pipeline che si desidera esplorare nella tabella. verrà eseguito il rendering dei grafici in un popup da Azure Machine Learning Studio.

### <a name="get-log-results-upon-completion"></a>Risultati del recupero dei log dopo il completamento

Il training e il monitoraggio del modello si verificano in background, pertanto è possibile eseguire altre attività durante l'attesa. È anche possibile attendere il completamento del training del modello prima di eseguire altro codice. Quando si usa **ScriptRunConfig**, è possibile usare ```run.wait_for_completion(show_output = True)``` per visualizzare quando viene completato il training del modello. Il flag ```show_output``` fornisce output dettagliato. 


### <a name="query-run-metrics"></a>Eseguire query sulle metriche di esecuzione

È possibile visualizzare le metriche relative a un modello sottoposto a training usando ```run.get_metrics()```. È ora possibile ottenere tutte le metriche registrate nell'esempio precedente per determinare il modello migliore.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>Visualizzare l'esperimento nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com)

Al termine dell'esecuzione di un esperimento, è possibile passare al record esecuzione dell'esperimento registrato. È possibile accedere alla cronologia dal [Azure Machine Learning Studio](https://ml.azure.com).

Passare alla scheda esperimenti e selezionare l'esperimento. Viene visualizzato il dashboard di esecuzione dell'esperimento, in cui è possibile visualizzare le metriche registrate e i grafici che vengono registrati per ogni esecuzione. In questo caso sono stati registrati i valori alfa e MSE.

  ![Eseguire i dettagli in Azure Machine Learning Studio](./media/how-to-track-experiments/experiment-dashboard.png)

È possibile eseguire il drill-down fino a un'esecuzione specifica per visualizzare i relativi output o log oppure scaricare lo snapshot dell'esperimento inviato, in modo che sia possibile condividere la cartella dell'esperimento con altri utenti.

### <a name="viewing-charts-in-run-details"></a>Visualizzazione dei grafici nei dettagli dell'esecuzione

Esistono diversi modi per usare le API di registrazione per registrare diversi tipi di metriche durante un'esecuzione e visualizzarli come grafici in Azure Machine Learning Studio.

|Valore registrato|Codice di esempio| Visualizzazione nel portale|
|----|----|----|
|Registra una matrice di valori numerici| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Grafico a linee a singola variabile|
|Registra un valore numerico singolo con lo stesso nome di metrica usato più volte (come in un ciclo for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Grafico a linee a singola variabile|
|Registra più volte una riga con due colonne numeriche|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Grafico a linee a due variabili|
|Registra una tabella con due colonne numeriche|`run.log_table(name='Sine Wave', value=sines)`|Grafico a linee a due variabili|


## <a name="example-notebooks"></a>Notebook di esempio
I notebook seguenti illustrano i concetti descritti in questo articolo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Eseguire i passaggi seguenti per imparare a usare Azure Machine Learning SDK per Python:

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

* Altre informazioni su come [eseguire il training di modelli di PyTorch con Azure Machine Learning](how-to-train-pytorch.md).
