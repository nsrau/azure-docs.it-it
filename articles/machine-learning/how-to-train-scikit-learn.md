---
title: Eseguire il training di modelli di apprendimento automatico Scikit-learn
titleSuffix: Azure Machine Learning
description: Scopri in che modo Azure Machine Learning ti permette di ridimensionare un processo di formazione Scikit-learn usando risorse di calcolo di cloud elastico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4758e937a0ed105bf136acf7e78f2d44c84e74fb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536055"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Eseguire il training di modelli Scikit-learn su larga scala con Azure Machine Learning

Questo articolo illustra come eseguire gli script di training di Scikit-learn con Azure Machine Learning.

Gli script di esempio in questo articolo vengono usati per classificare le immagini Flower Iris per creare un modello di machine learning basato sul [set di dati Iris](https://archive.ics.uci.edu/ml/datasets/iris)Scikit-learn.

Che tu stia eseguendo il training di un modello Scikit-learn di machine learning da zero o che crei un modello esistente nel cloud, puoi usare Azure Machine Learning per scalare in orizzontale processi di training open source con risorse di calcolo elastiche per il cloud. Con Azure Machine Learning è possibile compilare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:
 - Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

    - Completare l' [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md)  per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella Training degli esempi sul server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > ml-frameworks > Scikit-learn > Train-iperparameter-Tune-deploy-with-sklearn** Folder.

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, definendo l'ambiente di training e preparando lo script di training.

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) oggetto.

Creare un oggetto dell'area di lavoro dal `config.json` file creato nella [sezione Prerequisiti](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Preparare gli script

In questa esercitazione, lo script di training **train_iris. py** è già disponibile [qui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). In pratica, si dovrebbe essere in grado di eseguire qualsiasi script di training personalizzato ed eseguirlo con Azure ML senza dover modificare il codice.

Note:
- Lo script di training fornito Mostra come registrare alcune metriche nell'esecuzione di Azure ML usando l' `Run` oggetto all'interno dello script.
- Lo script di training fornito usa i dati di esempio della  `iris = datasets.load_iris()` funzione.  Per usare e accedere ai propri dati, vedere [How to train with DataSets](how-to-train-with-datasets.md) per rendere i dati disponibili durante il training.

### <a name="define-your-environment"></a>Definire l'ambiente

Per definire l' [ambiente](concept-environments.md) Azure ml che incapsula le dipendenze dello script di training, è possibile definire un ambiente personalizzato o usare l'ambiente curato di Azure ml.

#### <a name="use-a-curated-environment"></a>Usare un ambiente curato
Facoltativamente, Azure ML fornisce ambienti predefiniti e curati se non si vuole definire un ambiente personalizzato. Per altre informazioni, vedere [qui](resource-curated-environments.md).
Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Creare un ambiente personalizzato

È anche possibile creare un proprio ambiente personalizzato. Definire le dipendenze conda in un file YAML; in questo esempio il file è denominato `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Creare un ambiente Azure ML da questa specifica dell'ambiente conda. L'ambiente verrà incluso in un contenitore Docker in fase di esecuzione.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Per altre informazioni sulla creazione e sull'uso degli ambienti, vedere [creare e usare ambienti software in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configurare e inviare l'esecuzione del training

### <a name="create-a-scriptrunconfig"></a>Creare un ScriptRunConfig
Creare un oggetto ScriptRunConfig per specificare i dettagli di configurazione del processo di training, tra cui script di training, ambiente da usare e destinazione di calcolo in cui eseguirlo.
Eventuali argomenti dello script di training verranno passati tramite la riga di comando se specificati nel `arguments` parametro.

Il codice seguente consente di configurare un oggetto ScriptRunConfig per l'invio del processo per l'esecuzione nel computer locale.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Se invece si vuole eseguire il processo in un cluster remoto, è possibile specificare la destinazione di calcolo desiderata per il `compute_target` parametro di ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Invia la tua esecuzione
```python
from azureml.core import Experiment

run = Experiment(ws,'train-iris').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning esegue gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si desidera caricare, utilizzare un [file con estensione ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) o non includerlo nella directory di origine. È invece possibile accedere ai dati usando un [set](how-to-train-with-datasets.md)di dati di Azure ml.

### <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione** : viene creata un'immagine Docker in base all'ambiente definito. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento. Se invece si specifica un ambiente curato, verrà utilizzata l'immagine memorizzata nella cache che supporta l'ambiente curato.

- **Ridimensionamento** : il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running** : tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e `script` viene eseguito. Gli output da stdout e la cartella **./logs** vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione** : la cartella **./Outputs** dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

Aggiungere il codice seguente allo script di training, train_iris. py, per salvare il modello. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrare il modello nell'area di lavoro con il codice seguente. Specificando i parametri `model_framework` , `model_framework_version` e `resource_configuration` , la distribuzione del modello senza codice diventa disponibile. La distribuzione del modello senza codice consente di distribuire direttamente il modello come servizio Web dal modello registrato e l' [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?preserve-view=true&view=azure-ml-py) oggetto definisce la risorsa di calcolo per il servizio Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Distribuzione

Il modello appena registrato può essere distribuito esattamente come qualsiasi altro modello registrato in Azure ML. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

### <a name="preview-no-code-model-deployment"></a>Anteprima Distribuzione del modello senza codice

Anziché la route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per Scikit-learn. La distribuzione del modello senza codice è supportata per tutti i tipi di modello Scikit-learn predefiniti. Registrando il modello come illustrato in precedenza con i `model_framework` `model_framework_version` parametri, e `resource_configuration` , è possibile usare semplicemente la [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) funzione statica per distribuire il modello.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Nota: queste dipendenze sono incluse nel contenitore di inferenza Scikit-learn predefinito.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Il metodo [completo illustra la distribuzione](how-to-deploy-and-where.md) in Azure Machine Learning più approfondita.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di un modello Scikit-learn e sono state apprese le opzioni di distribuzione. Per ulteriori informazioni su Azure Machine Learning, vedere questi altri articoli.

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
