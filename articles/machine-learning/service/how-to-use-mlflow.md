---
title: Usare MLflow con il servizio di Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Informazioni su come registrare le metriche e gli elementi usando la libreria MLflow al servizio Azure Machine Learning
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2f3ec9792a7af0398da770286b805be4d1f25589
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657972"
---
# <a name="use-mlflow-with-azure-machine-learning-service-preview"></a>Usare MLflow con il servizio di Azure Machine Learning (anteprima)

Questo articolo illustra come usare MLflow URI di rilevamento e la registrazione di API, collettivamente noto anche come MLflow di rilevamento, con il servizio di Azure Machine Learning per rilevare e registrare metriche esperimento e gli elementi di [Azure Machine Learning area di lavoro servizio](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Se si usa già MLflow di rilevamento per gli esperimenti, l'area di lavoro fornisce una posizione centralizzata, sicura e scalabile per archiviare le metriche di formazione e modelli.

[MLflow](https://www.mlflow.org) è una libreria open source per la gestione del ciclo di vita di esperimenti di machine learning. [Rilevamento MLFlow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) è un componente di MLflow che registra e tiene traccia di metriche di esecuzione di training e gli elementi di modello, se vengono eseguiti in locale, gli esperimenti in una macchina virtuale o in un server remoto di calcolo del cluster.
![mlflow diagramma di azure machine learning](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Confrontare i client MLflow e Azure Machine Learning

 La tabella seguente sono riepilogati i diversi client che è possono usare il servizio di Azure Machine Learning e le relative funzionalità rispettiva funzione.

 Rilevamento MLflow offre la registrazione delle metriche e artefatto offrono funzionalità di archiviazione disponibili solo in caso contrario, tramite il [SDK Python di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | Rilevamento MLflow | Azure Machine Learning <br> Python SDK |  Azure Machine Learning <br> CLI | Portale di Azure|
|-|-|-|-|-|
| Gestire l'area di lavoro |   | ✓ |  ✓ | ✓  |
| Usare gli archivi dati  |   | ✓ |  ✓ |    |
| Registrare la metrica      | ✓ | ✓ |    |    |
| Caricamento degli elementi | ✓ | ✓ |    |    |
| Visualizzare le metriche     | ✓ | ✓ | ✓  | ✓ |
| Gestire il calcolo   |   | ✓ | ✓  | ✓ |
| Distribuire i modelli    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Prerequisiti

* [Installare MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Installare il SDK Python di Azure Machine Learning nel computer locale e creare un'area di lavoro di Azure Machine Learning](setup-create-workspace.md#sdk). il SDK fornisce la connettività per MLflow accedere all'area di lavoro.

## <a name="track-local-runs"></a>Tenere traccia delle esecuzioni locali

Installare il `azureml-contrib-run` pacchetto per l'uso di rilevamento MLflow con Azure Machine Learning on esperimenti eseguiti in locale in un editor di codice o Notebook di Jupyter.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Lo spazio dei nomi azureml.contrib cambia di frequente, come Microsoft si impegna per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Importa i `mlflow` e [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) classi accedere MLflow dell'URI di rilevamento e configurare l'area di lavoro.

Nel codice seguente, il `get_mlflow_tracking_uri()` metodo assegna un indirizzo URI di rilevamento univoco nell'area di lavoro `ws`, e `set_tracking_uri()` punta il MLflow URI di rilevamento per quell'indirizzo.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>L'URI di rilevamento è valida fino a un'ora o meno. Se si riavvia lo script dopo alcuni minuti di inattività, usare l'API get_mlflow_tracking_uri per ottenere un nuovo URI.

Impostare il nome dell'esperimento con MLflow `set_experiment()` e inizia la formazione eseguire con `start_run()`. Usare quindi `log_metric()` ad attivare l'API di registrazione MLflow e iniziare la registrazione di metriche di esecuzione di training.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Tenere traccia delle esecuzioni remote

Esecuzioni remote consentono di eseguire il training dei modelli in servizi di calcolo più potenti, ad esempio macchine virtuali abilitate per GPU o i cluster di calcolo di Machine Learning. Visualizzare [configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md) per altre informazioni sulle opzioni di calcolo diversi.

Configurare il calcolo e l'ambiente di esecuzione di training con il [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) classe. Includere `mlflow` e `azure-contrib-run` pip dei pacchetti nell'ambiente [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) sezione. Quindi costruire [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) con le risorse di calcolo remoto come destinazione di calcolo.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

Nello script di training, importare `mlflow` usare il MLflow le API di registrazione e avviare la registrazione ai criteri di esecuzione.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

Con questa configurazione di esecuzione di training e calcolo, usare il `Experiment.submit("train.py")` metodo per inviare un'esecuzione. Verrà automaticamente impostata il MLflow URI di rilevamento e indirizza la registrazione da MLflow all'area di lavoro.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visualizzare le metriche e gli elementi nell'area di lavoro

Le metriche e gli artefatti dalla registrazione MLflow vengono mantenuti nell'area di lavoro nel [portale di Azure](https://portal.azure.com). Per visualizzare in qualsiasi momento, passare all'area di lavoro e trovare l'esperimento in base al nome.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende usare le metriche registrate e gli elementi nell'area di lavoro, non è attualmente disponibile la possibilità di eliminarli singolarmente. In alternativa, eliminare il gruppo di risorse che contiene l'account di archiviazione e l'area di lavoro, in modo da non incorrere in eventuali costi:

1. Nel portale di Azure fare clic su **Gruppi di risorse** all'estrema sinistra.

   ![Eseguire l'eliminazione nel portale di Azure](media/how-to-use-mlflow/delete-resources.png)

1. Nell'elenco selezionare il gruppo di risorse creato.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse. Selezionare **Elimina**.

## <a name="example-notebooks"></a>Notebook di esempio

Il [MLflow con notebook di Azure Machine Learning](https://aka.ms/azureml-mlflow-examples) vengono illustrati i concetti in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* [Come distribuire un modello](how-to-deploy-and-where.md).
