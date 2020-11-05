---
title: Training di modelli keras per Deep Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training e registrare un modello di classificazione di rete neurale keras in esecuzione in TensorFlow usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a7d55c6e550000d2dd6c2930d95086ec433c246b
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361098"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Esegui il training dei modelli keras su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training di keras con Azure Machine Learning.

Il codice di esempio riportato in questo articolo illustra come eseguire il training e la registrazione di un modello di classificazione keras compilato usando il back-end TensorFlow con Azure Machine Learning. Usa il set di [dati MNIST](http://yann.lecun.com/exdb/mnist/) più diffusi per classificare le cifre scritte a mano usando una rete neurale profonda (DNN) creata usando la [libreria keras Python](https://keras.io) in esecuzione in [TensorFlow](https://www.tensorflow.org/overview).

Keras è un'API di rete neurale di alto livello in grado di eseguire la maggior parte degli altri Framework DNN più diffusi per semplificare lo sviluppo. Con Azure Machine Learning è possibile scalare rapidamente i processi di training usando le risorse di calcolo del cloud elastico. È anche possibile tenere traccia delle esecuzioni di training, dei modelli di versione, della distribuzione dei modelli e molto altro ancora.

Indipendentemente dal fatto che si stia sviluppando un modello keras da zero o si stia introducendo un modello esistente nel cloud, Azure Machine Learning possibile creare modelli pronti per la produzione.

> [!NOTE]
> Se si usa l'API keras **tf. keras** incorporata in TensorFlow e non il pacchetto keras autonomo, vedere invece di eseguire il [training dei modelli TensorFlow](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:

- Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

     - Completare l'[Esercitazione: Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server di notebook dedicato con l'SDK e il repository di esempi precaricati.
    - Nella cartella Samples nel server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > ml-frameworks > keras > Train-iperparameter-Tune-deploy-with-keras** Folder.

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - [Scaricare i file](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` script di esempio e `utils.py`

    È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include sezioni espanse che coprono l'ottimizzazione intelligente dei parametri, la distribuzione di modelli e i widget del notebook.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando il filedataset per i dati di training di input, creando la destinazione di calcolo e definendo l'ambiente di training.

### <a name="import-packages"></a>Importare pacchetti

Prima di tutto, importare le librerie Python necessarie.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) oggetto.

Creare un oggetto dell'area di lavoro dal `config.json` file creato nella [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Creare un set di dati di file

L'oggetto `FileDataset` fa riferimento a uno o più file nell'archivio dati dell'area di lavoro o negli URL pubblici. I file possono essere di qualsiasi formato e la classe offre la possibilità di scaricarli o montarli nel contesto di calcolo. Creando un oggetto `FileDataset`, si crea un riferimento alla posizione dell'origine dati. Anche le eventuali trasformazioni applicate al set di dati verranno archiviate nel set di dati. I dati rimangono nell'attuale posizione, quindi non si incorre in costi aggiuntivi di archiviazione. Per altre informazioni, vedere la guida alle [procedure](./how-to-create-register-datasets.md) del pacchetto `Dataset`.

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

È possibile usare il `register()` metodo per registrare il set di dati nell'area di lavoro in modo che possano essere condivisi con altri utenti, riutilizzati in diversi esperimenti e a cui viene fatto riferimento in base al nome nello script di training.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Creare una destinazione di calcolo

Creare una destinazione di calcolo per l'esecuzione del processo di training. In questo esempio, creare un cluster di calcolo Azure Machine Learning abilitato per GPU.

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

Definire l' [ambiente](concept-environments.md) Azure ml che incapsula le dipendenze dello script di training.

Definire innanzitutto le dipendenze conda in un file YAML; in questo esempio il file è denominato `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Creare un ambiente Azure ML da questa specifica dell'ambiente conda. L'ambiente verrà incluso in un contenitore Docker in fase di esecuzione.

Per impostazione predefinita, se non viene specificata alcuna immagine di base, Azure ML userà un'immagine della CPU `azureml.core.environment.DEFAULT_CPU_IMAGE` come immagine di base. Poiché in questo esempio viene eseguito il training in un cluster GPU, è necessario specificare un'immagine di base GPU con le dipendenze e i driver GPU necessari. Azure ML mantiene un set di immagini di base pubblicate in Microsoft Container Registry (AzureML) che è possibile usare. per ulteriori informazioni, vedere il repository GitHub [Azure/-container](https://github.com/Azure/AzureML-Containers) .

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Per altre informazioni sulla creazione e sull'uso degli ambienti, vedere [creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare un ScriptRunConfig
Per prima cosa, ottenere i dati dall'archivio dati dell'area di lavoro usando la `Dataset` classe.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Creare un oggetto ScriptRunConfig per specificare i dettagli di configurazione del processo di training, tra cui script di training, ambiente da usare e destinazione di calcolo in cui eseguirlo.

Eventuali argomenti dello script di training verranno passati tramite la riga di comando se specificati nel `arguments` parametro. Il valore di DatasetConsumptionConfig per il filedataset viene passato come argomento allo script di training, per l' `--data-folder` argomento. Azure ML risolverà questa DatasetConsumptionConfig nel punto di montaggio dell'archivio dati di backup, a cui è possibile accedere dallo script di training.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Per altre informazioni sulla configurazione dei processi con ScriptRunConfig, vedere [configurare e inviare le esecuzioni di training](how-to-set-up-training-targets.md).

> [!WARNING]
> Se in precedenza si usa TensorFlow Estimator per configurare i processi di training di keras, si noti che gli estimatori saranno deprecati in una versione futura di Azure ML SDK. Con Azure ML SDK >= 1.15.0, ScriptRunConfig è il metodo consigliato per configurare i processi di training, inclusi quelli che usano i Framework DL.

### <a name="submit-your-run"></a>Invia la tua esecuzione

L' [oggetto Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = Experiment(workspace=ws, name='keras-mnist').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione** : viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece si specifica un ambiente curato, verrà utilizzata l'immagine memorizzata nella cache che supporta l'ambiente curato.

- **Ridimensionamento** : il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running** : tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e `script` viene eseguito. Gli output da stdout e la cartella **./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione** : la cartella **./Outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="register-the-model"></a>Registrare il modello

Dopo aver eseguito il training del modello, è possibile registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

È anche possibile scaricare una copia locale del modello. Questa operazione può essere utile per eseguire altre operazioni di convalida del modello localmente. Nello script di training, `keras_mnist.py` un oggetto TensorFlow Saver Salva in modo permanente il modello in una cartella locale (locale nella destinazione di calcolo). È possibile usare l'oggetto Run per scaricare una copia dalla cronologia di esecuzione.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di un modello keras in Azure Machine Learning. Per informazioni su come distribuire un modello, continuare con l'articolo sulla distribuzione del modello.

* [Come e dove distribuire i modelli](how-to-deploy-and-where.md)
* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
