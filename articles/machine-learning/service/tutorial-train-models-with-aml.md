---
title: 'Esercitazione: Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning'
description: Questa esercitazione mostra come usare il servizio Azure Machine Learning per eseguire il training di un modello di classificazione delle immagini con scikit-learn in un notebook Jupyter per Python. Questa esercitazione è la prima di una serie in due parti.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 6fbca5e83d8ab4b3c34c6448c7a2303697da623b
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181399"
---
# <a name="tutorial-1-train-an-image-classification-model-with-azure-machine-learning"></a>Esercitazione n.1: Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning

In questa esercitazione si eseguirà il training di un modello di Machine Learning sia in locale che su risorse di calcolo remote. Si userà il flusso di lavoro di training e distribuzione per il servizio Azure Machine Learning (anteprima) in un notebook Jupyter per Python.  È quindi possibile usare il notebook come modello per eseguire il training di un modello di Machine Learning con i propri dati di training. Questa esercitazione è la **prima di una serie in due parti**.  

Questa esercitazione esegue il training di una semplice regressione logistica usando il set di dati [MNIST](http://yann.lecun.com/exdb/mnist/) e [scikit-learn](http://scikit-learn.org) con Azure Machine Learning.  MNIST è un set di dati noto costituito da 70.000 immagini in scala di grigi. Ogni immagine è una cifra scritta a mano di 28x28 pixel, che rappresenta un numero compreso tra 0 e 9. L'obiettivo è creare un classificatore multiclasse per identificare la cifra rappresentata da una determinata immagine. 

È possibile passare agli argomenti seguenti:

> [!div class="checklist"]
> * Configurazione dell'ambiente di sviluppo
> * Accedere ai dati ed esaminarli
> * Eseguire il training di una semplice regressione logistica con la nota libreria di Machine Learning scikit-learn 
> * Eseguire il training di più modelli in un cluster remoto
> * Verificare i risultati del training e registrare il modello migliore

Si vedrà come selezionare un modello e distribuirlo nella [seconda parte di questa esercitazione](tutorial-deploy-models-with-aml.md) in seguito. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="get-the-notebook"></a>Ottenere il notebook

Per comodità, questa esercitazione è disponibile anche come notebook di Jupyter. Usare uno dei due metodi seguenti per clonare il [repository GitHub di notebook di esempio di Machine Learning](https://github.com/Azure/MachineLearningNotebooks) ed eseguire il notebook `tutorials/01.train-models.ipynb`:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Tutte le impostazioni per il lavoro di sviluppo possono essere eseguite in un notebook di Python.  La configurazione include:

* Importazione di pacchetti Python
* Connessione a un'area di lavoro per abilitare la comunicazione tra il computer locale e le risorse remote
* Creazione di un esperimento per tenere traccia di tutte le esecuzioni
* Creazione di una destinazione di calcolo remota da usare per il training

### <a name="import-packages"></a>Importare i pacchetti

Importare i pacchetti Python necessari in questa sessione. Visualizzare anche la versione di Azure Machine Learning SDK.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt

import azureml
from azureml.core import Workspace, Run

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-workspace"></a>Connettersi all'area di lavoro

Creare un oggetto area di lavoro dall'area di lavoro esistente. `Workspace.from_config()` legge il file **config.json** e carica i dettagli in un oggetto denominato `ws`.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-experiment"></a>Creare un esperimento

Creare un esperimento per tenere traccia di tutte le esecuzioni nell'area di lavoro. Un'area di lavoro può includere più esperimenti. 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-remote-compute-target"></a>Creare la destinazione di calcolo remota

Azure Batch per intelligenza artificiale è un servizio gestito che consente ai data scientist di eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure, incluse le macchine virtuali senza supporto GPU.  In questa esercitazione verrà creato un cluster di Azure Batch per intelligenza artificiale come ambiente di training. Questo codice crea un cluster se non esiste già nell'area di lavoro. 

 **La creazione di un cluster richiede circa 5 minuti.** Se l'area di lavoro include già un cluster, questo codice lo usa e ignora il processo di creazione.


```python
from azureml.core.compute import ComputeTarget, BatchAiCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
batchai_cluster_name = "traincluster"

try:
    # look for the existing cluster by name
    compute_target = ComputeTarget(workspace=ws, name=batchai_cluster_name)
    if type(compute_target) is BatchAiCompute:
        print('found compute target {}, just use it.'.format(batchai_cluster_name))
    else:
        print('{} exists but it is not a Batch AI cluster. Please choose a different name.'.format(batchai_cluster_name))
except ComputeTargetException:
    print('creating a new compute target...')
    compute_config = BatchAiCompute.provisioning_configuration(vm_size="STANDARD_D2_V2", # small CPU-based VM
                                                                #vm_priority='lowpriority', # optional
                                                                autoscale_enabled=True,
                                                                cluster_min_nodes=0, 
                                                                cluster_max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, compute_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it uses the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
    # Use the 'status' property to get a detailed status for the current cluster. 
    print(compute_target.status.serialize())
```

Sono ora disponibili i pacchetti e le risorse di calcolo necessari per eseguire il training di un modello nel cloud. 

## <a name="explore-data"></a>Esplorazione dei dati

Prima di eseguire il training di un modello, è necessario conoscere i dati usati per il training.  È anche necessario copiare i dati nel cloud in modo che siano accessibili dall'ambiente di training nel cloud.  In questa sezione si apprenderà come:

* Scaricare il set di dati MNIST
* Visualizzare alcune immagini di esempio
* Caricare dati nel cloud

### <a name="download-the-mnist-dataset"></a>Scaricare il set di dati MNIST

Scaricare il set di dati MNIST e salvare i file in una directory `data` in locale.  Vengono scaricate immagini ed etichette per training e test.  


```python
import os
import urllib.request

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename='./data/train-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename='./data/train-labels.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/test-labels.gz')
```

### <a name="display-some-sample-images"></a>Visualizzare alcune immagini di esempio

Caricare i file compressi in matrici `numpy`. Usare quindi `matplotlib` per tracciare 30 immagini casuali dal set di dati con le etichette al di sopra. Per questo passaggio è necessaria una funzione `load_data` inclusa nel file `util.py`. Questo file è incluso nella cartella degli esempi. Assicurarsi che si trovi nella stessa cartella del notebook. La funzione `load_data` analizza i file compressi in matrici numpy.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data('./data/train-images.gz', False) / 255.0
y_train = load_data('./data/train-labels.gz', True).reshape(-1)

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

La figura seguente rappresenta un campione casuale di immagini:

![campione casuale di immagini](./media/tutorial-train-models-with-aml/digits.png)

Questo è l'aspetto delle immagini e dei risultati di stima previsti.

### <a name="upload-data-to-the-cloud"></a>Caricare dati nel cloud

A questo punto, è necessario rendere accessibili i dati in remoto, caricandoli dal computer locale in Azure, in modo che siano accessibili per il training remoto. L'archivio dati è un costrutto pratico associato all'area di lavoro per il caricamento e il download dei dati e supporta le interazioni dalle destinazioni di calcolo remote. È supportato dall'account di archiviazione BLOB di Azure.

I file MNIST vengono caricati in una directory denominata `mnist` alla radice dell'archivio dati.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir='./data', target_path='mnist', overwrite=True, show_progress=True)
```
È ora disponibile tutto ciò che occorre per iniziare il training di un modello. 

## <a name="train-a-model-locally"></a>Eseguire il training di un modello in locale

Eseguire il training di un semplice modello di regressione logistica da scikit-learn in locale.

Il **training in locale può richiedere un minuto o due** a seconda della configurazione del computer.

```python
%%time
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression()
clf.fit(X_train, y_train)
```

Successivamente, eseguire stime usando il set di test e calcolare l'accuratezza. 

```python
y_hat = clf.predict(X_test)
print(np.average(y_hat == y_test))
```

L'accuratezza del modello locale è la seguente:

`0.9202`

Sono sufficienti poche righe di codice per ottenere una precisione del 92%.

## <a name="train-on-a-remote-cluster"></a>Eseguire il training in un cluster remoto

A questo punto è possibile espandere questo semplice modello compilando un modello con un tasso di regolarizzazione diverso. Questa volta, il training del modello verrà eseguito su una risorsa remota.  

Per questa attività, inviare il processo al cluster di training remoto impostato in precedenza.  Per inviare un processo, è necessario:
* Creare una directory
* Creare uno script di training
* Creare un oggetto di stima
* Inviare il processo 

### <a name="create-a-directory"></a>Creare una directory

Creare una directory per distribuire il codice necessario dal computer alla risorsa remota.

```python
import os
script_folder = './sklearn-mnist'
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Creare uno script di training

Per inviare il processo al cluster, prima di tutto creare uno script di training. Eseguire il codice seguente per creare lo script di training chiamato `train.py` nella directory appena creata. Questo training aggiunge un tasso di regolarizzazione all'algoritmo di training, pertanto produce un modello leggermente diverso rispetto alla versione locale.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = os.path.join(args.data_folder, 'mnist')
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_submitted_run()

print('Train a logistic regression model with regularizaion rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Si noti come lo script ottiene i dati e salva i modelli:

+ Lo script di training legge un argomento per trovare la directory contenente i dati.  Quando si invia il processo in un secondo momento, si fa riferimento all'archivio dati per questo argomento: `parser.add_argument('--data-folder', type = str, dest = 'data_folder', help = 'data directory mounting point')`

    
+ Lo script di training salva il modello in una directory denominata outputs. <br/>
`joblib.dump(value = clf, filename = 'outputs/sklearn_mnist_model.pkl')`<br/>
Qualsiasi elemento scritto in questa directory viene caricato automaticamente nell'area di lavoro. Si accederà al modello da questa directory più avanti nell'esercitazione.

Lo script di training fa riferimento al file `utils.py` per caricare il set di dati in modo corretto.  Copiare questo script nella cartella dello script in modo che sia accessibile insieme allo script di training nella risorsa remota.


```python
import shutil
shutil.copy('utils.py', script_folder)
```


### <a name="create-an-estimator"></a>Creare un oggetto di stima

Un oggetto di stima viene usato per inviare l'esecuzione.  Creare l'oggetto di stima eseguendo il codice seguente per definire:

* Il nome dell'oggetto di stima, `est`
* La directory contenente gli script. Tutti i file in questa directory vengono caricati nei nodi del cluster per l'esecuzione. 
* La destinazione di calcolo.  In questo caso si userà il cluster Batch per intelligenza artificiale creato
* Il nome dello script di training, train.py
* Parametri richiesti dallo script di training 
* Pacchetti Python necessari per il training

In questa esercitazione, questa destinazione è il cluster Batch per intelligenza artificiale. Tutti i file nella directory del progetto vengono caricati nei nodi del cluster per l'esecuzione. Viene impostato data_folder per usare l'archivio dati (`ds.as_mount()`).

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Inviare il processo al cluster

Eseguire l'esperimento inviando l'oggetto di stima.

```python
run = exp.submit(config=est)
run
```

Poiché la chiamata è asincrona, restituisce lo stato **Preparazione** o **In esecuzione** non appena viene avviato il processo.

## <a name="monitor-a-remote-run"></a>Monitorare un'esecuzione remota

In totale, la prima esecuzione richiede **circa 10 minuti**. Per le successive esecuzioni, purché le dipendenze dello script non cambino, viene però riutilizzata la stessa immagine e quindi il tempo di avvio del contenitore è molto più veloce.

Ecco ciò che avviene durante l'attesa:

- **Creazione dell'immagine**: viene creata un'immagine Docker corrispondente all'ambiente Python specificato dall'oggetto di stima. L'immagine viene caricata nell'area di lavoro. Per la creazione e il caricamento delle immagini sono richiesti **circa 5 minuti**. 

  Questa fase viene eseguita una volta per ogni ambiente Python, perché il contenitore viene memorizzato nella cache per le esecuzioni successive.  Durante la creazione dell'immagine, i log vengono trasmessi alla cronologia di esecuzione. È possibile monitorare lo stato di creazione dell'immagine usando questi log.

- **Ridimensionamento**: se il cluster remoto richiede più nodi per l'esecuzione di quelli attualmente disponibili, vengono aggiunti automaticamente altri nodi. Per il ridimensionamento sono in genere necessari **circa 5 minuti**.

- **Esecuzione**: in questa fase, gli script e i file necessari vengono inviati alla destinazione di calcolo, quindi gli archivi dati vengono montati/copiati e infine viene eseguito entry_script. Durante l'esecuzione del processo, stdout e la directory ./logs vengono trasmessi alla cronologia di esecuzione. È possibile monitorare lo stato dell'esecuzione usando questi log.

- **Post-elaborazione**: la directory ./outputs dell'esecuzione viene copiata nella cronologia di esecuzione nell'area di lavoro in modo che sia possibile accedere a questi risultati.


È possibile controllare lo stato di un processo in esecuzione in diversi modi. Questa esercitazione usa un widget di Jupyter oltre a un metodo `wait_for_completion`. 

### <a name="jupyter-widget"></a>Widget di Jupyter

Controllare lo stato dell'esecuzione con un widget di Jupyter.  Come per l'invio dell'esecuzione, il widget è asincrono e fornisce gli aggiornamenti in tempo reale ogni 10-15 secondi finché non viene completato il processo.


```python
from azureml.train.widgets import RunDetails
RunDetails(run).show()
```

Di seguito è riportato uno snapshot del widget visualizzato alla fine del training:

![Widget del notebook](./media/tutorial-train-models-with-aml/widget.png)

### <a name="get-log-results-upon-completion"></a>Risultati del recupero dei log dopo il completamento

Il training del modello e il monitoraggio avvengono in background. Attendere il completamento del training del modello prima di eseguire altro codice. Usare `wait_for_completion` per visualizzare quando viene completato il training del modello. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Visualizzare i risultati dell'esecuzione

È ora disponibile un modello sottoposto a training in un cluster remoto.  Recuperare l'accuratezza del modello:

```python
print(run.get_metrics())
```
L'output mostra che il modello remoto ha un'accuratezza leggermente superiore rispetto al modello locale, a causa dell'aggiunta del tasso di regolarizzazione durante il training.  

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

Nell'esercitazione sulla distribuzione, questo modello verrà esaminato in maggiore dettaglio.

## <a name="register-model"></a>Registrare il modello

L'ultimo passaggio nello script di training ha scritto il file `outputs/sklearn_mnist_model.pkl` in una directory denominata `outputs` nella macchina virtuale del cluster in cui viene eseguito il processo. `outputs` è una directory speciale, perché tutto il contenuto in questa directory viene caricato automaticamente nell'area di lavoro.  Questo contenuto viene visualizzato nel record di esecuzione nell'esperimento nell'area di lavoro. Il file del modello è pertanto ora disponibile nell'area di lavoro.

È possibile visualizzare i file associati a tale esecuzione.

```python
print(run.get_file_names())
```

Registrare il modello nell'area di lavoro in modo che l'utente che ha eseguito la procedura (o altri collaboratori) possano in un secondo momento eseguire query, esaminare e distribuire questo modello.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile eliminare solo il cluster di calcolo gestito di Azure. Tuttavia, poiché la scalabilità automatica è attivata e il valore minimo del cluster è 0, questa particolare risorsa non comporterà addebiti aggiuntivi per il calcolo quando non è in uso.


```python
# optionally, delete the Azure Managed Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione di Azure Machine Learning è stato usato Python per:

> [!div class="checklist"]
> * Configurazione dell'ambiente di sviluppo
> * Accedere ai dati ed esaminarli
> * Eseguire il training di una semplice regressione logistica con la nota libreria di Machine Learning scikit-learn
> * Eseguire il training di più modelli in un cluster remoto
> * Verificare i dettagli del training e registrare il modello migliore

Si è pronti per distribuire questo modello registrato seguendo le istruzioni nella parte successiva della serie di esercitazioni:

> [!div class="nextstepaction"]
> [Esercitazione 2 - Distribuire modelli](tutorial-deploy-models-with-aml.md)
