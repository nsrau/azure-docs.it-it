---
title: Eseguire il training di un modello usando un'immagine Docker personalizzata
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training dei modelli con immagini Docker personalizzate in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 13a88b327b5ba56b52cd4f08d9c7fae5d653ed38
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92095943"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Eseguire il training di un modello usando un'immagine Docker personalizzata

Questo articolo illustra come usare un'immagine Docker personalizzata per il training dei modelli con Azure Machine Learning. 

Gli script di esempio in questo articolo vengono usati per classificare le immagini PET creando una rete neurale di convoluzione. 

Mentre Azure Machine Learning fornisce un'immagine di base Docker predefinita, è anche possibile usare Azure Machine Learning ambienti per specificare un'immagine di base specifica, ad esempio uno dei set di [Immagini di base di Azure ml](https://github.com/Azure/AzureML-Containers) gestite o un' [immagine personalizzata](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Le immagini di base personalizzate consentono di gestire in modo accurato le dipendenze e di mantenere un controllo più rigoroso sulle versioni dei componenti durante l'esecuzione di processi di training. 

## <a name="prerequisites"></a>Prerequisiti 
Eseguire questo codice in uno degli ambienti seguenti:
* Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria
    * Completare l' [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    * Nel repository di [esempi](https://github.com/Azure/azureml-examples)Azure Machine Learning trovare un notebook completato passando a questa directory: **Notebooks > fastai > Train-Pets-resnet34. ipynb** 

* Server Jupyter Notebook personale
    * Creare un [file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    * [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
    * Un [container Registry di Azure](/azure/container-registry) o un altro registro Docker accessibile su Internet.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento 
Questa sezione Configura l'esperimento di training inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro
L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) oggetto.

Creare un oggetto dell'area di lavoro dal `config.json` file creato nella [sezione Prerequisiti](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Preparare gli script
Per questa esercitazione, lo script di training **Train.py** è disponibile [qui](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). In pratica, è possibile usare qualsiasi script di training personalizzato, così come è, ed eseguirlo con Azure Machine Learning.

### <a name="define-your-environment"></a>Definire l'ambiente
Creare un oggetto ambiente e abilitare docker. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

L'immagine di base specificata di seguito supporta la libreria fast.ai che consente di distribuire funzionalità di apprendimento avanzato. Per ulteriori informazioni, vedere [Fast.ai DockerHub](https://hub.docker.com/u/fastdotai). 

Quando si usa l'immagine Docker personalizzata, è possibile che l'ambiente Python sia già configurato correttamente. In tal caso, impostare il `user_managed_dependencies` flag su true per sfruttare l'ambiente Python incorporato dell'immagine personalizzata. Per impostazione predefinita, Azure ML compilerà un ambiente conda con le dipendenze specificate e eseguirà l'esecuzione in tale ambiente invece di usare le librerie Python installate nell'immagine di base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Per usare un'immagine da un registro contenitori privato che non si trova nell'area di lavoro, è necessario usare `docker.base_image_registry` per specificare l'indirizzo del repository e un nome utente e una password:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

È anche possibile usare un Dockerfile personalizzato. Usare questo approccio se è necessario installare pacchetti non Python come dipendenze e si ricorda di impostare l'immagine di base su None.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Per altre informazioni sulla creazione e la gestione di ambienti Azure ML, vedere [creare & usare gli ambienti software](how-to-use-environments.md). 

### <a name="create-or-attach-existing-amlcompute"></a>Crea o Connetti AmlCompute esistenti
Sarà necessario creare una destinazione di [calcolo](concept-azure-machine-learning-architecture.md#compute-targets) per il training del modello. In questa esercitazione si creerà AmlCompute come risorsa di calcolo di training.

La creazione di AmlCompute richiede circa 5 minuti. Se il AmlCompute con lo stesso nome è già presente nell'area di lavoro, il codice ignorerà il processo di creazione.

Come per gli altri servizi di Azure, esistono limiti a determinate risorse (ad esempio AmlCompute) associate al servizio Azure Machine Learning. Leggere [questo articolo](how-to-manage-quotas.md) sui limiti predefiniti e su come richiedere più quote. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Creare un ScriptRunConfig
Questo ScriptRunConfig consente di configurare il processo per l'esecuzione nella [destinazione di calcolo](how-to-set-up-training-targets.md)desiderata.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>Invia la tua esecuzione
Quando viene inviata un'esecuzione di training usando un oggetto ScriptRunConfig, il metodo Submit restituisce un oggetto di tipo ScriptRun. L'oggetto ScriptRun restituito consente l'accesso a livello di codice alle informazioni relative all'esecuzione del training. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning esegue gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si desidera caricare, utilizzare un [file con estensione ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) o non includerlo nella directory di origine. È invece possibile accedere ai dati usando un [Archivio](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)dati.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato eseguito il training di un modello usando un'immagine Docker personalizzata. Per ulteriori informazioni su Azure Machine Learning, vedere questi altri articoli.
* [Rilevare le metriche di esecuzione](how-to-track-experiments.md) durante il training
* [Distribuire un modello](how-to-deploy-custom-docker-image.md) usando un'immagine Docker personalizzata.
