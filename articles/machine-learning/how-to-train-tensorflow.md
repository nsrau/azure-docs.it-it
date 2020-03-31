---
title: Eseguire il training e distribuire un modello TensorFlow
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire script di training TensorFlow su larga scala usando Azure Machine Learning.Learn how to run TensorFlow training scripts at scale using Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228301"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Crea un modello di deep learning TensorFlow su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire gli script di training TensorFlow su larga scala usando la classe di stima TensorFlow di Azure Machine Learning.This article shows you how to run your [TensorFlow](https://www.tensorflow.org/overview) training scripts at scale using Azure Machine Learning's [TensorFlow estimator estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) class. Questo esempio analizza e registra un modello TensorFlow per classificare le cifre scritte a mano usando una rete neurale completa (DNN).

Sia che tu stia sviluppando un modello TensorFlow da zero o che tu stia portando un [modello esistente](how-to-deploy-existing-model.md) nel cloud, puoi usare Azure Machine Learning per scalare orizzontalmente i processi di formazione open source per creare, distribuire, versione e monitorare modelli di livello di produzione.

Ulteriori informazioni sul [deep learning e sull'apprendimento automatico](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:Run this code on either of these environments:

 - Istanza di calcolo di Azure Machine Learning: non sono necessari download o installazioni

     - Completare [l'esercitazione: Ambiente](tutorial-1st-experiment-sdk-setup.md) di installazione e area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella di deep learning degli esempi nel server notebook, trovare un blocco appunti completato ed espanso passando a questa directory: **how-to-use-azureml > ml-frameworks > tensorflow > distribuzione > cartella train-hyperparameter-tune-deploy-with-tensorflow.** 
 
 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Creare un file di [configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` di script di esempio e`utils.py`
     
    È inoltre possibile trovare una [versione completata Di Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) di questa guida nella pagina degli esempi di GitHub.You can also find a completed Jupyter Notebook version of this guide on the GitHub samples page. Il blocco appunti include sezioni espanse che includono l'ottimizzazione intelligente degli iperparametri, la distribuzione dei modelli e i widget per i blocchi appunti.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, importare le librerie Python necessarie.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L'area di lavoro](concept-workspace.md) di Azure Machine Learning è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK è possibile accedere agli [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) elementi dell'area di lavoro creando un oggetto .

Creare un oggetto `config.json` dell'area di lavoro dal file creato nella [sezione Prerequisiti.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Creare un esperimento di deep learning

Creare un esperimento e una cartella per contenere gli script di formazione. In questo esempio, creare un esperimento denominato "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Creare un set di dati di fileCreate a file dataset

L'oggetto `FileDataset` fa riferimento a uno o più file nell'archivio dati dell'area di lavoro o negli URL pubblici. I file possono essere di qualsiasi formato e la classe offre la possibilità di scaricarli o montarli nel contesto di calcolo. Creando un oggetto `FileDataset`, si crea un riferimento alla posizione dell'origine dati. Anche le eventuali trasformazioni applicate al set di dati verranno archiviate nel set di dati. I dati rimangono nell'attuale posizione, quindi non si incorre in costi aggiuntivi di archiviazione. Per altre informazioni, vedere la guida alle [procedure](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) del pacchetto `Dataset`.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Utilizzare `register()` il metodo per registrare il set di dati nell'area di lavoro in modo che possano essere condivisi con altri utenti, riutilizzati in vari esperimenti e a cui viene fatto riferimento per nome nello script di training.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per il processo TensorFlow su cui eseguire il processo. In questo esempio creare un cluster di calcolo di Azure Machine Learning abilitato per GPU.

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

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un obiettivo](concept-compute-target.md) di calcolo.

## <a name="create-a-tensorflow-estimator"></a>Creare uno stimatore TensorFlow

Lo [stimatore TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornisce un modo semplice per avviare un lavoro di training TensorFlow su un obiettivo di calcolo.

Lo stimatore TensorFlow viene implementato [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) tramite la classe generica, che può essere utilizzata per supportare qualsiasi framework. Per altre informazioni sui modelli di training usando lo stimatore generico, vedere Eseguire il training dei [modelli con Azure Machine Learning usando estimator](how-to-train-ml-models.md)

Se lo script di training richiede pacchetti pip o conda aggiuntivi per l'esecuzione, è `pip_packages` possibile `conda_packages` fare in modo che i pacchetti vengano installati nell'immagine Docker risultante passando i relativi nomi tramite gli argomenti e .

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Il supporto per **Tensorflow 2.0** è stato aggiunto alla classe dello stimatore Tensorflow. Vedere il [post di blog](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) per altre informazioni.

Per ulteriori informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per la formazione e](how-to-use-environments.md)la distribuzione . 

## <a name="submit-a-run"></a>Inviare una corsa

[L'oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Durante l'esecuzione dell'esecuzione, l'esecuzione passa attraverso le seguenti fasi:

- **Preparazione**: Viene creata un'immagine Docker in base allo stimatore TensorFlow. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per esecuzioni successive. I log vengono trasmessi anche alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Scalabilità:** il cluster tenta di aumentare le scalabilità se il cluster Batch AI richiede più nodi per eseguire l'esecuzione di quelli attualmente disponibili.

- **In esecuzione:** tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e della cartella ./logs vengono trasmessi alla cronologia di esecuzione e possono essere utilizzati per monitorare l'esecuzione.

- **Post-elaborazione:** la cartella ./outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training del modello, è possibile registrarlo nell'area di lavoro. La registrazione dei modelli consente di archiviare e versione i modelli nell'area di lavoro per semplificare [la gestione e](concept-model-management-and-deployment.md)la distribuzione dei modelli. Specificando i parametri `model_framework` `model_framework_version`, `resource_configuration`e , la distribuzione senza modello di codice diventa disponibile. In questo modo è possibile distribuire direttamente il modello come `ResourceConfiguration` servizio Web dal modello registrato e l'oggetto definisce la risorsa di calcolo per il servizio Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

È inoltre possibile scaricare una copia locale del modello utilizzando l'oggetto Run. Nello script `mnist-tf.py`di training , un oggetto salvafattore TensorFlow rende persistente il modello in una cartella locale (locale alla destinazione di calcolo). È possibile utilizzare l'oggetto Run per scaricare una copia.

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

Lo [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) stimatore supporta anche il training distribuito tra i cluster CPU e GPU. È possibile eseguire facilmente processi TensorFlow distribuiti e Azure Machine Learning gestirà l'orchestrazione per l'utente.

Azure Machine Learning supporta due modalità di training distribuito in TensorFlow:

- Formazione distribuita [basata su MPI](https://www.open-mpi.org/) utilizzando il framework [Horovod](https://github.com/uber/horovod)
- [TensorFlow distribuito](https://www.tensorflow.org/deploy/distributed) nativo utilizzando il metodo del server dei parametri

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) è un quadro open source per la formazione distribuita sviluppato da Uber. Offre un percorso semplice per i processi GPU TensorFlow distribuiti.

Per utilizzare Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) specificare `distributed_training` un oggetto per il parametro nel costruttore TensorFlow. Questo parametro assicura che la libreria Horovod sia installata per l'utilizzo nello script di training.

```Python
from azureml.core.runconfig import MpiConfiguration
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
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Server dei parametri

È anche possibile eseguire [TensorFlow distribuito nativo](https://www.tensorflow.org/deploy/distributed), che usa il modello del server dei parametri. In questo modo si esegue il training in un cluster di server dei parametri e ruoli di lavoro. I ruoli di lavoro calcolano i gradienti durante il training, mentre i server dei parametri aggregano i gradienti.

Per utilizzare il metodo server [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) dei `distributed_training` parametri, specificare un oggetto per il parametro nel costruttore TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definire le specifiche del cluster in 'TF_CONFIG'Define cluster specifications in 'TF_CONFIG'

Sono inoltre necessari gli indirizzi di rete [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)e le porte `TF_CONFIG` del cluster per l'oggetto , pertanto Azure Machine Learning imposta la variabile di ambiente per l'utente.

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

Per l'API di [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) alto livello di TensorFlow, TensorFlow analizza la `TF_CONFIG` variabile e crea la specifica del cluster per l'utente.

Per le API core di livello inferiore di TensorFlow per il training, analizzare la `TF_CONFIG` variabile e compilare l'API `tf.train.ClusterSpec` nel codice di training.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Distribuire un modello TensorFlowDeploy a TensorFlow model

Il modello appena registrato può essere distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo stimatore usato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla creazione di una destinazione di [calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

## <a name="preview-no-code-model-deployment"></a>(Anteprima) Distribuzione del modello senza codice

Anziché la route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per Tensorflow. Registrando il modello come `model_framework`illustrato `model_framework_version`in `resource_configuration` precedenza con i `deploy()` parametri , e , è possibile utilizzare semplicemente la funzione statica per distribuire il modello.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

[L'e-how-to](how-to-deploy-and-where.md) completo copre la distribuzione in Azure Machine Learning in modo più approfondito.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di un modello TensorFlow e sono apprese le opzioni per la distribuzione. Vedere questi altri articoli per altre informazioni su Azure Machine Learning.See these other articles to learn more about Azure Machine Learning.

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Architettura di riferimento per la formazione sul deep learning distribuito in AzureReference architecture for distributed deep learning training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
