---
title: Inviare un'esecuzione di training a una destinazione di calcolo
titleSuffix: Azure Machine Learning
description: Eseguire il training del modello di apprendimento automatico in diversi ambienti di training (destinazioni di calcolo). Passare da un ambiente di training a un altro è facile. Iniziare a eseguire il training in locale. Se è necessario aumentare le risorse, passare a una destinazione di calcolo basata sul cloud.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: da48b593b8f645566b2f9775fabc5d8e62e625b6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661575"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Inviare un'esecuzione di training a una destinazione di calcolo

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come usare diversi ambienti di training ([destinazioni di calcolo](concept-compute-target.md)) per eseguire il training del modello di machine learning.

Solitamente il training si avvia nel computer locale e in un secondo momento si esegue tale script di training in una destinazione di calcolo diversa. Con Azure Machine Learning, è possibile eseguire lo script su varie destinazioni di calcolo senza dover modificare lo script di training.

È sufficiente definire l'ambiente per ogni destinazione di calcolo all'interno di una configurazione di **esecuzione dello script**.  Quindi, quando si vuole eseguire l'esperimento di training in una destinazione di calcolo diversa, specificare la configurazione di esecuzione per tale ambiente di calcolo.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree)
* [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* Un' [area di lavoro Azure Machine Learning](how-to-manage-workspace.md), `ws`
* Una destinazione di calcolo, `my_compute_target` .  Creare una destinazione di calcolo con:
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Che cos'è la configurazione di esecuzione dello script?

L'esperimento di training viene inviato con un oggetto [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) .  Questo oggetto include:

* **source_directory**: la directory di origine che contiene lo script di training
* **script**: identifica lo script di training
* **run_config**: la [configurazione di esecuzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true), che a sua volta definisce il punto in cui si verificherà il training. Nella `run_config` specificare la destinazione di calcolo e l'ambiente da usare quando si esegue lo script di training.  

## <a name="whats-an-environment"></a>Che cos'è un ambiente?

Azure Machine Learning [ambienti](concept-environments.md) sono incapsulati nell'ambiente in cui si verifica il training di machine learning. Specificano i pacchetti Python, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi. Specificano anche i tempi di esecuzione (Python, Spark o Docker).  

Gli ambienti vengono specificati nell'  `run_config` oggetto all'interno di un oggetto `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Eseguire il training del modello

Il criterio di codice per inviare l'esecuzione di un training è uguale per tutti i tipi di destinazioni di calcolo:

1. Creare un esperimento da eseguire
1. Creazione di un ambiente in cui viene eseguito lo script
1. Creare una configurazione di esecuzione dello script, che fa riferimento alla destinazione di calcolo e all'ambiente
1. Inviare l'esecuzione
1. Attendere il completamento dell'esecuzione

In alternativa, è possibile:

* Inviare l'esperimento con un oggetto `Estimator` come illustrato in [Eseguire il training di modelli di Machine Learning con oggetti Estimator](how-to-train-ml-models.md).
* Inviare un'esecuzione di HyperDrive per l'[ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md).
* Inviare un esperimento tramite l'[estensione di VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento nell'area di lavoro.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Creare un ambiente

Gli ambienti curati contengono raccolte di pacchetti Python e sono disponibili nell'area di lavoro per impostazione predefinita. Questi ambienti sono supportati da immagini Docker memorizzate nella cache che riduce il costo di preparazione dell'esecuzione. Per una destinazione di calcolo remota, è possibile usare uno di questi ambienti con la cura più diffusi per iniziare:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Per altre informazioni e dettagli sugli ambienti, vedere [creare & usare gli ambienti software in Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Destinazione di calcolo locale

Se la destinazione di calcolo è il **computer locale**, l'utente è responsabile di garantire che tutti i pacchetti necessari siano disponibili nell'ambiente Python in cui viene eseguito lo script.  Usare `python.user_managed_dependencies` per usare l'ambiente Python corrente o il Python nel percorso specificato.

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Crea configurazione esecuzione script

Ora che si dispone di una destinazione di calcolo ( `compute_target` ) e di un ambiente ( `my_environment` ), creare una configurazione di esecuzione dello script che esegua lo script di training ( `train.py` ) che si trova nella `project_folder` Directory:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Potrebbe anche essere necessario impostare il Framework per l'esecuzione.

* Per un cluster HDI:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Per una macchina virtuale remota:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Inviare l'esperimento

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> Quando si invia l'esecuzione di training, viene creato uno snapshot della directory contenente gli script di training che viene quindi inviato alla destinazione di calcolo. Lo snapshot viene inoltre archiviato come parte dell'esperimento nell'area di lavoro. Se si modificano i file e si invia di nuovo l'esecuzione, verranno caricati solo i file modificati.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Per ulteriori informazioni sugli snapshot, vedere [snapshot](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Rilevamento e integrazione di Git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Per altre informazioni, vedere [Integrazione di Git con Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Esempi di notebook

Consultare questi notebook per esempi di training con varie destinazioni di calcolo:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per il training del modello.
* Informazioni su come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori. View = Azure-ml-py&Preserve-View = true)
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* Consultare le informazioni sull'SDK di [classe RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true).
* [Usare Azure Machine Learning con le reti virtuali di Azure](how-to-enable-virtual-network.md)