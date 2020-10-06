---
title: Eseguire il training e distribuire un modello TensorFlow
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di TensorFlow su larga scala usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 21a0672db5a7038fbcdeb01e4cf07bcd760cf7ef
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742996"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Esegui il training dei modelli TensorFlow su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training di [TensorFlow](https://www.tensorflow.org/overview) su larga scala usando Azure Machine Learning.

Questo esempio esegue il training e la registrazione di un modello TensorFlow per classificare le cifre scritte a mano usando una rete neurale profonda (DNN).

Indipendentemente dal fatto che si stia sviluppando un modello TensorFlow da zero o si stia introducendo un [modello esistente](how-to-deploy-existing-model.md) nel cloud, è possibile usare Azure Machine Learning per scalare in orizzontale processi di training open source per compilare, distribuire, applicare la versione e monitorare i modelli a livello di produzione.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:

 - Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

     - Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.
    - Nella cartella Samples Deep learning nel server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > ml-frameworks > tensorflow > Train-iperparameter-Tune-deploy-with-tensorflow** Folder. 
 
 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.15.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` script di esempio e `utils.py`
     
    È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando la destinazione di calcolo e definendo l'ambiente di training.

### <a name="import-packages"></a>Importare pacchetti

Prima di tutto, importare le librerie Python necessarie.

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

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) oggetto.

Creare un oggetto dell'area di lavoro dal `config.json` file creato nella [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Creare un set di dati di file

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

Usare il `register()` metodo per registrare il set di dati nell'area di lavoro in modo che possano essere condivisi con altri utenti, riutilizzati in diversi esperimenti e a cui viene fatto riferimento in base al nome nello script di training.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per l'esecuzione del processo TensorFlow. In questo esempio, creare un cluster di calcolo Azure Machine Learning abilitato per GPU.

```Python
cluster_name = "gpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un calcolo di destinazione](concept-compute-target.md) .

### <a name="define-your-environment"></a>Definire l'ambiente

Per definire l' [ambiente](concept-environments.md) Azure ml che incapsula le dipendenze dello script di training, è possibile definire un ambiente personalizzato o usare un ambiente di Azure ml curato.

#### <a name="use-a-curated-environment"></a>Usare un ambiente curato
Azure ML offre ambienti predefiniti e curati se non si vuole definire un ambiente personalizzato. Azure ML dispone di diversi ambienti di CPU e GPU curati per TensorFlow corrispondenti a versioni diverse di TensorFlow. Per altre informazioni, vedere [qui](resource-curated-environments.md).

Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Per visualizzare i pacchetti inclusi nell'ambiente curato, è possibile scrivere le dipendenze conda sul disco:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Assicurarsi che l'ambiente curato includa tutte le dipendenze richieste dallo script di training. In caso contrario, sarà necessario modificare l'ambiente in modo da includere le dipendenze mancanti. Si noti che se l'ambiente viene modificato, sarà necessario assegnargli un nuovo nome, perché il prefisso ' AzureML ' è riservato per gli ambienti curati. Se il file YAML delle dipendenze conda è stato modificato, è possibile creare un nuovo ambiente con un nuovo nome, ad esempio:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Se invece l'oggetto ambiente curato è stato modificato direttamente, è possibile clonare tale ambiente con un nuovo nome:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Creare un ambiente personalizzato

È anche possibile creare un ambiente Azure ML personalizzato che incapsula le dipendenze dello script di training.

Definire innanzitutto le dipendenze conda in un file YAML; in questo esempio il file è denominato `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Creare un ambiente Azure ML da questa specifica dell'ambiente conda. L'ambiente verrà incluso in un contenitore Docker in fase di esecuzione.

Per impostazione predefinita, se non viene specificata alcuna immagine di base, Azure ML userà un'immagine della CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` come immagine di base. Poiché in questo esempio viene eseguito il training in un cluster GPU, è necessario specificare un'immagine di base GPU con le dipendenze e i driver GPU necessari. Azure ML mantiene un set di immagini di base pubblicate in Microsoft Container Registry (AzureML) che è possibile usare. per ulteriori informazioni, vedere il repository GitHub [Azure/-container](https://github.com/Azure/AzureML-Containers) .

```python
from azureml.core import Environment

tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Facoltativamente, è possibile acquisire solo tutte le dipendenze direttamente in un'immagine Docker personalizzata o Dockerfile e creare il proprio ambiente. Per altre informazioni, vedere eseguire il [training con un'immagine personalizzata](how-to-train-with-custom-image.md).

Per altre informazioni sulla creazione e sull'uso degli ambienti, vedere [creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare un ScriptRunConfig

Creare un oggetto [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) per specificare i dettagli di configurazione del processo di training, tra cui lo script di training, l'ambiente da usare e la destinazione di calcolo in cui eseguire. Eventuali argomenti dello script di training verranno passati tramite la riga di comando se specificati nel `arguments` parametro.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning esegue gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si desidera caricare, utilizzare un [file con estensione ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) o non includerlo nella directory di origine. È invece possibile accedere ai dati usando un [set](how-to-train-with-datasets.md)di dati di Azure ml.

Per altre informazioni sulla configurazione dei processi con ScriptRunConfig, vedere [configurare e inviare le esecuzioni di training](how-to-set-up-training-targets.md).

> [!WARNING]
> Se in precedenza si usa TensorFlow Estimator per configurare i processi di training di TensorFlow, si noti che gli estimatori saranno deprecati in una versione futura di Azure ML SDK. Con Azure ML SDK >= 1.15.0, ScriptRunConfig è il metodo consigliato per configurare i processi di training, inclusi quelli che usano i Framework DL.

### <a name="submit-a-run"></a>Inviare un'esecuzione

L' [oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**: viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece si specifica un ambiente curato, verrà utilizzata l'immagine memorizzata nella cache che supporta l'ambiente curato.

- **Ridimensionamento**: il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running**: tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e `script` viene eseguito. Gli output da stdout e la cartella **./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: la cartella **./Outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md). Facoltativo: se si specificano i parametri `model_framework` , `model_framework_version` e `resource_configuration` , la distribuzione del modello senza codice diventa disponibile. In questo modo è possibile distribuire direttamente il modello come servizio Web dal modello registrato e l' `ResourceConfiguration` oggetto definisce la risorsa di calcolo per il servizio Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

È inoltre possibile scaricare una copia locale del modello utilizzando l'oggetto Esegui. Nello script di training `tf_mnist.py` , un oggetto TensorFlow Saver Salva in modo permanente il modello in una cartella locale (locale nella destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Training distribuito

Azure Machine Learning supporta anche processi TensorFlow distribuiti a più nodi, in modo che sia possibile ridimensionare i carichi di lavoro di training. È possibile eseguire facilmente processi TensorFlow distribuiti e Azure ML gestirà l'orchestrazione.

Azure ML supporta l'esecuzione di processi TensorFlow distribuiti con l'API di training distribuito predefinita Horovod e TensorFlow.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) è un framework open source di riduzione per la formazione distribuita sviluppata da uber. Offre un percorso facile per la scrittura di codice TensorFlow distribuito per il training.

Il codice di training dovrà essere instrumentato con Horovod per il training distribuito. Per ulteriori informazioni sull'utilizzo di Horovod con TensorFlow, vedere la documentazione di Horovod:

Per ulteriori informazioni sull'utilizzo di Horovod con TensorFlow, vedere la documentazione di Horovod:

* [Horovod con TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod con l'API keras di TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Assicurarsi inoltre che l'ambiente di training includa il pacchetto **horovod** . Se si usa un ambiente curato TensorFlow, horovod è già incluso come una delle dipendenze. Se si usa un ambiente personalizzato, verificare che sia inclusa la dipendenza horovod, ad esempio:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Per eseguire un processo distribuito con MPI/Horovod in Azure ML, è necessario specificare un [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) per il `distributed_job_config` parametro del costruttore ScriptRunConfig. Il codice seguente consente di configurare un processo distribuito a 2 nodi che esegue un processo per nodo. Se si desidera eseguire più processi per nodo, ad esempio se lo SKU del cluster ha più GPU, specificare anche il `process_count_per_node` parametro in MpiConfiguration (il valore predefinito è `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Per un'esercitazione completa sull'esecuzione di TensorFlow distribuiti con Horovod in Azure ML, vedere [Distributed TensorFlow with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>TF. Distribuisci

Se si usa [TensorFlow distribuiti nativi](https://www.tensorflow.org/guide/distributed_training) nel codice di training, ad esempio l'API TensorFlow 2. x `tf.distribute.Strategy` , è anche possibile avviare il processo distribuito tramite Azure ml. 

A tale scopo, specificare un [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) per il `distributed_job_config` parametro del costruttore ScriptRunConfig. Se si usa `tf.distribute.experimental.MultiWorkerMirroredStrategy` , specificare `worker_count` in TensorflowConfiguration corrispondente al numero di nodi per il processo di training.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

In TensorFlow, la `TF_CONFIG` variabile di ambiente è obbligatoria per il training su più computer. Azure ML consente di configurare e impostare la `TF_CONFIG` variabile in modo appropriato per ogni thread di lavoro prima di eseguire lo script di training. È possibile accedere `TF_CONFIG` dallo script di training se è necessario tramite `os.environ['TF_CONFIG']` .

Struttura di esempio di `TF_CONFIG` set in un nodo del ruolo di lavoro principale:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Se lo script di training usa la strategia del server dei parametri per il training distribuito, ad esempio per Legacy TensorFlow 1. x, sarà necessario specificare anche il numero di server dei parametri da usare nel processo, ad `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` esempio.

## <a name="deploy-a-tensorflow-model"></a>Distribuire un modello TensorFlow

La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

### <a name="preview-no-code-model-deployment"></a>Anteprima Distribuzione del modello senza codice

Anziché la route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per TensorFlow. Registrando il modello come illustrato in precedenza con i `model_framework` `model_framework_version` parametri, e `resource_configuration` , è possibile usare semplicemente la `deploy()` funzione statica per distribuire il modello.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Il metodo [completo illustra la distribuzione](how-to-deploy-and-where.md) in Azure Machine Learning più approfondita.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di un modello TensorFlow e sono state apprese le opzioni per la distribuzione. Per ulteriori informazioni su Azure Machine Learning, vedere questi altri articoli.

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
