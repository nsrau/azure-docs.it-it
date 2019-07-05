---
title: Eseguire il training e registrare scikit-informazioni su modelli
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come eseguire il training e registrare un scikit-informazioni su modelli mediante il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: d2c9b104d1fe9333221bc20e7e23b436358c9ece
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514031"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Eseguire il training e di registrare modelli Scikit-learn su larga scala con il servizio di Azure Machine Learning

Questo articolo illustra come eseguire il training e registrare un modello di Scikit-learn usando il servizio di Azure Machine Learning. Usa il diffuso [set di dati Iris](https://archive.ics.uci.edu/ml/datasets/iris) classificare le immagini in fiore iris con l'oggetto personalizzato [scikit-informazioni su](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) classe.

Scikit-learn è un framework di elaborazione open source comunemente usato per machine learning. Con il servizio di Azure Machine Learning, è possibile aumentare rapidamente i processi di training open source con le risorse di calcolo cloud elastico. È inoltre possibile monitorare le esecuzioni di training, i modelli di versione, la distribuzione di modelli e molto altro ancora.

Se si sta sviluppando un modello Scikit-learn da zero o si desidera introdurre un modello esistente nel cloud, servizio Azure Machine Learning consentono di compilare modelli pronti per la produzione.

## <a name="prerequisites"></a>Prerequisiti

Eseguire questo codice in uno dei due ambienti:
 - Azure Machine Learning Notebook VM - alcun download o installazione necessaria

    - Completare la [Guida introduttiva di notebook basato su cloud](quickstart-run-cloud-notebook.md) per creare un server notebook dedicato precaricato con il SDK e il repository di esempio.
    - Nella cartella samples nel server notebook, trovare un notebook completato ed espanso passando a questa directory: **how-to-uso-azureml > formazione > train-hyperparameter-tune-deploy-with-sklearn** cartella.

 - Server Jupyter Notebook personale

    - [Installare Azure Machine Learning SDK per Python](setup-create-workspace.md#sdk)
    - [Creare un file di configurazione dell'area di lavoro](setup-create-workspace.md#write-a-configuration-file)
    - [Scaricare il file di script di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - È anche possibile trovare un completate [versione di Notebook di Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) di questa Guida nella pagina degli esempi di GitHub. Il notebook include una sezione espansa copertura intelligente degli iperparametri di ottimizzazione e il recupero del modello migliore con le metriche primarie.

## <a name="set-up-the-experiment"></a>Configurare l'esperimento

In questa sezione si configura l'esperimento di training per il caricamento di pacchetti python necessari, l'inizializzazione di un'area di lavoro, creazione di un esperimento e caricare i dati di training e gli script di training.

### <a name="import-packages"></a>Importare pacchetti

In primo luogo, importare le librerie di Python necessari.

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

Il [dell'area di lavoro di Azure Machine Learning servizio](concept-workspace.md) è la risorsa di primo livello per il servizio. Offre una posizione centralizzata per lavorare con tutti gli elementi creati. In Python SDK, è possibile accedere gli elementi dell'area di lavoro mediante la creazione di un [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) oggetto.

Crea un oggetto dell'area di lavoro dal `config.json` creato nel file il [sezione Prerequisiti](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Creare un esperimento

Creare un esperimento e una cartella per contenere gli script di training. In questo esempio, creare un esperimento denominato "iris-sklearn".

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Caricare set di dati e gli script

Il [datastore](how-to-access-data.md) è una posizione in cui i dati possono essere archiviati e a cui accede il montaggio o copiare i dati nella destinazione di calcolo. Ogni area di lavoro fornisce un archivio dati predefinito. Caricare i dati e script di training per l'archivio dati in modo che sono facilmente accessibili durante il training.

1. Creare la directory per i dati.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Caricare il set di dati iris nell'archivio dati predefinito.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Script di training caricamento Scikit-learn, `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Creare o ottenere una destinazione di calcolo

Creare una destinazione di calcolo per il processo da eseguire su Scikit-learn. Scikit altre supporta solo nodo, di elaborazione della CPU.

Il codice seguente, crea un calcolo gestita di Azure Machine Learning (AmlCompute) per la risorsa di calcolo remoto corsi di formazione. La creazione di AmlCompute richiede circa 5 minuti. Se il AmlCompute con lo stesso nome è già nell'area di lavoro, questo codice verrà eseguito il processo di creazione.

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

Per altre informazioni sulle destinazioni di calcolo, vedere la [What ' s una destinazione di calcolo](concept-compute-target.md) articolo.

## <a name="create-a-scikit-learn-estimator"></a>Creare un Estimatore Scikit-learn

Il [estimator Scikit-learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornisce un modo semplice per l'avvio di Scikit-learn processo di training in una destinazione di calcolo. Viene implementato tramite il [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) (classe), che può essere usato per supportare la formazione di CPU a nodo singolo.

Se lo script di training deve aggiuntive pip o conda pacchetti da eseguire, è possibile usare i pacchetti installati nell'immagine docker risultante passando i nomi tramite il `pip_packages` e `conda_packages` argomenti.

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

Il [oggetto esecuzione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornisce l'interfaccia per la cronologia di esecuzione durante l'esecuzione del processo e dopo il completamento.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

L'esecuzione viene eseguita, la chiamata passa attraverso le fasi seguenti:

- **Preparazione**: In base alla stima di TensorFlow viene creata un'immagine docker. L'immagine viene caricata in Registro contenitori dell'area di lavoro e memorizzato nella cache per le esecuzioni successive. I log vengono anche trasmessi per la cronologia di esecuzione e possono essere consultati per monitorare lo stato di avanzamento.

- **Ridimensionamento**: Il cluster tenta di aumentare le prestazioni se il cluster Batch per intelligenza artificiale richiede più nodi per l'esecuzione dell'esecuzione rispetto a quelli attualmente disponibili.

- **In esecuzione**: Tutti gli script nella cartella dello script vengono caricati nella destinazione di calcolo, archivi dati vengono montati o copiati e viene eseguito il entry_script. Gli output di stdout e. / cartella dei log vengono inviati nel flusso per la cronologia di esecuzione e può essere usato per monitorare l'esecuzione.

- **Post-elaborazione**: I. / output dell'esecuzione della cartella da copiare la cronologia di esecuzione.

## <a name="save-and-register-the-model"></a>Salvare e registrare il modello

Dopo aver eseguito il training modello, è possibile salvare e registrarlo per l'area di lavoro. Registrazione del modello consente di store e la versione dei modelli nell'area di lavoro per semplificare [modello di distribuzione e gestione](concept-model-management-and-deployment.md).

Aggiungere il codice seguente allo script di training, train_iris.py, salvare il modello. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Registrare il modello nell'area di lavoro con il codice seguente.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, viene sottoposto a training e registrato un modello Scikit-learn nel servizio di Azure Machine Learning.

* Per informazioni su come distribuire un modello, passare al nostro [distribuzione del modello](how-to-deploy-and-where.md) articolo.

* [Ottimizzare gli iperparametri](how-to-tune-hyperparameters.md)

* [Tracciare le metriche di esecuzione durante il training](how-to-track-experiments.md)