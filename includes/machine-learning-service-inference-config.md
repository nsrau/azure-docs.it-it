---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159413"
---
Le voci nella `inferenceconfig.json` mappa documento vengono mappate ai parametri per la classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) Nella tabella seguente viene descritto il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Descrizione |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Percorso di un file locale che contiene il codice da eseguire per l'immagine. |
| `sourceDirectory` | `source_directory` | Facoltativa. Percorso delle cartelle che contengono tutti i file per creare l'immagine, che rende facile accedere a tutti i file all'interno di questa cartella o sottocartella. È possibile caricare un'intera cartella dal computer locale come dipendenze per il servizio Web.You can upload an entire folder from your local machine as dependencies for the Webservice. Nota: i percorsi entry_script, conda_file e extra_docker_file_steps sono percorsi relativi al percorso source_directory. |
| `environment` | `environment` | Facoltativa.  Ambiente Di Azure Machine [Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

È possibile includere specifiche complete di un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning nel file di configurazione dell'inferenza. Se questo ambiente non esiste nell'area di lavoro, Azure Machine Learning lo creerà. In caso contrario, Azure Machine Learning aggiornerà l'ambiente se necessario. Il codice JSON seguente è un esempio:The following JSON is an example:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

È anche possibile usare un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning esistente in parametri CLI separati e rimuovere la chiave "ambiente" dal file di configurazione dell'inferenza. Utilizzare -e come nome dell'ambiente e --ev per la versione dell'ambiente. Se non si specifica --ev, verrà utilizzata la versione più recente. Di seguito è riportato un esempio di file di configurazione dell'inferenza:Here is an example of an inference configuration file:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Il comando seguente illustra come distribuire un modello utilizzando il file di configurazione dell'inferenza precedente (denominato myInferenceConfig.json). 

Usa anche la versione più recente di un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning esistente (denominato AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
