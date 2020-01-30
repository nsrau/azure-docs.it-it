---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845166"
---
Le voci del documento `inferenceconfig.json` mappano i parametri per la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Percorso di un file locale che contiene il codice da eseguire per l'immagine. |
| `runtime` | `runtime` | Facoltativa. Runtime da usare per l'immagine. I runtime supportati sono `spark-py` e `python`. Se `environment` è impostata, questa voce viene ignorata. |
| `condaFile` | `conda_file` | Facoltativa. Percorso di un file locale che contiene una definizione di ambiente conda da usare per l'immagine.  Se `environment` è impostata, questa voce viene ignorata. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Facoltativa. Percorso di un file locale che contiene passaggi aggiuntivi di Docker da eseguire durante la configurazione dell'immagine.  Se `environment` è impostata, questa voce viene ignorata.|
| `sourceDirectory` | `source_directory` | Facoltativa. Percorso delle cartelle che contengono tutti i file per creare l'immagine, semplificando l'accesso a tutti i file all'interno della cartella o della sottocartella. È possibile caricare un'intera cartella dal computer locale come dipendenze per il servizio Web. Nota: i percorsi di entry_script, conda_file e extra_docker_file_steps sono percorsi relativi del percorso source_directory. |
| `enableGpu` | `enable_gpu` | Facoltativa. Indica se abilitare il supporto GPU nell'immagine. È necessario usare l'immagine GPU in un servizio di Azure, ad esempio istanze di contenitore di Azure. Ad esempio, Azure Machine Learning calcolo, le macchine virtuali di Azure e il servizio Azure Kubernetes. Il valore predefinito è False. Se `environment` è impostata, questa voce viene ignorata.|
| `baseImage` | `base_image` | Facoltativa. Immagine personalizzata da usare come immagine di base. Se non viene specificata alcuna immagine di base, l'immagine sarà basata sul parametro di runtime specificato. Se `environment` è impostata, questa voce viene ignorata. |
| `baseImageRegistry` | `base_image_registry` | Facoltativa. Registro immagini che contiene l'immagine di base. Se `environment` è impostata, questa voce viene ignorata.|
| `cudaVersion` | `cuda_version` | Facoltativa. Versione di CUDA da installare per le immagini che necessitano del supporto GPU. È necessario usare l'immagine GPU in un servizio di Azure. Ad esempio, istanze di contenitore di Azure, Azure Machine Learning calcolo, macchine virtuali di Azure e servizio Azure Kubernetes. Le versioni supportate sono 9,0, 9,1 e 10,0. Se `enable_gpu` è impostato, il valore predefinito è 9,1. Se `environment` è impostata, questa voce viene ignorata. |
| `description` | `description` | Descrizione dell'immagine. Se `environment` è impostata, questa voce viene ignorata.  |
| `environment` | `environment` | Facoltativa.  [Ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning.|

Il codice JSON seguente è una configurazione di esempio di inferenza da usare con l'interfaccia della riga di comando:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

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

Il comando seguente illustra come distribuire un modello usando il file di configurazione dell'inferenza precedente (denominato myInferenceConfig. Json). 

USA inoltre la versione più recente di un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning esistente (denominato AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
