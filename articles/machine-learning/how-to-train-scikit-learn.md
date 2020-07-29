---
title: Eseguire il training di modelli di apprendimento automatico Scikit-learn
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di Scikit-learn in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: dfe3d0e7bf0d291807a5a051f834753c7816801a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320885"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Creazione di modelli Scikit-learn su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire gli script di training di Scikit-learn con Azure Machine Learning.

Gli script di esempio in questo articolo vengono usati per classificare le immagini Flower Iris per creare un modello di machine learning basato sul [set di dati Iris](https://archive.ics.uci.edu/ml/datasets/iris)Scikit-learn.

Che tu stia eseguendo il training di un modello Scikit-learn di machine learning da zero o che crei un modello esistente nel cloud, puoi usare Azure Machine Learning per scalare in orizzontale processi di training open source con risorse di calcolo elastiche per il cloud. Con Azure Machine Learning è possibile compilare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:
 - Istanza di calcolo di Azure Machine Learning: nessun download o installazione necessaria

    - Completare l' [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella Training degli esempi sul server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > ml-frameworks > Scikit-learn > training > Train-iperparameter-Tune-deploy-with-sklearn** cartella.

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) oggetto.

Creare un oggetto dell'area di lavoro dal `config.json` file creato nella [sezione Prerequisiti](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>Preparare gli script

In questa esercitazione, lo script di training **train_iris. py** è già disponibile [qui](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py). In pratica, si dovrebbe essere in grado di eseguire qualsiasi script di training personalizzato ed eseguirlo con Azure ML senza dover modificare il codice.

Note:
- Lo script di training fornito Mostra come registrare alcune metriche nell'esecuzione di Azure ML usando l' `Run` oggetto all'interno dello script.
- Lo script di training fornito usa i dati di esempio della `iris = datasets.load_iris()` funzione.  Per i dati personali, potrebbe essere necessario usare passaggi come il caricamento di set di dati [e script](how-to-train-keras.md#data-upload) per rendere disponibili i dati durante il training.

### <a name="define-your-environment"></a>Definire l'ambiente.

#### <a name="create-a-custom-environment"></a>Creare un ambiente personalizzato.

Creare l'ambiente conda (sklearn-ENV. yml).
Per scrivere l'ambiente conda da un notebook, è possibile aggiungere la riga ```%%writefile sklearn-env.yml``` nella parte superiore della cella.

```yaml
name: sklearn-training-env
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

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
```

#### <a name="use-a-curated-environment"></a>Usare un ambiente curato
Azure ML offre ambienti di contenitori predefiniti e curati se non si vuole creare un'immagine personalizzata. Per altre informazioni, vedere [qui](resource-curated-environments.md).
Se si vuole usare un ambiente curato, è invece possibile eseguire il comando seguente:

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>Creare un ScriptRunConfig

Questo ScriptRunConfig invierà il processo per l'esecuzione nella destinazione di calcolo locale.

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
src.run_config.environment = myenv
```

Se si vuole inviare un cluster remoto, è possibile modificare run_config. target per la destinazione di calcolo desiderata.

### <a name="submit-your-run"></a>Invia la tua esecuzione
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure Machine Learning esegue gli script di training copiando l'intera directory di origine. Se si dispone di dati sensibili che non si desidera caricare, utilizzare un [file con estensione ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) o non includerlo nella directory di origine. È invece possibile accedere ai dati usando un [Archivio](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)dati.

Per altre informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per il training e la distribuzione](how-to-use-environments.md). 

## <a name="what-happens-during-run-execution"></a>Cosa accade durante l'esecuzione dell'esecuzione
Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**: viene creata un'immagine Docker in base allo strumento di stima TensorFlow. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running**: tutti gli script nella cartella di script vengono caricati nella destinazione di calcolo, vengono montati o copiati gli archivi dati e viene eseguita la entry_script. Gli output da stdout e la cartella./logs vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: la cartella./Outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nell'area di lavoro per semplificare la [gestione e la distribuzione dei modelli](concept-model-management-and-deployment.md).

Aggiungere il codice seguente allo script di training, train_iris. py, per salvare il modello. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrare il modello nell'area di lavoro con il codice seguente. Specificando i parametri `model_framework` , `model_framework_version` e `resource_configuration` , la distribuzione del modello senza codice diventa disponibile. La distribuzione del modello senza codice consente di distribuire direttamente il modello come servizio Web dal modello registrato e l' [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) oggetto definisce la risorsa di calcolo per il servizio Web.

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

Il modello appena registrato può essere distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo strumento di stima utilizzato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

### <a name="preview-no-code-model-deployment"></a>Anteprima Distribuzione del modello senza codice

Anziché la route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per Scikit-learn. La distribuzione del modello senza codice è supportata per tutti i tipi di modello Scikit-learn predefiniti. Registrando il modello come illustrato in precedenza con i `model_framework` `model_framework_version` parametri, e `resource_configuration` , è possibile usare semplicemente la [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) funzione statica per distribuire il modello.

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
* [Architettura di riferimento per il training di Deep learning distribuito in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
