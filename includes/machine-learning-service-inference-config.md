---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159413"
---
Le voci nel `inferenceconfig.json` documento vengono mappate ai parametri per la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Percorso di un file locale che contiene il codice da eseguire per l'immagine. |
| `sourceDirectory` | `source_directory` | Facoltativa. Percorso delle cartelle che contengono tutti i file per creare l'immagine, semplificando l'accesso a tutti i file all'interno della cartella o della sottocartella. È possibile caricare un'intera cartella dal computer locale come dipendenze per il servizio Web. Nota: i percorsi di entry_script, conda_file e extra_docker_file_steps sono percorsi relativi del percorso source_directory. |
| `environment` | `environment` | Facoltativa.  [Ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning.|

È possibile includere le specifiche complete di un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure machine learning nel file di configurazione dell'inferenza. Se l'ambiente non esiste nell'area di lavoro, Azure Machine Learning lo creerà. In caso contrario, Azure Machine Learning aggiornerà l'ambiente, se necessario. Il codice JSON seguente è un esempio:

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

È anche possibile usare un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning esistente in parametri dell'interfaccia della riga di comando separati e rimuovere la chiave "Environment" dal file di configurazione dell'inferenza. Usare-e per il nome dell'ambiente e--EV per la versione dell'ambiente. Se non si specifica--EV, verrà usata la versione più recente. Di seguito è riportato un esempio di un file di configurazione dell'inferenza:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Il comando seguente illustra come distribuire un modello usando il file di configurazione dell'inferenza precedente (denominato myInferenceConfig.json). 

USA inoltre la versione più recente di un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning esistente (denominato AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
