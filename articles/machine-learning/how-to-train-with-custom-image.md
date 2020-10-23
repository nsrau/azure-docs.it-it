---
title: Eseguire il training di un modello usando un'immagine Docker personalizzata
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire il training dei modelli con immagini Docker personalizzate in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 6ce0885cce1861b27d6230c3807350831603684b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329118"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Eseguire il training di un modello usando un'immagine Docker personalizzata

Questo articolo illustra come usare un'immagine Docker personalizzata quando si esegue il training dei modelli con Azure Machine Learning. Gli script di esempio in questo articolo consentono di classificare le immagini PET creando una rete neurale di convoluzione. 

Azure Machine Learning fornisce un'immagine di base Docker predefinita. È anche possibile usare Azure Machine Learning ambienti per specificare un'immagine di base diversa, ad esempio una delle [Immagini di base Azure Machine Learning](https://github.com/Azure/AzureML-Containers) gestite o un' [immagine personalizzata](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Le immagini di base personalizzate consentono di gestire in modo accurato le dipendenze e mantenere un controllo più rigoroso sulle versioni dei componenti durante l'esecuzione di processi di training

## <a name="prerequisites"></a>Prerequisiti

Eseguire il codice in uno degli ambienti seguenti:

* Azure Machine Learning istanza di calcolo (nessun download o installazione necessaria):
  * Completare l'esercitazione [configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
  * Nel repository di [esempi](https://github.com/Azure/azureml-examples)Azure Machine Learning trovare un notebook completato passando alla directory **Notebooks**  >  **fastai**  >  **Train-Pets-resnet34. ipynb** . 
* Il proprio server Jupyter Notebook:
  * Creare un [file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
  * Installare [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true). 
  * Creare un [Registro contenitori di Azure](/azure/container-registry) o un altro registro Docker disponibile su Internet.

## <a name="set-up-a-training-experiment"></a>Configurare un esperimento di training

In questa sezione si configura l'esperimento di training inizializzando un'area di lavoro, definendo l'ambiente e configurando una destinazione di calcolo.

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) oggetto.

Creare un `Workspace` oggetto dal config.jssul file creato come [prerequisito](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definire l'ambiente

Creare un `Environment` oggetto e abilitare docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

L'immagine di base specificata nel codice seguente supporta la libreria fast.ai, che consente di distribuire funzionalità di apprendimento avanzato. Per altre informazioni, vedere il [repository dell'hub docker Fast.ai](https://hub.docker.com/u/fastdotai). 

Quando si usa l'immagine Docker personalizzata, è possibile che l'ambiente di Python sia già configurato correttamente. In tal caso, impostare il `user_managed_dependencies` flag su `True` per usare l'ambiente Python incorporato dell'immagine personalizzata. Per impostazione predefinita, Azure Machine Learning compila un ambiente conda con le dipendenze specificate. Il servizio esegue lo script in tale ambiente invece di usare le librerie Python installate nell'immagine di base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Usare un registro contenitori privato (facoltativo)

Per usare un'immagine da un registro contenitori privato che non si trovi nell'area di lavoro, usare `docker.base_image_registry` per specificare l'indirizzo del repository e un nome utente e una password:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Usare un Dockerfile personalizzato (facoltativo)

È anche possibile usare un Dockerfile personalizzato. Usare questo approccio se è necessario installare pacchetti non Python come dipendenze. Ricordarsi di impostare l'immagine di base su `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Per altre informazioni sulla creazione e la gestione di ambienti di Azure Machine Learning, vedere [creare e usare ambienti software](how-to-use-environments.md). 

### <a name="create-or-attach-a-compute-target"></a>Creare o alleghi una destinazione di calcolo

È necessario creare una [destinazione di calcolo](concept-azure-machine-learning-architecture.md#compute-targets) per il training del modello. Questa esercitazione illustra come creare una `AmlCompute` risorsa di calcolo di training.

La creazione di `AmlCompute` richiede alcuni minuti. Se la `AmlCompute` risorsa è già presente nell'area di lavoro, questo codice ignora il processo di creazione.

Come per gli altri servizi di Azure, esistono limiti per determinate risorse (ad esempio, `AmlCompute` ) associate al servizio Azure Machine Learning. Per altre informazioni, vedere [limiti predefiniti e come richiedere una quota superiore](how-to-manage-quotas.md).

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Configurare il processo di training

Per questa esercitazione, usare lo script di training *Train.py* su [GitHub](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py). In pratica, è possibile usare qualsiasi script di training personalizzato ed eseguirlo, così com'è, con Azure Machine Learning.

Creare una `ScriptRunConfig` risorsa per configurare il processo per l'esecuzione nella [destinazione di calcolo](how-to-set-up-training-targets.md)desiderata.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Inviare il processo di training

Quando si invia un'esecuzione di training utilizzando un `ScriptRunConfig` oggetto, il `submit` metodo restituisce un oggetto di tipo `ScriptRun` . L' `ScriptRun` oggetto restituito consente l'accesso a livello di codice alle informazioni relative all'esecuzione del training. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning esegue gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si desidera caricare, utilizzare un [file con estensione ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) o non includerlo nella directory di origine. È invece possibile accedere ai dati usando un [Archivio](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)dati.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato eseguito il training di un modello usando un'immagine Docker personalizzata. Per ulteriori informazioni su Azure Machine Learning, vedere gli altri articoli:
* [Rilevare le metriche di esecuzione](how-to-track-experiments.md) durante il training.
* [Distribuire un modello](how-to-deploy-custom-docker-image.md) usando un'immagine Docker personalizzata.
