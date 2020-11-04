---
title: Configurare un'esecuzione di training
titleSuffix: Azure Machine Learning
description: Eseguire il training del modello di apprendimento automatico in diversi ambienti di training (destinazioni di calcolo). Passare da un ambiente di training a un altro è facile. Iniziare a eseguire il training in locale. Se è necessario aumentare le risorse, passare a una destinazione di calcolo basata sul cloud.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 8280af20d63da969504cda8ffe875405d4bf0218
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324719"
---
# <a name="configure-and-submit-training-runs"></a>Configurare e inviare le esecuzioni di training

Questo articolo illustra come configurare e inviare Azure Machine Learning esecuzioni per eseguire il training dei modelli.

Quando si esegue il training, è normale avviarsi nel computer locale e successivamente aumentare il livello di scalabilità orizzontale in un cluster basato sul cloud. Con Azure Machine Learning, è possibile eseguire lo script su varie destinazioni di calcolo senza dover modificare lo script di training.

È sufficiente definire l'ambiente per ogni destinazione di calcolo all'interno di una configurazione di **esecuzione dello script**.  Quindi, quando si vuole eseguire l'esperimento di training in una destinazione di calcolo diversa, specificare la configurazione di esecuzione per tale ambiente di calcolo.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree)
* [SDK Azure Machine Learning per Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* Un' [area di lavoro Azure Machine Learning](how-to-manage-workspace.md), `ws`
* Una destinazione di calcolo, `my_compute_target` .  [Creare una destinazione di calcolo](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Che cos'è la configurazione di esecuzione dello script?
Un [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) viene usato per configurare le informazioni necessarie per l'invio di un training eseguito come parte di un esperimento.

L'esperimento di training viene inviato con un oggetto ScriptRunConfig.  Questo oggetto include:

* **source_directory** : la directory di origine che contiene lo script di training
* **script** : script di training da eseguire
* **compute_target** : la destinazione di calcolo da eseguire in
* **ambiente** : ambiente da usare quando si esegue lo script
* e alcune opzioni configurabili aggiuntive (vedere la [documentazione di riferimento](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) per ulteriori informazioni)

## <a name="train-your-model"></a><a id="submit"></a>Eseguire il training del modello

Il criterio di codice per inviare l'esecuzione di un training è uguale per tutti i tipi di destinazioni di calcolo:

1. Creare un esperimento da eseguire
1. Creazione di un ambiente in cui viene eseguito lo script
1. Creare un ScriptRunConfig, che specifica la destinazione e l'ambiente di calcolo
1. Inviare l'esecuzione
1. Attendere il completamento dell'esecuzione

In alternativa, è possibile:

* Inviare un'esecuzione di HyperDrive per l'[ottimizzazione degli iperparametri](how-to-tune-hyperparameters.md).
* Inviare un esperimento tramite l'[estensione di VS Code](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento nell'area di lavoro.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Selezionare una destinazione di calcolo

Selezionare la destinazione di calcolo in cui eseguirà lo script di training. Se non è specificata alcuna destinazione di calcolo in ScriptRunConfig o se `compute_target='local'` , Azure ml eseguirà lo script localmente. 

Il codice di esempio in questo articolo presuppone che sia già stata creata una destinazione `my_compute_target` di calcolo dalla sezione "Prerequisiti".

## <a name="create-an-environment"></a>Creare un ambiente
Azure Machine Learning [ambienti](concept-environments.md) sono incapsulati nell'ambiente in cui si verifica il training di machine learning. Specificano i pacchetti Python, l'immagine Docker, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi. Specificano anche i Runtime (Python, Spark o Docker).

È possibile definire un ambiente personalizzato o usare un ambiente curato di Azure ML. Per impostazione predefinita, gli [ambienti curati](./how-to-use-environments.md#use-a-curated-environment) sono ambienti predefiniti disponibili nell'area di lavoro. Questi ambienti sono supportati da immagini Docker memorizzate nella cache che riduce il costo di preparazione dell'esecuzione. Vedere [Azure Machine Learning ambienti curati](./resource-curated-environments.md) per l'elenco completo degli ambienti curati disponibili.

Per una destinazione di calcolo remota, è possibile usare uno di questi ambienti con la cura più diffusi per iniziare:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Per altre informazioni e dettagli sugli ambienti, vedere [creare & usare gli ambienti software in Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Destinazione di calcolo locale

Se la destinazione di calcolo è il **computer locale** , l'utente è responsabile di garantire che tutti i pacchetti necessari siano disponibili nell'ambiente Python in cui viene eseguito lo script.  Usare `python.user_managed_dependencies` per usare l'ambiente Python corrente o il Python nel percorso specificato.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Creare la configurazione di esecuzione dello script

Ora che si dispone di una destinazione di calcolo ( `my_compute_target` ) e di un ambiente ( `myenv` ), creare una configurazione di esecuzione dello script che esegua lo script di training ( `train.py` ) che si trova nella `project_folder` Directory:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Se non si specifica un ambiente, verrà creato un ambiente predefinito.

Se si dispone di argomenti della riga di comando che si desidera passare allo script di training, è possibile specificarli tramite il **`arguments`** parametro del costruttore ScriptRunConfig, ad esempio `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Se si vuole eseguire l'override del tempo massimo predefinito consentito per l'esecuzione, è possibile farlo tramite il **`max_run_duration_seconds`** parametro. Il sistema tenterà di annullare automaticamente l'esecuzione se richiede più tempo rispetto a questo valore.

### <a name="specify-a-distributed-job-configuration"></a>Specificare una configurazione per un processo distribuito
Se si desidera eseguire un processo di training distribuito, fornire al parametro la configurazione specifica del processo distribuito **`distributed_job_config`** . I tipi di configurazione supportati includono [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)e [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py). 

Per ulteriori informazioni ed esempi sull'esecuzione di processi Horovod, TensorFlow e PyTorch distribuiti, vedere:

* [Eseguire il training di modelli di TensorFlow](./how-to-train-tensorflow.md#distributed-training)
* [Eseguire il training di modelli di PyTorch](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Inviare l'esperimento

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Quando si invia l'esecuzione di training, viene creato uno snapshot della directory contenente gli script di training che viene quindi inviato alla destinazione di calcolo. Lo snapshot viene inoltre archiviato come parte dell'esperimento nell'area di lavoro. Se si modificano i file e si invia di nuovo l'esecuzione, verranno caricati solo i file modificati.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Per ulteriori informazioni sugli snapshot, vedere [snapshot](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Cartelle speciali** Due cartelle *outputs* e *logs* , hanno un trattamento speciale in Azure Machine Learning. Durante il training, se si scrivono file in cartelle denominate *outputs* e *logs* relative alla directory radice (rispettivamente `./outputs` e `./logs`), i file verranno caricati automaticamente nella cronologia di esecuzione e vi si potrà accedere al termine dell'esecuzione.
>
> Per creare artefatti creati durante il training (ad esempio file modello, checkpoint, file di dati o immagini tracciate), scriverli nella cartella `./outputs`.
>
> Analogamente, è possibile scrivere tutti i log del training eseguito nella cartella `./logs`. Per usare l'[integrazione TensorBoard](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) di Azure Machine Learning, assicurarsi di scrivere i log di TensorBoard in questa cartella. Mentre è in corso l'esecuzione, sarà possibile avviare TensorBoard e riprodurre in streaming questi log.  In seguito sarà anche possibile ripristinare i log di una qualsiasi delle esecuzioni precedenti.
>
> Ad esempio, per scaricare un file scritto nella cartella *outputs* del computer locale dopo l'esecuzione del training remoto: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Rilevamento e integrazione di Git

Quando si avvia un'esecuzione di training in cui la directory di origine è un repository Git locale, le informazioni sul repository vengono archiviate nella cronologia di esecuzione. Per altre informazioni, vedere [Integrazione di Git con Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Esempi di notebook

Vedere questi notebook per esempi di configurazione delle esecuzioni per diversi scenari di training:
* [Training su varie destinazioni di calcolo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Training con i Framework ML](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per il training del modello.
* Vedere come eseguire il training dei modelli con Framework ML specifici, ad esempio [Scikit-learn](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md)e [PyTorch](how-to-train-pytorch.md).
* Consultare le informazioni su come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* Visualizzare la Guida di riferimento di [ScriptRunConfig Class](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) SDK.
* [Usare Azure Machine Learning con le reti virtuali di Azure](./how-to-network-security-overview.md)