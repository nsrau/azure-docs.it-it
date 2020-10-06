---
title: Training di modelli PyTorch per Deep Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di PyTorch su scala aziendale usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 22e834ccc31e2d01646250c973080848173661de
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743778"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Esegui il training dei modelli PyTorch su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training di [PyTorch](https://pytorch.org/) su scala aziendale usando Azure Machine Learning.

Gli script di esempio in questo articolo vengono usati per classificare le immagini di pollo e tacchino per creare una rete neurale per la formazione approfondita (DNN) basata sull' [esercitazione](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)per il trasferimento di PyTorch. 

Sia che si stia eseguendo il training di un modello PyTorch di apprendimento avanzato da zero o si stia portando un modello esistente nel cloud, è possibile usare Azure Machine Learning per scalare i processi di training open source con risorse di calcolo elastiche del cloud. Con Azure Machine Learning è possibile compilare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione. 

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:

- Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

    - Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.
    - Nella cartella Samples Deep learning nel server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > ml-frameworks > pytorch > Train-iperparameter-Tune-deploy-with-pytorch** Folder. 
 
 - Server Jupyter Notebook personale
    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.15.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando la destinazione di calcolo e definendo l'ambiente di training.

### <a name="import-packages"></a>Importare pacchetti

Prima di tutto, importare le librerie Python necessarie.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) oggetto.

Creare un oggetto dell'area di lavoro dal `config.json` file creato nella [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="get-the-data"></a>Ottenere i dati

Il set di dati è costituito da circa 120 di immagini di training per tacchini e polling, con immagini di convalida 100 per ogni classe. Il set di dati viene scaricato ed estratto come parte dello script di training `pytorch_train.py` . Le immagini sono un subset del [set di dati V5 Open images V5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Preparare lo script di training

In questa esercitazione, lo script di training `pytorch_train.py` è già disponibile. In pratica, è possibile usare qualsiasi script di training personalizzato, così come è, ed eseguirlo con Azure Machine Learning.

Creare una cartella per gli script di training.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per l'esecuzione del processo PyTorch. In questo esempio, creare un cluster di calcolo Azure Machine Learning abilitato per GPU.

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
Azure ML offre ambienti predefiniti e curati se non si vuole definire un ambiente personalizzato. Azure ML dispone di diversi ambienti di CPU e GPU curati per PyTorch corrispondenti a versioni diverse di PyTorch. Per altre informazioni, vedere [qui](resource-curated-environments.md).

Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Per visualizzare i pacchetti inclusi nell'ambiente curato, è possibile scrivere le dipendenze conda sul disco:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Assicurarsi che l'ambiente curato includa tutte le dipendenze richieste dallo script di training. In caso contrario, sarà necessario modificare l'ambiente in modo da includere le dipendenze mancanti. Si noti che se l'ambiente viene modificato, sarà necessario assegnargli un nuovo nome, perché il prefisso ' AzureML ' è riservato per gli ambienti curati. Se il file YAML delle dipendenze conda è stato modificato, è possibile creare un nuovo ambiente con un nuovo nome, ad esempio:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Se invece l'oggetto ambiente curato è stato modificato direttamente, è possibile clonare tale ambiente con un nuovo nome:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Creare un ambiente Azure ML da questa specifica dell'ambiente conda. L'ambiente verrà incluso in un contenitore Docker in fase di esecuzione.

Per impostazione predefinita, se non viene specificata alcuna immagine di base, Azure ML userà un'immagine della CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` come immagine di base. Poiché in questo esempio viene eseguito il training in un cluster GPU, è necessario specificare un'immagine di base GPU con le dipendenze e i driver GPU necessari. Azure ML mantiene un set di immagini di base pubblicate in Microsoft Container Registry (AzureML) che è possibile usare. per ulteriori informazioni, vedere il repository GitHub [Azure/-container](https://github.com/Azure/AzureML-Containers) .

```python
from azureml.core import Environment

pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Facoltativamente, è possibile acquisire solo tutte le dipendenze direttamente in un'immagine Docker personalizzata o Dockerfile e creare il proprio ambiente. Per altre informazioni, vedere eseguire il [training con un'immagine personalizzata](how-to-train-with-custom-image.md).

Per altre informazioni sulla creazione e sull'uso degli ambienti, vedere [creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare un ScriptRunConfig

Creare un oggetto [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) per specificare i dettagli di configurazione del processo di training, tra cui lo script di training, l'ambiente da usare e la destinazione di calcolo in cui eseguire. Eventuali argomenti dello script di training verranno passati tramite la riga di comando se specificati nel `arguments` parametro. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> Azure Machine Learning esegue gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si desidera caricare, utilizzare un [file con estensione ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) o non includerlo nella directory di origine. È invece possibile accedere ai dati usando un [set](how-to-train-with-datasets.md)di dati di Azure ml.

Per altre informazioni sulla configurazione dei processi con ScriptRunConfig, vedere [configurare e inviare le esecuzioni di training](how-to-set-up-training-targets.md).

> [!WARNING]
> Se in precedenza si usa PyTorch Estimator per configurare i processi di training di PyTorch, si noti che gli estimatori saranno deprecati in una versione futura di Azure ML SDK. Con Azure ML SDK >= 1.15.0, ScriptRunConfig è il metodo consigliato per configurare i processi di training, inclusi quelli che usano i Framework DL.

## <a name="submit-your-run"></a>Invia la tua esecuzione

L' [oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = Experiment(ws, name='pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**: viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece si specifica un ambiente curato, verrà utilizzata l'immagine memorizzata nella cache che supporta l'ambiente curato.

- **Ridimensionamento**: il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running**: tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e `script` viene eseguito. Gli output da stdout e la cartella **./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: la cartella **./Outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-or-download-a-model"></a>Registrare o scaricare un modello

Dopo aver eseguito il training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

È inoltre possibile scaricare una copia locale del modello utilizzando l'oggetto Esegui. Nello script di training `pytorch_train.py` , un oggetto PyTorch Save Salva in modo permanente il modello in una cartella locale (locale nella destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Training distribuito

Azure Machine Learning supporta anche processi PyTorch distribuiti a più nodi, in modo che sia possibile ridimensionare i carichi di lavoro di training. È possibile eseguire facilmente processi PyTorch distribuiti e Azure ML gestirà l'orchestrazione.

Azure ML supporta l'esecuzione di processi PyTorch distribuiti con il modulo DistributedDataParallel incorporato Horovod e PyTorch.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) è un framework open source di riduzione per la formazione distribuita sviluppata da uber. Offre un percorso facile per la scrittura di codice PyTorch distribuito per il training.

Il codice di training dovrà essere instrumentato con Horovod per il training distribuito. Per ulteriori informazioni sull'utilizzo di Horovod con PyTorch, vedere la [documentazione di Horovod](https://horovod.readthedocs.io/en/stable/pytorch.html).

Assicurarsi inoltre che l'ambiente di training includa il pacchetto **horovod** . Se si usa un ambiente curato PyTorch, horovod è già incluso come una delle dipendenze. Se si usa un ambiente personalizzato, verificare che sia inclusa la dipendenza horovod, ad esempio:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Per eseguire un processo distribuito con MPI/Horovod in Azure ML, è necessario specificare un [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) per il `distributed_job_config` parametro del costruttore ScriptRunConfig. Il codice seguente consente di configurare un processo distribuito a 2 nodi che esegue un processo per nodo. Se si desidera eseguire più processi per nodo, ad esempio se lo SKU del cluster ha più GPU, specificare anche il `process_count_per_node` parametro in MpiConfiguration (il valore predefinito è `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Per un'esercitazione completa sull'esecuzione di PyTorch distribuiti con Horovod in Azure ML, vedere [Distributed PyTorch with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistributedDataParallel
Se si usa il modulo [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) incorporato di PyTorch compilato con il pacchetto **torcia. Distributed** nel codice di training, è anche possibile avviare il processo distribuito tramite Azure ml.

Per eseguire un processo PyTorch distribuito con DistributedDataParallel, specificare un [PyTorchConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?view=azure-ml-py&preserve-view=true) per il `distributed_job_config` parametro del costruttore ScriptRunConfig. Per usare il back-end NCCL per Torch. Distributed, specificare `communication_backend='Nccl'` in PyTorchConfiguration. Il codice seguente consente di configurare un processo distribuito a 2 nodi. Il back-end NCCL è il back-end consigliato per la formazione GPU distribuita PyTorch.

Per i processi PyTorch distribuiti configurati tramite PyTorchConfiguration, Azure ML imposta le variabili di ambiente seguenti nei nodi della destinazione di calcolo:

* `AZ_BATCHAI_PYTORCH_INIT_METHOD`: URL per l'inizializzazione file system condivisa del gruppo di processi
* `AZ_BATCHAI_TASK_INDEX`: rango globale del processo di lavoro

È possibile specificare queste variabili di ambiente per gli argomenti corrispondenti dello script di training tramite il `arguments` parametro di ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

args = ['--dist-backend', 'nccl',
        '--dist-url', '$AZ_BATCHAI_PYTORCH_INIT_METHOD',
        '--rank', '$AZ_BATCHAI_TASK_INDEX',
        '--world-size', 2]

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=PyTorchConfiguration(communication_backend='Nccl', node_count=2))
```

Se invece si preferisce usare il back-end Gloo per la formazione distribuita, specificare `communication_backend='Gloo'` . Il back-end Gloo è consigliato per il training della CPU distribuito.

Per un'esercitazione completa sull'esecuzione di PyTorch distribuiti in Azure ML, vedere [Distributed PyTorch with DistributedDataParallel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-nccl-gloo).

## <a name="export-to-onnx"></a>Eseguire l'esportazione in ONNX

Per ottimizzare l'inferenza con il [Runtime ONNX](concept-onnx.md), convertire il modello PyTorch sottoposto a training nel formato ONNX. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione. Per un esempio, vedere l' [esercitazione](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di una rete neurale di apprendimento avanzato con PyTorch in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
