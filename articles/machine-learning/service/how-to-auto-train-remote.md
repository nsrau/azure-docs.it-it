---
title: Destinazioni di calcolo remote per Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come creare modelli usando Machine Learning automatizzato in una destinazione di calcolo remota Azure Machine Learning con Azure Machine Learning
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5104e6e037341c41a032f80287c6d56d17361d4c
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932189"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Eseguire il training di modelli di apprendimento automatico nel cloud

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In Azure Machine Learning è possibile eseguire il training di un modello su diversi tipi di risorse di calcolo gestite. La destinazione di calcolo può essere un computer locale o una risorsa nel cloud.

È possibile aumentare o ridurre la scalabilità orizzontale dell'esperimento di Machine Learning aggiungendo altre destinazioni di calcolo, ad esempio Azure Machine Learning Compute (AmlCompute). AmlCompute è un'infrastruttura di calcolo gestito che consente di creare facilmente un calcolo a nodo singolo o a più nodi.

In questo articolo si apprenderà come creare un modello usando l'automazione di Machine Learning con AmlCompute.

## <a name="how-does-remote-differ-from-local"></a>In che modo il remote e il locale sono diversi?

L'esercitazione "eseguire il[training di un modello di classificazione con Machine Learning automatico](tutorial-auto-train-models.md)" illustra come usare un computer locale per eseguire il training di un modello con ml automatico. Il flusso di lavoro durante il training in locale si applica anche nelle destinazioni remoto. Tuttavia, con il calcolo remoto, le iterazioni dell'esperimento ML automatizzate vengono eseguite in modo asincrono. Questa funzionalità consente di annullare un'iterazione specifica, controllare lo stato dell'esecuzione o continuare a lavorare su altre celle nel notebook di Jupyter. Per eseguire il training in remoto, creare prima di tutto una destinazione di calcolo remota, ad esempio AmlCompute. È quindi possibile configurare la risorsa remota e inviare il codice.

Questo articolo illustra i passaggi aggiuntivi necessari per eseguire un esperimento di Machine Learning automatico in una destinazione AmlCompute remota. L'oggetto dell'area di lavoro, `ws`, creato nell'esercitazione viene usato in tutto il codice qui.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Crea risorsa

Creare la destinazione AmlCompute nell'area di lavoro (`ws`) se non esiste già.

**Tempo stimato**: la creazione della destinazione AmlCompute richiede circa 5 minuti.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

È ora possibile usare l'oggetto `compute_target` come destinazione di calcolo remota.

Le restrizioni relative ai nomi di cluster includono:
+ Non deve contenere più di 64 caratteri.
+ Non può contenere i caratteri seguenti: `\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-tabulardataset-function"></a>Accedere ai dati tramite la funzione TabularDataset

Definito X e y come `TabularDataset`s, che vengono passati a ML automatico in AutoMLConfig. per impostazione predefinita, `from_delimited_files` imposta la `infer_column_types` su true, che dedurrà automaticamente il tipo di colonna. 

Se si desidera impostare manualmente i tipi di colonna, è possibile impostare l'argomento `set_column_types` per impostare manualmente il tipo di ogni colonna. Nell'esempio di codice seguente, i dati provengono dal pacchetto sklearn.

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>Crea configurazione di esecuzione

Per rendere disponibili le dipendenze per lo script get_data. py, definire un oggetto `RunConfiguration` con `CondaDependencies`definito. Utilizzare questo oggetto per il parametro `run_configuration` in `AutoMLConfig`.

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

Vedere questo [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) per un altro esempio di questo schema progettuale.

## <a name="configure-experiment"></a>Configurare l'esperimento
Specificare le impostazioni per `AutoMLConfig`.  (Vedere un [elenco completo di parametri](how-to-configure-auto-train.md#configure-experiment) e i relativi valori possibili.)

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>Inviare l'esperimento di training

A questo punto inviare la configurazione per selezionare automaticamente l'algoritmo, i parametri hyper e il training del modello.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

L'output sarà simile all'esempio seguente:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Esplorare i risultati

Per visualizzare un grafico e una tabella di risultati, è possibile usare lo stesso [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) , come illustrato nell' [esercitazione di training](tutorial-auto-train-models.md#explore-the-results) .

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

Ecco un'immagine statica del widget.  Nel notebook, è possibile fare clic su una qualsiasi riga della tabella per visualizzare le proprietà di esecuzione e i log di output per quell'esecuzione.   È possibile usare l'elenco a discesa sopra il grafico per visualizzare un grafico di ogni metrica disponibile per ogni iterazione.

![Tabella del widget](./media/how-to-auto-train-remote/table.png)
![tracciato del widget](./media/how-to-auto-train-remote/plot.png)

Il widget visualizza un URL da usare per visualizzare ed esplorare i singoli dettagli dell'esecuzione.  

Se non si è in un notebook di Jupyter, è possibile visualizzare l'URL dall'esecuzione stessa:

```
remote_run.get_portal_url()
```

Le stesse informazioni sono disponibili nell'area di lavoro.  Per altre informazioni su questi risultati, vedere informazioni sui [risultati automatici di Machine Learning](how-to-understand-automated-ml.md).

## <a name="example"></a>Esempio

Il [notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb) seguente illustra i concetti in questo articolo.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su [How to configure settings for automatic training](how-to-configure-auto-train.md) (Come configurare le impostazioni per il training automatico).
* Vedere le [procedure per](how-to-machine-learning-interpretability-automl.md) abilitare le funzionalità di interpretazione dei modelli negli esperimenti di Machine Learning automatici.
