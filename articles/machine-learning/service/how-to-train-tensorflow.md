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
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 9961129805d133c4512e40e4c8be80185316a1ce
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074914"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Eseguire il training e di registrare modelli TensorFlow su larga scala con il servizio di Azure Machine Learning

Questo articolo illustra come eseguire il training e registrare un modello TensorFlow usando il servizio di Azure Machine Learning. Usa il diffuso [set di dati MNIST](http://yann.lecun.com/exdb/mnist/) classificare cifre scritte a mano usando una rete neurale profonda compilata utilizzando il [libreria Python di TensorFlow](https://www.tensorflow.org/overview).

TensorFlow è un framework di elaborazione open source comunemente usato per creare le reti neurali profonde (DNN). Con il servizio di Azure Machine Learning, è possibile aumentare rapidamente i processi di training open source con le risorse di calcolo cloud elastico. È inoltre possibile monitorare le esecuzioni di training, i modelli di versione, la distribuzione di modelli e molto altro ancora.

Se si sta sviluppando un modello TensorFlow da zero o si desidera introdurre un modello esistente nel cloud, servizio Azure Machine Learning consentono di compilare modelli pronti per la produzione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).
- [Installare Azure Machine Learning SDK per Python](setup-create-workspace.md#sdk)
- [Creare un file di configurazione dell'area di lavoro](setup-create-workspace.md#write-a-configuration-file)
- [Scaricare i file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` e `utils.py`

È anche possibile trovare un completate [versione di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) di questa Guida nella pagina di esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione degli iperparametri intelligenti, la distribuzione del modello e i widget di notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

In questa sezione si configura l'esperimento di training per il caricamento di pacchetti python necessari, l'inizializzazione di un'area di lavoro, creazione di un esperimento e caricare i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, importare le librerie di Python necessari.

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

Il [dell'area di lavoro di Azure Machine Learning servizio](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK, è possibile accedere gli elementi dell'area di lavoro mediante la creazione di un [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) oggetto.

Crea un oggetto dell'area di lavoro dal `config.json` creato nel file il [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento e una cartella per contenere gli script di training. In questo esempio, creare un esperimento denominato "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Caricare set di dati e gli script

Il [datastore](how-to-access-data.md) è una posizione in cui i dati possono essere archiviati e a cui accede il montaggio o copiare i dati nella destinazione di calcolo. Ogni area di lavoro fornisce un archivio dati predefinito. Caricare i dati e script di training per l'archivio dati in modo che sono facilmente accessibili durante il training.

1. Scaricare localmente il set di dati MNIST.

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

Creare una destinazione di calcolo per il processo TensorFlow eseguire nell'ambito. In questo esempio, creare un cluster di calcolo abilitate per GPU di Azure Machine Learning.

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

Per altre informazioni sulle destinazioni di calcolo, vedere la [What ' s una destinazione di calcolo](concept-compute-target.md) articolo.

## <a name="create-a-tensorflow-estimator"></a>Creare un Estimatore TensorFlow

Il [estimator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornisce un modo semplice per l'avvio di un processo di training TensorFlow in una destinazione di calcolo.

Lo strumento di stima TensorFlow viene implementata tramite il tipo generico [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) (classe), che può essere usato per supportare qualsiasi framework. Per altre informazioni sui training dei modelli utilizzando lo strumento di stima generico, vedere [il training dei modelli con Azure Machine Learning con strumento di stima](how-to-train-ml-models.md)

Se lo script di training deve aggiuntive pip o conda pacchetti da eseguire, è possibile usare i pacchetti installati nell'immagine docker risultante passando i nomi tramite il `pip_packages` e `conda_packages` argomenti.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Invia un'esecuzione

Il [oggetto esecuzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia per la cronologia di esecuzione durante l'esecuzione del processo e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

L'esecuzione viene eseguita, la chiamata passa attraverso le fasi seguenti:

- **Preparazione**: In base alla stima di TensorFlow viene creata un'immagine docker. L'immagine viene caricata in Registro contenitori dell'area di lavoro e memorizzato nella cache per le esecuzioni successive. I log vengono anche trasmessi per la cronologia di esecuzione e possono essere consultati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster tenta di aumentare le prestazioni se il cluster Batch per intelligenza artificiale richiede più nodi per l'esecuzione dell'esecuzione rispetto a quelli attualmente disponibili.

- **In esecuzione**: Tutti gli script nella cartella dello script vengono caricati nella destinazione di calcolo, archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e. / cartella dei log vengono inviati nel flusso per la cronologia di esecuzione e può essere usato per monitorare l'esecuzione.

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

- [Basato su MPI](https://www.open-mpi.org/) distribuite training usando il [Horovod](https://github.com/uber/horovod) framework
- Nativi [TensorFlow distribuiti](https://www.tensorflow.org/deploy/distributed) usando il metodo server parametro

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) è un framework open source per l'addestramento distribuito sviluppato da Uberjar. Offre un percorso facilitato per i processi GPU TensorFlow distribuiti.

Per utilizzare Horovod, specificare un [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) dell'oggetto per il `distributed_training` parametro nel costruttore TensorFlow. Questo parametro assicura che library Horovod viene installato da usare nello script di training.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Server dei parametri

È anche possibile eseguire [TensorFlow distribuito nativo](https://www.tensorflow.org/deploy/distributed), che usa il modello del server dei parametri. In questo modo si esegue il training in un cluster di server dei parametri e ruoli di lavoro. I ruoli di lavoro calcolano i gradienti durante il training, mentre i server dei parametri aggregano i gradienti.

Per usare il parametro di metodo di server, specificare una [ `TensorflowConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) dell'oggetto per il `distributed_training` parametro nel costruttore TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>Definire specifiche del cluster in 'TF_CONFIG'

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

Per il livello elevato di TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow analizza il `TF_CONFIG` variabile e compilazioni specifiche del cluster per l'utente.

Per core di livello inferiore di TensorFlow API per il training, analizzare il `TF_CONFIG` variabile e compilazione di `tf.train.ClusterSpec` del codice di training.

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
