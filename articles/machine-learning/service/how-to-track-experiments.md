---
title: Tenere traccia di esperimenti e metriche di training - Azure Machine Learning | Microsoft Docs
description: Con il servizio Azure Machine Learning, è possibile tenere traccia degli esperimenti e monitorare le metriche per migliorare il processo di creazione dei modelli. Informazioni su come aggiungere la registrazione allo script di training, inviare l'esperimento, controllare lo stato di avanzamento di un processo in esecuzione e visualizzare i risultati di un'esecuzione.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 9af7e57db0e465f59f43c93d0b5f6ec220836ff7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308189"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Tenere traccia di esperimenti e metriche di training in Azure Machine Learning

Nel servizio Azure Machine Learning è possibile tenere traccia degli esperimenti e monitorare le metriche per migliorare il processo di creazione dei modelli. Questo articolo illustrerà le diverse modalità disponibili per aggiungere la registrazione allo script di training, oltre a come inviare l'esperimento con **start_logging** e **ScriptRunConfig**, controllare lo stato di avanzamento di un processo in esecuzione e visualizzare i risultati di un'esecuzione. 

>[!NOTE]
> Il codice in questo articolo è stato testato con Azure Machine Learning SDK versione 0.1.74 

## <a name="list-of-training-metrics"></a>Elenco delle metriche di training 

Le metriche seguenti possono essere aggiunte a un'esecuzione durante il training di un esperimento. Per visualizzare un elenco più dettagliato di cosa è possibile monitorare in un'esecuzione, vedere la [documentazione di riferimento dell'SDK](https://aka.ms/aml-sdk).

|type| Funzione Python | Esempio | Note|
|----|:----|:----|:----|
|Valori scalari | `run.log(name, value, description='')`| `run.log("accuracy", 0.95) ` |Registrare un valore numerico o stringa per l'esecuzione con il nome specificato. La registrazione di una metrica per un'esecuzione fa sì che tale metrica venga archiviata nel record esecuzione nell'esperimento.  È possibile registrare la stessa metrica più volte all'interno di un'esecuzione. Il risultato verrà considerato un vettore di tale metrica.|
|Elenchi| `run.log_list(name, value, description='')`| `run.log_list("accuracies", [0.6, 0.7, 0.87])` | Registrare un elenco di valori per l'esecuzione con il nome specificato.|
|Riga| `run.log_row(name, description=None, **kwargs)`| `run.log_row("Y over X", x=1, y=0.4)` | L'uso di *log_row* crea una metrica con più colonne come descritto in kwargs. Ogni parametro denominato genera una colonna con il valore specificato.  *log_row* può essere chiamato una volta per registrare una tupla arbitraria o più volte in un ciclo per generare una tabella completa.|
|Tabella| `run.log_table(name, value, description='')`| `run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]})` | Registrare un oggetto dizionario per l'esecuzione con il nome specificato. |
|Immagini| `run.log_image(name, path=None, plot=None)`| `run.log_image("ROC", plt)` | Registrare un'immagine per il record esecuzione. Usare log_image per registrare un file di immagine o un tracciato matplotlib per l'esecuzione.  Queste immagini saranno visibili e confrontabili nel record esecuzione.|
|Assegnare un tag a un'esecuzione| `run.tag(key, value=None)`| `run.tag("selected", "yes")` | Assegnare all'esecuzione una chiave stringa e un valore di stringa facoltativo.|
|Caricare un file o una directory|`run.upload_file(name, path_or_stream)`| run.upload_file("best_model.pkl", "./model.pkl") | Caricare un file per il record esecuzione. Consente di eseguire automaticamente il file di acquisizione nella directory di output specificata, che per impostazione predefinita corrisponde a "./outputs" per la maggior parte dei tipi di esecuzione.  Usare upload_file solo quando è necessario caricare altri file o non è specificata una directory di output. È consigliabile aggiungere `outputs` al nome, in modo che venga caricato nella directory outputs. È possibile elencare tutti i file associati a questo record esecuzione chiamando `run.get_file_names()`|

> [!NOTE]
> Le metriche per valori scalari, elenchi, righe e tabelle possono essere di tipo float, integer o string.

## <a name="log-metrics-for-experiments"></a>Registrare le metriche per gli esperimenti

Se si vuole tenere traccia dell'esperimento o monitorarlo, è necessario aggiungere codice per avviare la registrazione quando si invia l'esecuzione. Di seguito sono elencati diversi modi per attivare l'invio dell'esecuzione:
* __Run.start_logging__: aggiungere funzioni di registrazione allo script di training e avviare una sessione di registrazione interattiva nell'esperimento specificato. **start_logging** crea un'esecuzione interattiva per l'uso in scenari come i notebook. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento.
* __ScriptRunConfig__: aggiungere funzioni di registrazione allo script di training e caricare l'intera cartella dello script con l'esecuzione.  **ScriptRunConfig** è una classe per l'impostazione di configurazioni per le esecuzioni dello script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

## <a name="set-up-the-workspace-and-experiment"></a>Configurare l'area di lavoro e l'esperimento
Prima di aggiungere la registrazione e di inviare un esperimento, è necessario configurare l'area di lavoro e l'esperimento.

1. Caricare l'area di lavoro. Per altre informazioni sull'impostazione della configurazione dell'area di lavoro, seguire la [guida introduttiva](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Workspace, Run
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```

2. Creare l'esperimento.

  ```python
  from azureml.core import Experiment

  # make up an arbitrary name
  experiment_name = 'train-in-notebook'
  ```
  
## <a name="option-1-use-startlogging"></a>Opzione 1: usare start_logging

**start_logging** crea un'esecuzione interattiva per l'uso in scenari come i notebook. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento.

Nell'esempio seguente viene eseguito il training di un semplice modello sklearn Ridge localmente in un'istanza di Jupyter Notebook locale. Per altre informazioni sull'invio di esperimenti in ambienti diversi, vedere [Configurare le destinazioni di calcolo per il training del modello con il servizio di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Creare uno script di training in un'istanza di Jupyter Notebook locale. 

  ``` python
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

2. Aggiungere il rilevamento dell'esperimento usando Azure Machine Learning SDK e caricare un modello persistente nel record esecuzione dell'esperimento. Il codice seguente aggiunge tag e log e consente di caricare un file di modello nell'esecuzione dell'esperimento.

  ```python
  experiment = Experiment(workspace = ws, name = experiment_name)
  run = experiment.start_logging()
  run.tag("Description","My first run!")
  run.log('alpha', 0.03)
  reg = Ridge(alpha = 0.03)
  reg.fit(data['train']['X'], data['train']['y'])
  preds = reg.predict(data['test']['X'])
  run.log('mse', mean_squared_error(preds, data['test']['y']))
  joblib.dump(value = reg, filename = 'model.pkl')
  # Upload file directly to the outputs folder
  run.upload_file(name = 'outputs/model.pkl', path_or_stream = './model.pkl')

  run.complete()
  ```

Lo script termina con ```run.complete()```, che contrassegna l'esecuzione come completata.  Viene usato in genere in scenari di notebook interattivi.

## <a name="option-2-use-scriptrunconfig"></a>Opzione 2: usare ScriptRunConfig

**ScriptRunConfig** è una classe per l'impostazione di configurazioni per le esecuzioni dello script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

Questo esempio si espande a partire dal modello sklearn Ridge di base dell'esempio precedente. Esegue un semplice sweep di parametri su valori alfa del modello per acquisire le metriche e i modelli sottoposti a training nelle esecuzioni nell'ambito dell'esperimento. L'esempio viene eseguito localmente in un ambiente gestito dall'utente. 

1. Creare uno script di training. Viene usato ```%%writefile%%``` per scrivere il codice di training nella cartella dello script come ```train.py```.

  ```python
  %%writefile $project_folder/train.py

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

2. Lo script ```train.py``` fa riferimento a ```mylib.py```. Questo file consente di ottenere l'elenco dei valori alfa da usare nel modello ridge.

  ```python
  %%writefile $script_folder/mylib.py
  import numpy as np

  def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
  ```

3. Configurare un ambiente locale gestito dall'utente.

  ```python
  from azureml.core.runconfig import RunConfiguration

  # Editing a run configuration property on-fly.
  run_config_user_managed = RunConfiguration()

  run_config_user_managed.environment.python.user_managed_dependencies = True

  # You can choose a specific Python environment by pointing to a Python path 
  #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
  ```

4. Inviare lo script ```train.py``` da eseguire nell'ambiente gestito dall'utente. L'intera cartella dello script viene inviata per il training, incluso il file ```mylib.py```.

  ```python
  from azureml.core import ScriptRunConfig

  src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
  run = experiment.submit(src)
  ```
  
## <a name="view-run-details"></a>Visualizzare i dettagli dell'esecuzione

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Monitorare l'esecuzione con i widget di Jupyter Notebook
Quando si usa il metodo **ScriptRunConfig** per inviare le esecuzioni, è possibile controllare lo stato di avanzamento dell'esecuzione con un widget di Jupyter Notebook. Come per l'invio dell'esecuzione, il widget è asincrono e fornisce aggiornamenti in tempo reale ogni 10-15 secondi finché non viene completato il processo.

1. Visualizzare il widget di Jupyter durante l'attesa del completamento dell'esecuzione.

  ```python
  from azureml.train.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Screenshot del widget di Jupyter Notebook](./media/how-to-track-experiments/widgets.PNG)

### <a name="get-log-results-upon-completion"></a>Ottenere i risultati del log dopo il completamento

Il training e il monitoraggio del modello si verificano in background, pertanto è possibile eseguire altre attività durante l'attesa. È anche possibile attendere il completamento del training del modello prima di eseguire altro codice. Quando si usa **ScriptRunConfig**, è possibile usare ```run.wait_for_completion(show_output = True)``` per visualizzare quando viene completato il training del modello. Il flag ```show_output``` fornisce output dettagliato. 
  
### <a name="query-run-metrics"></a>Eseguire query sulle metriche di esecuzione

È possibile visualizzare le metriche relative a un modello sottoposto a training usando ```run.get_metrics()```. È ora possibile ottenere tutte le metriche registrate nell'esempio precedente per determinare il modello migliore.

<a name='view-the-experiment-in-the-web-portal'/>
## <a name="view-the-experiment-in-the-azure-portal"></a>Visualizzare l'esperimento nel portale di Azure

Al termine dell'esecuzione di un esperimento, è possibile passare al record esecuzione dell'esperimento registrato. Questa operazione può essere eseguita in due modi:

* Ottenere l'URL dell'esecuzione direttamente ```print(run.get_portal_url())```
* Visualizzare i dettagli dell'esecuzione inviando il nome dell'esecuzione (in questo caso, ```run```). In questo modo vengono visualizzati il nome, l'ID, il tipo, lo stato e una pagina di dettagli dell'esperimento, oltre a un collegamento al portale di Azure e a un collegamento alla documentazione.

Il collegamento per l'esecuzione consente di accedere direttamente alla pagina di dettagli dell'esecuzione nel portale di Azure, dove è possibile visualizzare eventuali proprietà, metriche rilevate, immagini e grafici che sono registrati nell'esperimento. In questo caso sono stati registrati i valori alfa e MSE.

  ![Schermata dei dettagli dell'esecuzione nel portale di Azure](./media/how-to-track-experiments/run-details-page-web.PNG)

È anche possibile visualizzare gli output o i log per l'esecuzione oppure scaricare lo snapshot dell'esperimento inviato in modo da condividere la cartella dell'esperimento con altri utenti.
### <a name="viewing-charts-in-run-details"></a>Visualizzazione dei grafici nei dettagli dell'esecuzione

Sono disponibili vari modi per usare le API di registrazione per registrare diversi tipi di metriche durante un'esecuzione e visualizzarli come grafici nel portale di Azure. 

|Valore registrato|Codice di esempio| Visualizzazione nel portale|
|----|----|----|
|Registra una matrice di valori numerici| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Grafico a linee a singola variabile|
|Registra un valore numerico singolo con lo stesso nome di metrica usato più volte (come in un ciclo for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Grafico a linee a singola variabile|
|Registra più volte una riga con due colonne numeriche|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Grafico a linee a due variabili|
|Registra una tabella con due colonne numeriche|`run.log_table(name='Sine Wave', value=sines)`|Grafico a linee a due variabili|

## <a name="example-notebooks"></a>Notebook di esempio
I notebook seguenti illustrano i concetti descritti in questo articolo:
* [01.getting-started/01.train-within-notebook/01.train-within-notebook.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/01.train-within-notebook)
* [01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.getting-started/06.logging-api/06.logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/06.logging-api/06.logging-api.ipynb)

Ottenere questi notebook:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Eseguire i passaggi seguenti per imparare a usare Azure Machine Learning SDK per Python:

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

* Altre informazioni su come [eseguire il training di modelli di PyTorch con Azure Machine Learning](how-to-train-pytorch.md).
