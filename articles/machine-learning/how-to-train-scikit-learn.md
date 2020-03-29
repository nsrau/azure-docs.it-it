---
title: Addestra modelli di machine learning scikit-learn
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di formazione scikit-learn su scala aziendale usando la classe di stima di Azure Machine Learning SKlearn. Gli script di esempio classificano le immagini dei fiori di iride per creare un modello di apprendimento automatico basato sul set di dati dell'iride di scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942265"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Crea modelli di apprendimento scikit su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come eseguire gli script di formazione scikit-learn su scala aziendale usando la classe di stima di Azure Machine Learning SKlearn.In this article, learn how to run your scikit-learn training scripts at enterprise scale by using the Azure Machine Learning [SKlearn estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) class. 

Gli script di esempio in questo articolo vengono usati per classificare le immagini dei fiori di iride per creare un modello di apprendimento automatico basato sul set di dati [dell'iride](https://archive.ics.uci.edu/ml/datasets/iris)di scikit-learn.

Sia che tu stia allenando un modello di scikit di apprendimento automatico da zero o che tu stia introducendo un modello esistente nel cloud, puoi usare Azure Machine Learning per scalare orizzontalmente i processi di formazione open source usando le risorse di calcolo cloud elastiche. È possibile creare, distribuire, versione e monitorare modelli di livello di produzione con Azure Machine Learning.You can build, deploy, version and monitor production-grade models with Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno di questi ambienti:Run this code on either of these environments:
 - Istanza di calcolo di Azure Machine Learning: non sono necessari download o installazioni

    - Completare [l'esercitazione: Ambiente](tutorial-1st-experiment-sdk-setup.md) di installazione e area di lavoro per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella di formazione degli esempi nel server notebook, trovare un blocco appunti completato ed espanso passando a questa directory: **how-to-use-azureml > ml-frameworks > scikit-learn > formazione > cartella train-hyperparameter-tune-deploy-with-sklearn.**

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Creare un file di [configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - Scaricare il set di dati e il file di script di esempioDownload the dataset and sample script file 
        - [set di dati dell'iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - È inoltre possibile trovare una [versione completata Di Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) di questa guida nella pagina degli esempi di GitHub.You can also find a completed Jupyter Notebook version of this guide on the GitHub samples page. Il notebook include una sezione espansa che copre l'ottimizzazione intelligente degli iperparametri e il recupero del miglior modello in base alle metriche primarie.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione configura l'esperimento di training caricando i pacchetti pitone necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

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
```

### <a name="initialize-a-workspace"></a>Inizializzare un'area di lavoro

[L'area di lavoro](concept-workspace.md) di Azure Machine Learning è la risorsa di primo livello per il servizio. Fornisce una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK è possibile accedere agli [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) elementi dell'area di lavoro creando un oggetto .

Creare un oggetto `config.json` dell'area di lavoro dal file creato nella [sezione Prerequisiti.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Creare un esperimento di Machine Learning

Creare un esperimento e una cartella per contenere gli script di formazione. In questo esempio, creare un esperimento denominato "sklearn-iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Preparare uno script di formazione

In questa esercitazione, lo script di training **train_iris.py** è già disponibile per l'utente. In pratica, si dovrebbe essere in grado di prendere qualsiasi script di training personalizzato così com'è ed eseguirlo con Azure ML senza dover modificare il codice.

Per usare le funzionalità di rilevamento e metriche di Azure ML, aggiungere una piccola quantità di codice di Azure ML all'interno dello script di training.  Lo script di training **train_iris.py** mostra come registrare alcune `Run` metriche nell'esecuzione di Azure ML usando l'oggetto all'interno dello script.

Lo script di training fornito `iris = datasets.load_iris()` usa dati di esempio della funzione.  Per i propri dati, potrebbe essere necessario usare passaggi come Caricare set di [dati e script](how-to-train-keras.md#data-upload) per rendere i dati disponibili durante il training.

Copiare lo script di training **train_iris.py** nella directory del progetto.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Creare o ottenere una destinazione di calcoloCreate or get a compute target

Creare una destinazione di calcolo per il processo di apprendimento di scikit su cui eseguire il processo. Scikit-learn supporta solo nodo singolo, CPU computing.

Il codice seguente crea un calcolo gestito di Azure Machine Learning (AmlCompute) per la risorsa di calcolo di training remoto. La creazione di AmlCompute richiede circa 5 minuti. Se AmlCompute con tale nome è già presente nell'area di lavoro, questo codice ignorerà il processo di creazione.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un obiettivo](concept-compute-target.md) di calcolo.

## <a name="create-a-scikit-learn-estimator"></a>Creare uno stimatore di scikit-learn

Lo [stimatore scikit-learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornisce un modo semplice per lanciare un lavoro di formazione scikit-learn su un obiettivo di calcolo. Viene implementato tramite la [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) classe , che può essere utilizzata per supportare il training della CPU a nodo singolo.

Se lo script di training richiede pacchetti pip o conda aggiuntivi per l'esecuzione, è `pip_packages` possibile `conda_packages` fare in modo che i pacchetti siano installati nell'immagine docker risultante passando i relativi nomi attraverso gli argomenti e .

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


Per ulteriori informazioni sulla personalizzazione dell'ambiente Python, vedere [Creare e gestire ambienti per la formazione e](how-to-use-environments.md)la distribuzione . 

## <a name="submit-a-run"></a>Inviare una corsa

[L'oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Quando viene eseguita l'esecuzione, passa attraverso le seguenti fasi:

- **Preparazione**: Viene creata un'immagine docker in base allo stimatore TensorFlow. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per esecuzioni successive. I log vengono trasmessi anche alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Scalabilità:** il cluster tenta di aumentare le scalabilità se il cluster Batch AI richiede più nodi per eseguire l'esecuzione di quelli attualmente disponibili.

- **In esecuzione:** tutti gli script nella cartella script vengono caricati nella destinazione di calcolo, gli archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e della cartella ./logs vengono trasmessi alla cronologia di esecuzione e possono essere utilizzati per monitorare l'esecuzione.

- **Post-elaborazione:** la cartella ./outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. La registrazione dei modelli consente di archiviare e versione i modelli nell'area di lavoro per semplificare [la gestione e](concept-model-management-and-deployment.md)la distribuzione dei modelli.

Aggiungere il codice seguente allo script di training, train_iris.py, per salvare il modello. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrare il modello nell'area di lavoro con il codice seguente. Specificando i parametri `model_framework` `model_framework_version`, `resource_configuration`e , la distribuzione senza modello di codice diventa disponibile. In questo modo è possibile distribuire direttamente il modello come [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py) servizio Web dal modello registrato e l'oggetto definisce la risorsa di calcolo per il servizio Web.

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

Il modello appena registrato può essere distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo stimatore usato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla creazione di una destinazione di [calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

### <a name="preview-no-code-model-deployment"></a>(Anteprima) Distribuzione del modello senza codice

Anziché la route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per scikit-learn. La distribuzione del modello senza codice è supportata per tutti i tipi di modello scikit-learn incorporati. Registrando il modello come `model_framework`illustrato `model_framework_version`in `resource_configuration` precedenza con i [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) parametri , e , è possibile utilizzare semplicemente la funzione statica per distribuire il modello.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

NOTA: queste dipendenze sono incluse nel contenitore di inferenza scikit-learn predefinito.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

[L'e-how-to](how-to-deploy-and-where.md) completo copre la distribuzione in Azure Machine Learning in modo più approfondito.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il training e la registrazione di un modello di apprendimento scikit e sono stati fornite informazioni sulle opzioni di distribuzione. Vedere questi altri articoli per altre informazioni su Azure Machine Learning.See these other articles to learn more about Azure Machine Learning.

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)
* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)
* [Architettura di riferimento per la formazione sul deep learning distribuito in AzureReference architecture for distributed deep learning training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
