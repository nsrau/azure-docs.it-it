---
title: 'Esercitazione sulla classificazione di immagini: Eseguire il training dei modelli'
titleSuffix: Azure Machine Learning service
description: Informazioni su come eseguire il training di un modello di classificazione delle immagini con scikit-learn in un notebook Jupyter per Python con il servizio Azure Machine Learning. Questa esercitazione è la prima di una serie in due parti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: df5085011fd2771f094131244c1f466cebcbc89a
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534794"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Esercitazione: Eseguire il training di modelli di classificazione delle immagini con dati MNIST e scikit-learn usando Azure Machine Learning

In questa esercitazione si eseguirà il training di un modello di Machine Learning su risorse di calcolo remote. Si userà il flusso di lavoro di training e distribuzione per il servizio Azure Machine Learning (anteprima) in un notebook Jupyter per Python.  È quindi possibile usare il notebook come modello per eseguire il training di un modello di Machine Learning con i propri dati di training. Questa esercitazione è la **prima di una serie in due parti**.  

Questa esercitazione esegue il training di una regressione logistica semplice usando il set di dati [MNIST](http://yann.lecun.com/exdb/mnist/) e [scikit-learn](https://scikit-learn.org) con il servizio Azure Machine Learning. MNIST è un set di dati noto costituito da 70.000 immagini in scala di grigi. Ogni immagine è una cifra in stile scrittura a mano di 28x28 pixel, che rappresenta un numero compreso tra zero e nove. L'obiettivo è creare un classificatore multiclasse per identificare la cifra rappresentata da una determinata immagine.

Si apprenderà a eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Configurare l'ambiente di sviluppo.
> * Accedere ai dati ed esaminarli.
> * Eseguire il training di un semplice modello di regressione logistica in un cluster remoto.
> * Verificare i risultati del training e registrare il modello migliore.

Nella [seconda parte di questa esercitazione](tutorial-deploy-models-with-aml.md) si vedrà come selezionare un modello e distribuirlo.

Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

>[!NOTE]
> Il codice di questo articolo è stato testato con Azure Machine Learning SDK versione 1.0.41.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'[Esercitazione: Introduzione alla creazione del primo esperimento di Machine Learning](tutorial-1st-experiment-sdk-setup.md) per:
    * Creare un'area di lavoro
    * Creare un server notebook cloud
    * Avviare il dashboard di Jupyter Notebook

* Dopo aver avviato il dashboard di Jupyter Notebook, aprire il notebook **tutorials/img-classification-part1-training.ipynb**.

Questa esercitazione e il file **utils.py** associato sono disponibili in [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) se si vuole usarli nel proprio [ambiente locale](how-to-configure-environment.md#local).  Verificare di aver installato `matplotlib` e `scikit-learn` nell'ambiente.


## <a name="start"></a>Configurare l'ambiente di sviluppo

Tutte le impostazioni per il lavoro di sviluppo possono essere eseguite in un notebook di Python. La configurazione include le azioni seguenti:

* Importare i pacchetti Python.
* Connettersi a un'area di lavoro per consentire al computer locale di comunicare con risorse remote.
* Creare un esperimento per tenere traccia di tutte le esecuzioni.
* Creare una destinazione di calcolo remota da usare per il training.

### <a name="import-packages"></a>Importare pacchetti

Importare i pacchetti Python necessari in questa sessione. Visualizzare anche la versione di Azure Machine Learning SDK:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Connettersi a un'area di lavoro

Creare un oggetto area di lavoro dall'area di lavoro esistente. `Workspace.from_config()` legge il file **config.json** e carica i dettagli in un oggetto denominato `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento per tenere traccia di tutte le esecuzioni nell'area di lavoro. Un'area di lavoro può includere più esperimenti:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Creare una risorsa di calcolo o collegarne una esistente

Con l'ambiente di calcolo di Azure Machine Learning, un servizio gestito, i data scientist possono eseguire il training di modelli di Machine Learning in cluster di macchine virtuali di Azure, ad esempio le macchine virtuali con supporto per GPU. In questa esercitazione verrà creato l'ambiente di calcolo di Azure Machine Learning come ambiente di training. Il codice seguente crea automaticamente i cluster di calcolo se non esistono già nell'area di lavoro.

 **Per la creazione dell'ambiente di calcolo sono necessari circa cinque minuti.** Se l'ambiente di calcolo è già presente nell'area di lavoro, il codice usa tale ambiente e ignora il processo di creazione.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Sono ora disponibili i pacchetti e le risorse di calcolo necessari per eseguire il training di un modello nel cloud.

## <a name="explore-data"></a>Esplorazione dei dati

Prima di eseguire il training di un modello, è necessario conoscere i dati usati per il training. È inoltre necessario copiare i dati nel cloud in modo che siano accessibili dall'ambiente di training nel cloud. In questa sezione si apprenderà a eseguire le operazioni seguenti:

* Scaricare il set di dati MNIST.
* Visualizzare alcune immagini di esempio.
* Caricare i dati nel cloud.

### <a name="download-the-mnist-dataset"></a>Scaricare il set di dati MNIST

Scaricare il set di dati MNIST e salvare i file in una directory `data` in locale. Vengono scaricate immagini ed etichette per training e test:

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

L'output sarà simile al seguente: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Visualizzare alcune immagini di esempio

Caricare i file compressi in matrici `numpy`. Usare quindi `matplotlib` per tracciare 30 immagini casuali dal set di dati con le etichette al di sopra. Per questo passaggio è necessaria una funzione `load_data` inclusa in un file `util.py`. Questo file è incluso nella cartella degli esempi. Assicurarsi che si trovi nella stessa cartella del notebook. La funzione `load_data` si limita ad analizzare i file compressi in matrici numpy:

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
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

![Campione casuale di immagini](./media/tutorial-train-models-with-aml/digits.png)

Questo è l'aspetto delle immagini e dei risultati di stima previsti.

### <a name="upload-data-to-the-cloud"></a>Caricare dati nel cloud

A questo punto, è necessario rendere accessibili i dati in remoto, caricandoli dal computer locale in Azure, in modo che siano accessibili per il training remoto. L'archivio dati è un costrutto pratico associato all'area di lavoro per il caricamento e il download dei dati. È possibile interagire con l'archivio dati anche da destinazioni di calcolo remote. È supportato da un account di archiviazione BLOB di Azure.

I file MNIST vengono caricati in una directory denominata `mnist` alla radice dell'archivio dati:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist',
          overwrite=True, show_progress=True)
```

È ora disponibile tutto ciò che occorre per iniziare il training di un modello.

## <a name="train-on-a-remote-cluster"></a>Eseguire il training in un cluster remoto

Per questa attività, inviare il processo al cluster di training remoto impostato in precedenza.  Per inviare un processo, è necessario:
* Creare una directory
* Creare uno script di training
* Creare un oggetto di stima
* Inviare il processo

### <a name="create-a-directory"></a>Creare una directory

Creare una directory per distribuire il codice necessario dal computer alla risorsa remota.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Creare uno script di training

Per inviare il processo al cluster, prima di tutto creare uno script di training. Eseguire il codice seguente per creare lo script di training chiamato `train.py` nella directory appena creata.

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

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
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

+ Lo script di training legge un argomento per trovare la directory che contiene i dati. Quando si invia il processo in un secondo momento, si fa riferimento all'archivio dati per questo argomento: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Lo script di training salva il modello in una directory denominata **outputs**. Qualsiasi elemento scritto in questa directory viene caricato automaticamente nell'area di lavoro. Si accederà al modello da questa directory più avanti nell'esercitazione. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Lo script di training necessita del file `utils.py` per il caricamento corretto del set di dati. Il codice seguente copia `utils.py` in `script_folder` in modo che il file sia accessibile insieme allo script di training nella risorsa remota.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Creare un oggetto di stima

Per inviare l'esecuzione, si usa un oggetto [estimator di SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py). Creare l'oggetto estimator eseguendo il codice seguente per definire questi elementi:

* Il nome dell'oggetto estimator, `est`.
* La directory contenente gli script. Tutti i file in questa directory vengono caricati nei nodi del cluster per l'esecuzione.
* La destinazione di calcolo. In questo caso si usa il cluster di calcolo di Azure Machine Learning che è stato creato.
* Il nome dello script di training, **train.py**.
* I parametri richiesti dallo script di training.

In questa esercitazione la destinazione è AmlCompute. Tutti i file nella cartella dello script vengono caricati nei nodi del cluster per l'esecuzione. Il parametro **data_folder** viene impostato per l'uso dell'archivio dati, `ds.path('mnist').as_mount()`:

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Inviare il processo al cluster

Eseguire l'esperimento inviando l'oggetto estimator:

```python
run = exp.submit(config=est)
run
```

Poiché la chiamata è asincrona, restituisce lo stato **Preparazione** o **In esecuzione** non appena viene avviato il processo.

## <a name="monitor-a-remote-run"></a>Monitorare un'esecuzione remota

In totale, la prima esecuzione richiede **circa 10 minuti**. Tuttavia, per le esecuzioni successive, purché le dipendenze dello script rimangano invariate, viene riutilizzata la stessa immagine. Il tempo di avvio del contenitore è quindi molto più veloce.

Cosa accade durante l'attesa:

- **Creazione di immagini**: viene creata un'immagine Docker corrispondente all'ambiente Python specificato dall'oggetto estimator. L'immagine viene caricata nell'area di lavoro. Per la creazione e il caricamento delle immagini sono necessari **circa cinque minuti**.

  Questa fase viene eseguita una volta per ogni ambiente Python, perché il contenitore viene memorizzato nella cache per le esecuzioni successive. Durante la creazione dell'immagine, i log vengono trasmessi alla cronologia di esecuzione. È possibile monitorare lo stato di avanzamento del processo di creazione dell'immagine usando questi log.

- **Ridimensionamento**: se il cluster remoto richiede più nodi per l'esecuzione rispetto a quelli attualmente disponibili, vengono aggiunti automaticamente altri nodi. Per il ridimensionamento sono in genere necessari **circa cinque minuti**.

- **In esecuzione**: in questa fase, gli script e i file necessari vengono inviati alla destinazione di calcolo. Gli archivi dati vengono montati o copiati. Viene infine eseguito **entry_script**. Durante l'esecuzione del processo, **stdout** e la directory **./logs** vengono trasmessi alla cronologia di esecuzione. È possibile monitorare lo stato di avanzamento dell'esecuzione usando questi log.

- **Post-elaborazione**: la directory **./outputs** dell'esecuzione viene copiata nella cronologia di esecuzione nell'area di lavoro in modo da rendere accessibili questi risultati.

È possibile controllare lo stato di avanzamento di un processo in esecuzione in diversi modi. Questa esercitazione usa un widget di Jupyter e un metodo `wait_for_completion`.

### <a name="jupyter-widget"></a>Widget di Jupyter

Controllare lo stato dell'esecuzione con un [widget di Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Come per l'invio dell'esecuzione, il widget è asincrono e fornisce aggiornamenti in tempo reale ogni 10-15 secondi finché non viene completato il processo:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Alla fine del training, l'aspetto del widget sarà simile al seguente:

![Widget del notebook](./media/tutorial-train-models-with-aml/widget.png)

Per cancellare un'esecuzione, seguire [queste istruzioni](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Ottenere i risultati del log dopo il completamento

Il training del modello e il monitoraggio avvengono in background. Attendere il completamento del training del modello prima di eseguire altro codice. Usare `wait_for_completion` per visualizzare quando viene completato il training del modello:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Visualizzare i risultati dell'esecuzione

È ora disponibile un modello sottoposto a training in un cluster remoto. Recuperare l'accuratezza del modello:

```python
print(run.get_metrics())
```

L'output mostra che il modello remoto ha un'accuratezza di 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

Nell'esercitazione successiva questo modello viene esaminato in maggiore dettaglio.

## <a name="register-model"></a>Registrare il modello

L'ultimo passaggio nello script di training ha scritto il file `outputs/sklearn_mnist_model.pkl` in una directory denominata `outputs` nella macchina virtuale del cluster in cui viene eseguito il processo. `outputs` è una directory speciale perché tutto il contenuto di questa directory viene caricato automaticamente nell'area di lavoro. Questo contenuto viene visualizzato nel record di esecuzione nell'esperimento nell'area di lavoro. Il file del modello è pertanto ora disponibile nell'area di lavoro.

È possibile visualizzare i file associati a tale esecuzione:

```python
print(run.get_file_names())
```

Registrare il modello nell'area di lavoro in modo che l'utente che ha eseguito la procedura, o altri collaboratori, possano in un secondo momento eseguire query, esaminare e distribuire questo modello:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile eliminare solo il cluster dell'ambiente di calcolo di Azure Machine Learning. Tuttavia, la scalabilità automatica è attivata e il valore minimo del cluster è zero. Questa particolare risorsa non comporterà quindi addebiti aggiuntivi per il calcolo quando non è in uso:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sul servizio Azure Machine Learning è stato usato Python per le attività seguenti:

> [!div class="checklist"]
> * Configurare l'ambiente di sviluppo.
> * Accedere ai dati ed esaminarli.
> * Eseguire il training di più modelli in un cluster remoto con la nota libreria di Machine Learning scikit-learn
> * Verificare i dettagli del training e registrare il modello migliore.

Si è pronti per distribuire questo modello registrato seguendo le istruzioni nella parte successiva della serie:

> [!div class="nextstepaction"]
> [Esercitazione 2 - Distribuire modelli](tutorial-deploy-models-with-aml.md)
