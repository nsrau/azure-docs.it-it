---
title: Tenere traccia degli esperimenti e delle metriche di training
titleSuffix: Azure Machine Learning service
description: Con il servizio Azure Machine Learning, è possibile tenere traccia degli esperimenti e monitorare le metriche per migliorare il processo di creazione dei modelli. Informazioni su come aggiungere la registrazione allo script di training, inviare l'esperimento, controllare lo stato di avanzamento di un processo in esecuzione e visualizzare i risultati di un'esecuzione.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 07b29b05bc15f57d6fd3ec64ceaee812b912b0f6
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977899"
---
# <a name="track-experiments-and-training-metrics-in-azure-machine-learning"></a>Tenere traccia di esperimenti e metriche di training in Azure Machine Learning

Nel servizio Azure Machine Learning è possibile tenere traccia degli esperimenti e monitorare le metriche per migliorare il processo di creazione dei modelli. Questo articolo illustrerà le diverse modalità disponibili per aggiungere la registrazione allo script di training, oltre a come inviare l'esperimento con **start_logging** e **ScriptRunConfig**, controllare lo stato di avanzamento di un processo in esecuzione e visualizzare i risultati di un'esecuzione. 


## <a name="list-of-training-metrics"></a>Elenco delle metriche di training 

Le metriche seguenti possono essere aggiunte a un'esecuzione durante il training di un esperimento. Per visualizzare un elenco più dettagliato di cosa è possibile monitorare in un'esecuzione, vedere la [documentazione di riferimento della classe Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Funzione Python | Note|
|----|:----|:----|
|Valori scalari |Funzione:<br>`run.log(name, value, description='')`<br><br>Esempio:<br>run.log("accuracy", 0.95) |Registrare un valore numerico o stringa per l'esecuzione con il nome specificato. La registrazione di una metrica per un'esecuzione fa sì che tale metrica venga archiviata nel record esecuzione nell'esperimento.  È possibile registrare la stessa metrica più volte all'interno di un'esecuzione. Il risultato verrà considerato un vettore di tale metrica.|
|Elenchi|Funzione:<br>`run.log_list(name, value, description='')`<br><br>Esempio:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Registrare un elenco di valori per l'esecuzione con il nome specificato.|
|Riga|Funzione:<br>`run.log_row(name, description=None, **kwargs)<br>Esempio:<br>run.log_row("Y over X", x=1, y=0.4) | L'uso di *log_row* crea una metrica con più colonne come descritto in kwargs. Ogni parametro denominato genera una colonna con il valore specificato.  *log_row* può essere chiamato una volta per registrare una tupla arbitraria o più volte in un ciclo per generare una tabella completa.|
|Tabella|Funzione:<br>`run.log_table(name, value, description='')`<br><br>Esempio:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Registrare un oggetto dizionario per l'esecuzione con il nome specificato. |
|Immagini|Funzione:<br>`run.log_image(name, path=None, plot=None)`<br><br>Esempio:<br>run.log_image("ROC", plt) | Registrare un'immagine per il record esecuzione. Usare log_image per registrare un file di immagine o un tracciato matplotlib per l'esecuzione.  Queste immagini saranno visibili e confrontabili nel record esecuzione.|
|Assegnare un tag a un'esecuzione|Funzione:<br>`run.tag(key, value=None)`<br><br>Esempio:<br>run.tag("selected", "yes") | Assegnare all'esecuzione una chiave stringa e un valore di stringa facoltativo.|
|Caricare un file o una directory|Funzione:<br>`run.upload_file(name, path_or_stream)`<br> <br> Esempio:<br>run.upload_file("best_model.pkl", "./model.pkl") | Caricare un file per il record esecuzione. Consente di eseguire automaticamente il file di acquisizione nella directory di output specificata, che per impostazione predefinita corrisponde a "./outputs" per la maggior parte dei tipi di esecuzione.  Usare upload_file solo quando è necessario caricare altri file o non è specificata una directory di output. È consigliabile aggiungere `outputs` al nome, in modo che venga caricato nella directory outputs. È possibile elencare tutti i file associati a questo record esecuzione chiamando `run.get_file_names()`|

> [!NOTE]
> Le metriche per valori scalari, elenchi, righe e tabelle possono essere di tipo float, integer o string.

## <a name="start-logging-metrics"></a>Avviare la registrazione delle metriche

Se si vuole tenere traccia dell'esperimento o monitorarlo, è necessario aggiungere codice per avviare la registrazione quando si invia l'esecuzione. Di seguito sono elencati diversi modi per attivare l'invio dell'esecuzione:
* __Run.start_logging__: aggiungere funzioni di registrazione allo script di training e avviare una sessione di registrazione interattiva nell'esperimento specificato. **start_logging** crea un'esecuzione interattiva per l'uso in scenari come i notebook. Le metriche registrate durante la sessione vengono aggiunte al record esecuzione nell'esperimento.
* __ScriptRunConfig__: aggiungere funzioni di registrazione allo script di training e caricare l'intera cartella dello script con l'esecuzione.  **ScriptRunConfig** è una classe per l'impostazione di configurazioni per le esecuzioni dello script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

## <a name="set-up-the-workspace"></a>Configurare l'area di lavoro
Prima di aggiungere la registrazione e inviare un esperimento, è necessario configurare l'area di lavoro.

1. Caricare l'area di lavoro. Per altre informazioni sull'impostazione della configurazione dell'area di lavoro, seguire la [guida introduttiva](https://docs.microsoft.com/azure/machine-learning/service/quickstart-get-started).

  ```python
  from azureml.core import Experiment, Run, Workspace
  import azureml.core
  
  ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Opzione 1: Usare start_logging

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
  # Get an experiment object from Azure Machine Learning
  experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
  # Create a run object in the experiment
  run = experiment.start_logging()# Log the algorithm parameter alpha to the run
  run.log('alpha', 0.03)

  # Create, fit, and test the scikit-learn Ridge regression model
  regression_model = Ridge(alpha=0.03)
  regression_model.fit(data['train']['X'], data['train']['y'])
  preds = regression_model.predict(data['test']['X'])

  # Output the Mean Squared Error to the notebook and to the run
  print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
  run.log('mse', mean_squared_error(data['test']['y'], preds))

  # Save the model to the outputs directory for capture
  joblib.dump(value=regression_model, filename='outputs/model.pkl')

  # Take a snapshot of the directory containing this notebook
  run.take_snapshot('./')

  # Complete the run
  run.complete()
  
  ```

Lo script termina con ```run.complete()```, che contrassegna l'esecuzione come completata.  Questa funzione viene usata in genere in scenari di notebook interattivi.

## <a name="option-2-use-scriptrunconfig"></a>Opzione 2: Usare ScriptRunConfig

**ScriptRunConfig** è una classe per l'impostazione di configurazioni per le esecuzioni dello script. Con questa opzione, è possibile aggiungere codice per il monitoraggio in modo da ricevere una notifica del completamento o per ottenere un widget visivo da monitorare.

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
  
  experiment = Experiment(workspace=ws, name="train-on-local")
  src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
  run = experiment.submit(src)
  ```

## <a name="cancel-a-run"></a>Annullare un'esecuzione
Dopo che viene inviata un'esecuzione, è possibile annullarla anche se è stato perso il riferimento all'oggetto, purché si conosca il nome dell'esperimento e l'ID esecuzione. 

```python
from azureml.core import Experiment
exp = Experiment(ws, "my-experiment-name")

# if you don't know the run id, you can list all runs under an experiment
for r in exp.get_runs():  
    print(r.id, r.get_status())

# if you know the run id, you can "rehydrate" the run
from azureml.core import get_run
r = get_run(experiment=exp, run_id="my_run_id", rehydrate=True)
  
# check the returned run type and status
print(type(r), r.get_status())

# you can cancel a run if it hasn't completed or failed
if r.get_status() not in ['Complete', 'Failed']:
    r.cancel()
```
Si noti che attualmente solo i tipi ScriptRun e PipelineRun supportano l'operazione di annullamento.

È anche possibile annullare un'esecuzione tramite l'interfaccia della riga di comando usando il comando seguente:
```shell
az ml run cancel -r <run_id> -p <project_path>
```


## <a name="view-run-details"></a>Visualizzare i dettagli dell'esecuzione

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Monitorare l'esecuzione con i widget di Jupyter Notebook
Quando si usa il metodo **ScriptRunConfig** per inviare le esecuzioni, è possibile controllare lo stato di avanzamento dell'esecuzione con un widget di Jupyter Notebook. Come per l'invio dell'esecuzione, il widget è asincrono e fornisce aggiornamenti in tempo reale ogni 10-15 secondi finché non viene completato il processo.

1. Visualizzare il widget di Jupyter durante l'attesa del completamento dell'esecuzione.

  ```python
  from azureml.widgets import RunDetails
  RunDetails(run).show()
  ```

  ![Screenshot del widget di Jupyter Notebook](./media/how-to-track-experiments/widgets.PNG)

2. **[Per le esecuzioni di Machine Learning automatizzato]** Per accedere ai grafici di un'esecuzione precedente, sostituire `<<experiment_name>>` con il nome dell'esperimento appropriato:

   ``` 
   from azureml.train.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

  ![Widget di notebook di Jupyter per Machine Learning automatizzato](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Per visualizzare altri dettagli di un pipeline, fare clic sulla pipeline che si vuole esplorare nella tabella. I grafici verranno visualizzati in una finestra popup dal portale di Azure.

### <a name="get-log-results-upon-completion"></a>Ottenere i risultati del log dopo il completamento

Il training e il monitoraggio del modello si verificano in background, pertanto è possibile eseguire altre attività durante l'attesa. È anche possibile attendere il completamento del training del modello prima di eseguire altro codice. Quando si usa **ScriptRunConfig**, è possibile usare ```run.wait_for_completion(show_output = True)``` per visualizzare quando viene completato il training del modello. Il flag ```show_output``` fornisce output dettagliato. 
  
### <a name="query-run-metrics"></a>Eseguire query sulle metriche di esecuzione

È possibile visualizzare le metriche relative a un modello sottoposto a training usando ```run.get_metrics()```. È ora possibile ottenere tutte le metriche registrate nell'esempio precedente per determinare il modello migliore.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Visualizzare l'esperimento nel portale di Azure

Al termine dell'esecuzione di un esperimento, è possibile passare al record esecuzione dell'esperimento registrato. È possibile accedere alla cronologia in due modi:

* Ottenere l'URL dell'esecuzione direttamente ```print(run.get_portal_url())```
* Visualizzare i dettagli dell'esecuzione inviando il nome dell'esecuzione (in questo caso, ```run```). In questo modo vengono visualizzati il nome, l'ID, il tipo, lo stato, una pagina di dettagli dell'esperimento, un collegamento al portale di Azure e un altro collegamento alla documentazione.

Il collegamento per l'esecuzione consente di accedere direttamente alla pagina di dettagli dell'esecuzione nel portale di Azure, dove è possibile visualizzare eventuali proprietà, metriche rilevate, immagini e grafici che sono registrati nell'esperimento. In questo caso sono stati registrati i valori alfa e MSE.

  ![Dettagli dell'esecuzione nel portale di Azure](./media/how-to-track-experiments/run-details-page-web.PNG)

È anche possibile visualizzare gli output o i log per l'esecuzione oppure scaricare lo snapshot dell'esperimento inviato in modo da condividere la cartella dell'esperimento con altri utenti.

### <a name="viewing-charts-in-run-details"></a>Visualizzazione dei grafici nei dettagli dell'esecuzione

Sono disponibili vari modi per usare le API di registrazione per registrare diversi tipi di metriche durante un'esecuzione e visualizzarli come grafici nel portale di Azure. 

|Valore registrato|Codice di esempio| Visualizzazione nel portale|
|----|----|----|
|Registra una matrice di valori numerici| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Grafico a linee a singola variabile|
|Registra un valore numerico singolo con lo stesso nome di metrica usato più volte (come in un ciclo for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Grafico a linee a singola variabile|
|Registra più volte una riga con due colonne numeriche|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Grafico a linee a due variabili|
|Registra una tabella con due colonne numeriche|`run.log_table(name='Sine Wave', value=sines)`|Grafico a linee a due variabili|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Informazioni sui grafici di Machine Learning automatizzato

Dopo avere inviato un processo di Machine Learning automatizzato in un notebook, è possibile trovare una cronologia di queste esecuzioni nell'area di lavoro del servizio Machine Learning. 

Altre informazioni su:
+ [Grafici e curve per i modelli di classificazione](#classification)
+ [Grafici per i modelli di regressione](#regression)
+ [Spiegabilità del modello](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Visualizzare i grafici delle esecuzioni

1. Passa all'area di lavoro. 

1. Selezionare **Esperimenti** nel pannello a sinistra dell'area di lavoro.

  ![Screenshot del menu Esperimenti](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Selezionare l'esperimento a cui si è interessati.

  ![Elenco degli esperimenti](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. Nella tabella selezionare il numero dell'esecuzione.

   ![Esecuzione dell'esperimento](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1.  Nella tabella selezionare il numero dell'iterazione per il modello che si vuole esplorare più in dettaglio.

   ![Modello dell'esperimento](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>classificazione

Per ogni modello di classificazione creato tramite le funzionalità di Machine Learning automatizzato in Azure Machine Learning, è possibile visualizzare i grafici seguenti: 
+ [Matrice di confusione](#confusion-matrix)
+ [Grafico di precisione-recupero](#precision-recall-chart)
+ [ROC (Receiver Operating Characteristics)](#ROC)
+ [Curva di accuratezza](#lift-curve)
+ [Curva del guadagno](#gains-curve)
+ [Tracciato di calibrazione](#calibration-plot)

#### <a name="confusion-matrix"></a>Matrice di confusione

Una matrice di confusione viene usata per descrivere le prestazioni di un modello di classificazione. Ogni riga visualizza le istanze della classe vera e ogni colonna rappresenta le istanze della classe stimata. La matrice di confusione mostra le etichette classificate correttamente e quelle classificate in modo errato per un determinato modello.

Per i problemi di classificazione, Azure Machine Learning offre automaticamente una matrice di confusione per ogni modello creato. Per ogni matrice di confusione, il processo di Machine Learning automatizzato visualizza in verde le etichette classificate correttamente e in rosso quelle classificate in modo errato. La dimensione del cerchio rappresenta il numero di esempi nello specifico contenitore. Nei grafici a barre adiacenti è inoltre indicato il valore di frequenza di ogni etichetta stimata e ogni etichetta vera. 

Esempio 1: modello di classificazione con scarsa accuratezza ![modello di classificazione con scarsa accuratezza](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

Esempio 2 modello di classificazione con elevata accuratezza (ideale) ![modello di classificazione con elevata accuratezza](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Grafico di precisione-recupero

Con questo grafico è possibile confrontare le curve di precisione-recupero per ogni modello al fine di determinare quale modello ha una relazione accettabile tra precisione e recupero per uno specifico problema aziendale. Questo grafico mostra la media macro di precisione-recupero, la media micro di precisione-recupero e il valore di precisione-recupero associato a tutte le classi per un modello.

Il termine "precisione" indica la capacità di un algoritmo di classificazione di etichettare correttamente tutte le istanze. Il termine "recupero" indica la capacità di un algoritmo di classificazione di trovare tutte le istanze di una determinata etichetta. La curva di precisione-recupero mostra la relazione tra questi due concetti. In una situazione ideale, il modello ha 100% di precisione e 100% di accuratezza.

Esempio 1: modello di classificazione con valori ridotti di precisione e recupero ![modello di classificazione con valori ridotti di precisione e recupero](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

Esempio 2 modello di classificazione con ~100% di precisione e ~100% di recupero (ideale) ![modello di classificazione con valori elevati di precisione e recupero](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

ROC (Receiver Operating Characteristics) è un tracciato che mette a confronto le etichette classificate correttamente con quelle classificate in modo errato per un determinato modello. La curva ROC può essere meno informativa quando si esegue il training di modelli su set di dati con elevata distorsione perché non visualizza le etichette false positive.

Esempio 1: modello di classificazione con un numero ridotto di etichette vere e un numero elevato di etichette false ![modello di classificazione con un numero ridotto di etichette vere e un numero elevato di etichette false](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

Esempio 2 modello di classificazione con un numero elevato di etichette vere e un numero ridotto di etichette false ![modello di classificazione con un numero elevato di etichette vere e un numero ridotto di etichette false](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Curva di accuratezza

È possibile confrontare l'accuratezza del modello creato automaticamente con Azure Machine Learning rispetto al modello di base per visualizzare il guadagno di valore di tale modello.

I grafici di accuratezza vengono usati per valutare le prestazioni di un modello di classificazione. Mostrano il miglioramento che ci si può aspettare di ottenere usando un modello rispetto all'apprendimento senza modello. 

Esempio 1: risultati del modello peggiori rispetto un modello di selezione casuale ![risultati del modello di classificazione peggiori rispetto un modello di selezione casuale](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

Esempio 2 risultati del modello migliori rispetto un modello di selezione casuale ![risultati del modello di classificazione migliori](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Curva del guadagno

Un grafico del guadagno valuta le prestazioni di un modello di classificazione in base alle singole porzioni dei dati. Per ogni percentile del set di dati, mostra il miglioramento che ci si può aspettare di ottenere rispetto a un modello di selezione casuale.

L'uso del grafico del guadagno cumulativo consente di scegliere il limite di classificazione usando un valore percentuale corrispondente al guadagno che si vuole ottenere dal modello. Queste informazioni offrono un modo alternativo di esaminare i risultati nel grafico di accuratezza associato.

Esempio 1: modello di classificazione con guadagno minimo ![modello di classificazione con guadagno minimo](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

Esempio 2 modello di classificazione con guadagno significativo ![modello di classificazione con guadagno significativo](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>Tracciato di calibrazione

Per tutti i problemi di classificazione, è possibile esaminare la linea di calibrazione per media micro, media macro e ogni classe in un determinato modello predittivo. 

Un tracciato di calibrazione viene usato per visualizzare il grado di fiducia di un modello predittivo. A tale scopo, il tracciato mostra la relazione tra probabilità stimata e probabilità effettiva, dove per "probabilità" si intende la possibilità che una particolare istanza appartenga a una determinata etichetta. Un modello ben calibrato è allineato alla linea y=x, che rappresenta una ragionevole fiducia nelle stime. Un modello con eccessiva fiducia è allineato alla linea y=0, dove la probabilità stimata è presente ma non esiste alcuna probabilità effettiva.

Esempio 1: modello ben calibrato ![ modello ben calibrato](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

Esempio 2 modello con eccessiva fiducia ![modello con eccessiva fiducia](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>Regressione
Per ogni modello di regressione creato tramite le funzionalità di Machine Learning automatizzato in Azure Machine Learning, è possibile visualizzare i grafici seguenti: 
+ [Valori stimato rispetto a valore vero](#pvt)
+ [Istogramma dei valori residui](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Valore stimato rispetto a valore True 

Questo grafico mostra la relazione tra un valore stimato e il valore vero correlato per un problema di regressione. Può essere usato per misurare le prestazioni di un modello poiché la vicinanza dei valori stimati alla linea y=x è direttamente proporzionale all'accuratezza di un modello predittivo.

Dopo ogni esecuzione, è possibile visualizzare un grafico del valore stimato rispetto al valore vero per ogni modello di regressione. Per proteggere la privacy dei dati, i valori sono raggruppati in contenitori e la dimensione di ogni contenitore viene visualizzata in un grafico a barre nella parte inferiore dell'area del grafico. È possibile confrontare il modello predittivo, con l'area più chiara indicante i margini di errore, rispetto al valore ideale che dovrebbe raggiungere il modello.

Esempio 1: modello di regressione con accuratezza delle stime ridotta ![modello di regressione con accuratezza delle stime ridotta](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

Esempio 2 modello di regressione con accuratezza delle stime elevata ![modello di regressione con accuratezza delle stime elevata](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Istogramma dei valori residui

Un valore residuo rappresenta la differenza tra un valore y osservato e il valore y stimato. Per mostrare un margine di errore con distorsione ridotta, l'istogramma dei valori residui deve avere la forma di una curva a campana, con il centro vicino allo 0. 

Esempio 1: modello di regressione con distorsione negli errori ![modello di regressione con distorsione negli errori](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

Esempio 2 modello di regressione con una distribuzione degli errori più uniforme![modello di regressione con una distribuzione degli errori più uniforme](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>Spiegabilità del modello e importanza delle caratteristiche

Il grafico relativo all'importanza delle caratteristiche mostra un punteggio che indica l'importanza di ogni caratteristica nella creazione di un modello. È possibile esaminare l'importanza delle caratteristiche per l'intero modello e per classe di un modello predittivo. È inoltre possibile visualizzare il confronto dell'importanza di ogni caratteristica rispetto alle singole classi e al modello complessivo.

![Spiegabilità delle caratteristiche](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>Notebook di esempio
I notebook seguenti illustrano i concetti descritti in questo articolo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training\train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Eseguire i passaggi seguenti per imparare a usare Azure Machine Learning SDK per Python:

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).

* Altre informazioni su come [eseguire il training di modelli di PyTorch con Azure Machine Learning](how-to-train-pytorch.md).
