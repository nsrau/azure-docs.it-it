---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: f500df6a592769928470d22468ff2fdff18293a5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748757"
---
Le voci del documento `inferenceconfig.json` vengono mappate ai parametri della classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro per il metodo | Descrizione |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Percorso di un file locale che contiene il codice da eseguire per l'immagine. |
| `sourceDirectory` | `source_directory` | Facoltativa. Percorso delle cartelle che contengono tutti i file per la creazione dell'immagine, semplificando l'accesso a qualsiasi file all'interno di questa cartella o sottocartella. È possibile caricare un'intera cartella dal computer locale come dipendenze per il servizio Web. Nota: i percorsi your entry_script, conda_file e extra_docker_file_steps sono relativi al percorso source_directory. |
| `environment` | `environment` | Facoltativa.  [Ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning.|

Nei file di configurazione dell'inferenza è possibile includere le specifiche complete di un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning. Se l'ambiente non esiste nell'area di lavoro, Azure Machine Learning lo creerà. In caso contrario, Azure Machine Learning aggiornerà l'ambiente, se necessario. Di seguito è riportato un file JSON di esempio:

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
                            "scikit-learn==0.22.1",
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

È anche possibile usare un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning esistente nei parametri dell'interfaccia della riga di comando separati e rimuovere la chiave "environment" dal file di configurazione dell'inferenza. Usare -e per il nome dell'ambiente e --ev per la versione. Se non si specifica--ev, verrà usata la versione più recente. Ecco un esempio di file di configurazione dell'inferenza:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Il comando seguente illustra come distribuire un modello usando il file di configurazione dell'inferenza precedente (denominato myInferenceConfig.json). 

Usa inoltre la versione più recente di un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning esistente (denominato AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
