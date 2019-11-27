---
title: Eseguire il training di modelli di apprendimento automatico Scikit-learn
titleSuffix: Azure Machine Learning
description: Informazioni su come eseguire gli script di training di Scikit-learn su scala aziendale usando la classe SKlearn Estimator di Azure Machine Learning. Gli script di esempio classificano le immagini fiore Iris per creare un modello di machine learning basato sul set di dati Iris Scikit-learn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: a85d33a804c8aaf3081439806bf69dab5263dcf2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224850"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Creazione di modelli Scikit-learn su larga scala con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come eseguire gli script di training di Scikit-learn su scala aziendale usando la classe [SKlearn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) di Azure Machine Learning. 

Gli script di esempio in questo articolo vengono usati per classificare le immagini Flower Iris per creare un modello di machine learning basato sul [set di dati Iris](https://archive.ics.uci.edu/ml/datasets/iris)Scikit-learn.

Che tu stia eseguendo il training di un modello Scikit-learn di machine learning da zero o che crei un modello esistente nel cloud, puoi usare Azure Machine Learning per scalare in orizzontale processi di training open source con risorse di calcolo elastiche per il cloud. Con Azure Machine Learning è possibile creare, distribuire, eseguire la versione e monitorare i modelli a livello di produzione.

## <a name="prerequisites"></a>prerequisiti

Eseguire questo codice in uno degli ambienti seguenti:
 - Azure Machine Learning macchina virtuale Notebook-nessun download o installazione necessaria

    - Completare l' [esercitazione: configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md) per creare un server notebook dedicato precaricato con l'SDK e il repository di esempio.
    - Nella cartella Training degli esempi sul server notebook trovare un notebook completato e espanso passando a questa directory: **How-to-use-azureml > ml-frameworks > Scikit-learn > training > Train-iperparameter-Tune-deploy-with-sklearn** cartella.

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Creare un file di configurazione dell'area di lavoro](how-to-configure-environment.md#workspace).
    - Scaricare il set di dati e il file di script di esempio 
        - [set di dati Iris](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - È anche possibile trovare una [versione di Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb) completata di questa guida nella pagina degli esempi di GitHub. Il notebook include una sezione espansa che illustra l'ottimizzazione di iperparametri intelligenti e il recupero del modello migliore in base alle metriche primarie.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

Questa sezione Configura l'esperimento di training caricando i pacchetti Python necessari, inizializzando un'area di lavoro, creando un esperimento e caricando i dati di training e gli script di training.

### <a name="import-packages"></a>Importare i pacchetti

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

L' [area di lavoro Azure Machine Learning](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli artefatti creati. In Python SDK è possibile accedere agli elementi dell'area di lavoro creando un oggetto [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Creare un oggetto area di lavoro dal file `config.json` creato nella [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>Creare un esperimento di Machine Learning

Creare un esperimento e una cartella che contengano gli script di training. In questo esempio, creare un esperimento denominato "sklearn-Iris".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>Preparare lo script di training

In questa esercitazione, lo script di training **train_iris. py** è già disponibile. In pratica, si dovrebbe essere in grado di eseguire qualsiasi script di training personalizzato ed eseguirlo con Azure ML senza dover modificare il codice.

Per usare le funzionalità di rilevamento e metrica di Azure ML, aggiungere una piccola quantità di codice di Azure ML all'interno dello script di training.  Lo script di training **train_iris. py** Mostra come registrare alcune metriche nell'esecuzione di Azure ml usando l'oggetto `Run` all'interno dello script.

Lo script di training fornito usa i dati di esempio della funzione `iris = datasets.load_iris()`.  Per i dati personali, potrebbe essere necessario usare passaggi come il caricamento di set di dati [e script](how-to-train-keras.md#data-upload) per rendere disponibili i dati durante il training.

Copiare lo script di training **train_iris. py** nella directory del progetto.

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>Creare o ottenere una destinazione di calcolo

Creare una destinazione di calcolo per il processo Scikit-learn da eseguire in. Scikit-learn supporta solo il singolo nodo, il calcolo della CPU.

Il codice seguente crea una Azure Machine Learning calcolo gestito (AmlCompute) per la risorsa di calcolo di training remoto. La creazione di AmlCompute richiede circa 5 minuti. Se il AmlCompute con lo stesso nome è già presente nell'area di lavoro, questo codice ignorerà il processo di creazione.

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

Per altre informazioni sulle destinazioni di calcolo, vedere l'articolo [che cos'è un calcolo di destinazione](concept-compute-target.md) .

## <a name="create-a-scikit-learn-estimator"></a>Creare un Scikit-learn Estimator

[Scikit-learn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py) fornisce un modo semplice per avviare un processo di formazione Scikit-learn in una destinazione di calcolo. Viene implementato tramite la classe [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) , che può essere utilizzata per supportare il training della CPU a nodo singolo.

Se lo script di training richiede l'esecuzione di pacchetti PIP o conda aggiuntivi, è possibile fare in modo che i pacchetti siano installati nell'immagine Docker risultante passando i relativi nomi tramite gli argomenti `pip_packages` e `conda_packages`.

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

## <a name="submit-a-run"></a>Invia un'esecuzione

L' [oggetto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia alla cronologia di esecuzione mentre il processo è in esecuzione e dopo il completamento.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Quando l'esecuzione viene eseguita, vengono eseguite le fasi seguenti:

- **Preparazione**: viene creata un'immagine Docker in base allo strumento di stima TensorFlow. L'immagine viene caricata nel registro contenitori dell'area di lavoro e memorizzata nella cache per le esecuzioni successive. Anche i log vengono trasmessi alla cronologia di esecuzione e possono essere visualizzati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: il cluster tenta di eseguire la scalabilità verticale se il cluster batch per intelligenza artificiale richiede un numero maggiore di nodi per eseguire l'esecuzione rispetto al momento disponibile.

- **Running**: tutti gli script nella cartella di script vengono caricati nella destinazione di calcolo, vengono montati o copiati gli archivi dati e viene eseguita la entry_script. Gli output da stdout e la cartella./logs vengono trasmessi alla cronologia di esecuzione e possono essere usati per monitorare l'esecuzione.

- **Post-elaborazione**: la cartella./Outputs dell'esecuzione viene copiata nella cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training del modello, è possibile salvarlo e registrarlo nell'area di lavoro. Con la registrazione del modello è possibile archiviare e deversionere i modelli nell'area di lavoro per semplificare la [gestione e la distribuzione del modello](concept-model-management-and-deployment.md).

Aggiungere il codice seguente allo script di training, train_iris. py, per salvare il modello. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrare il modello nell'area di lavoro con il codice seguente. Specificando i parametri `model_framework`, `model_framework_version`e `resource_configuration`, la distribuzione del modello senza codice diventa disponibile. In questo modo è possibile distribuire direttamente il modello come servizio Web dal modello registrato e l'oggetto `ResourceConfiguration` definisce la risorsa di calcolo per il servizio Web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Distribuzione

Il modello appena registrato può essere distribuito esattamente come qualsiasi altro modello registrato in Azure Machine Learning, indipendentemente dallo strumento di stima utilizzato per il training. La procedura di distribuzione contiene una sezione sulla registrazione dei modelli, ma è possibile passare direttamente alla [creazione di una destinazione di calcolo](how-to-deploy-and-where.md#choose-a-compute-target) per la distribuzione, poiché si dispone già di un modello registrato.

### <a name="preview-no-code-model-deployment"></a>Anteprima Distribuzione del modello senza codice

Anziché la route di distribuzione tradizionale, è anche possibile usare la funzionalità di distribuzione senza codice (anteprima) per Scikit-learn. La distribuzione del modello senza codice è supportata per tutti i tipi di modello Scikit-learn predefiniti. Registrando il modello come illustrato in precedenza con i parametri `model_framework`, `model_framework_version`e `resource_configuration`, è possibile usare semplicemente la funzione statica `deploy()` per distribuire il modello.

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
