---
title: Eseguire il training e registrare i modelli di TensorFlow
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come eseguire il training e registrare un modello TensorFlow usando il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: 314917ce91407206d786b191df118893696ac82c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417136"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Eseguire il training e di registrare modelli TensorFlow su larga scala con il servizio di Azure Machine Learning

Questo articolo illustra come eseguire il training e registrare un modello TensorFlow usando il servizio di Azure Machine Learning. Verrà usato il famoso [set di dati MNIST](http://yann.lecun.com/exdb/mnist/) per classificare le cifre scritte a mano usando una rete neurale profonda basata su TensorFlow.

Con il servizio di Azure Machine Learning, sarà in grado di aumentare rapidamente i processi di training open source usando le risorse di calcolo cloud elastico. È anche possibile monitorare le esecuzioni di training, i modelli di versione, la distribuzione di modelli e molto altro ancora. 

Se si sta sviluppando un modello TensorFlow da zero o si desidera introdurre un modello esistente nel cloud, è possibile compilare modelli pronti per la produzione con il servizio di Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

- Installare Azure Machine Learning SDK per Python
- Facoltativo: Creare un file di configurazione dell'area di lavoro
- Scaricare il [i file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` e `utils.py`

È possibile seguire le [Guida all'installazione di Python SDK](setup-create-workspace.md#sdk) per istruzioni dettagliate su come configurare l'ambiente. I file di formazione di esempio sono reperibili nel nostro [pagina di GitHub degli esempi](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) insieme alla versione di Juypter Notebook estesa, di questa Guida.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, è necessario importare le librerie di Python necessari.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L'oggetto dell'area di lavoro è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli elementi creati.

Se è stato completato il passaggio facoltativo nella [sezione relativa ai prerequisiti](#prerequisites), è possibile usare `Workspace.from_config()` creare rapidamente un oggetto dell'area di lavoro da dettagli archiviati nel file di configurazione.

```Python
ws = Workspace.from_config()
```

È possibile anche possibile creare un'area di lavoro in modo esplicito.

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento e una cartella per contenere gli script di training. In questo esempio, creare un esperimento denominato "tf-mnist"

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Caricare set di dati e gli script

Il [datastore](how-to-access-data.md) è una posizione in cui i dati possono essere archiviati e a cui accede il montaggio o copiare i dati nella destinazione di calcolo. Ogni area di lavoro fornisce un archivio dati predefinito. Microsoft carica i dati e script di training in modo che siano accessibili facilmente durante il training.

1. Scaricare localmente il set di dati MNIST

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Caricare il set di dati MNIST nell'archivio dati predefinito.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Caricare lo script di training, TensorFlow `tf_mnist.py`e il file di helper, `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per il processo TensorFlow eseguire nell'ambito. In questo esempio viene creato un cluster AmlCompute abilitate per GPU. Per un elenco di risorse di formazione disponibili destinazioni di calcolo, vedere [in questo articolo](how-to-set-up-training-targets.md#compute-targets-for-training)

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="create-a-tensorflow-estimator"></a>Creare un Estimatore TensorFlow

Il [estimator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornisce un modo semplice per l'avvio di un processo di training TensorFlow in una destinazione di calcolo. Un'immagine docker che ha installato TensorFlow fornirà automaticamente.

È possibile includere altri pacchetti pip o conda nell'immagine docker risultante passando i nomi tramite il `pip_packages` e `conda_packages` argomenti.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='tf_mnist.py',
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Invia un'esecuzione

Il [oggetto esecuzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia per la cronologia di esecuzione durante l'esecuzione del processo e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

L'esecuzione viene eseguita, passerà attraverso le fasi seguenti:

- **Preparazione**: In base alla stima di TensorFlow viene creata un'immagine docker. L'immagine viene caricata in Registro contenitori dell'area di lavoro e memorizzato nella cache per le esecuzioni successive. I log vengono anche trasmessi per la cronologia di esecuzione e possono essere consultati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster verrà eseguito un tentativo di aumentare le prestazioni se il cluster Batch per intelligenza artificiale richiede più nodi per l'esecuzione dell'esecuzione rispetto a quelli attualmente disponibili.

- **In esecuzione**: Tutti gli script nella cartella dello script vengono caricati nella destinazione di calcolo, archivi dati vengono montati o copiati e viene eseguito il entry_script. Output di stdout e. / cartella dei log vengono inviati nel flusso per la cronologia di esecuzione e può essere usato per monitorare l'esecuzione.

- **Post-elaborazione**: I. / output dell'esecuzione della cartella da copiare la cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training modello, è possibile registrarlo per l'area di lavoro. Registrazione del modello consente di store e la versione dei modelli nell'area di lavoro per semplificare [modello di distribuzione e gestione](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

È anche possibile scaricare una copia locale del modello usando l'oggetto di esecuzione. Nello script di training `mnist-tf.py`, un oggetto di screen saver TensorFlow viene mantenuto il modello in una cartella locale (computer locale per la destinazione di calcolo). È possibile usare l'oggetto di esecuzione per scaricare una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Training distribuito

Il [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator supporta anche l'addestramento distribuito tra i cluster CPU e GPU. È possibile eseguire facilmente i processi TensorFlow distribuiti e servizio Azure Machine Learning gestiranno l'orchestrazione per l'utente.

Servizio Azure Machine Learning supporta due metodi di addestramento distribuito in TensorFlow:

* [Basato su MPI](https://www.open-mpi.org/) distribuite training usando il [Horovod](https://github.com/uber/horovod) framework
* Nativi [TensorFlow distribuiti](https://www.tensorflow.org/deploy/distributed) usando il metodo server parametro

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) è un framework open source per l'addestramento distribuito sviluppato da Uberjar. Offre un percorso facilitato per i processi GPU TensorFlow distribuiti.

Per utilizzare Horovod, specificare `mpi` per il `distributed_training` parametro nel costruttore estimator TensorFlow. Horovod verrà installato da usare nello script di training.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Server dei parametri

È anche possibile eseguire [TensorFlow distribuito nativo](https://www.tensorflow.org/deploy/distributed), che usa il modello del server dei parametri. In questo modo si esegue il training in un cluster di server dei parametri e ruoli di lavoro. I ruoli di lavoro calcolano i gradienti durante il training, mentre i server dei parametri aggregano i gradienti.

Per usare il parametro di metodo di server, specificare `ps` per il `distributed_training` parametro nel costruttore estimator TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='ps',
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Nota su `TF_CONFIG`

È necessario anche gli indirizzi di rete e porte del cluster per il [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), in modo che Azure Machine Learning imposta il `TF_CONFIG` variabile di ambiente per l'utente.

La variabile di ambiente `TF_CONFIG` è una stringa JSON. Di seguito è riportato un esempio della variabile per un server dei parametri:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Per il livello elevato di TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow analizzerà ciò `TF_CONFIG` variabile e il cluster di compilazione specifiche per l'utente.

Per core di livello inferiore di TensorFlow API per il training, analizzare il `TF_CONFIG` variabile e compilazione di `tf.train.ClusterSpec` del codice di training. In [questo esempio](https://aka.ms/aml-notebook-tf-ps) si eseguirebbe questa operazione nel **proprio script di training** come segue:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, viene sottoposto a training e registrato un modello TensorFlow nel servizio di Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione modello.

> [!div class="nextstepaction"]
> [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
