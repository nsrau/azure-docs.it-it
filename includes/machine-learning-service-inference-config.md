---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926931"
---
Le voci del documento `inferenceconfig.json` mappano i parametri per la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Percorso di un file locale che contiene il codice da eseguire per l'immagine. |
| `runtime` | `runtime` | facoltativo. Runtime da usare per l'immagine. I runtime supportati correnti sono `spark-py` e `python`. Se `environment` è impostato, questo viene ignorato. |
| `condaFile` | `conda_file` | facoltativo. Percorso di un file locale che contiene una definizione di ambiente conda da usare per l'immagine.  Se `environment` è impostato, questo viene ignorato. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | facoltativo. Percorso di un file locale che contiene passaggi aggiuntivi di Docker da eseguire durante la configurazione dell'immagine.  Se `environment` è impostato, questo viene ignorato.|
| `sourceDirectory` | `source_directory` | facoltativo. Percorso delle cartelle che contengono tutti i file per la creazione dell'immagine. |
| `enableGpu` | `enable_gpu` | facoltativo. Indica se abilitare il supporto GPU nell'immagine. È necessario usare l'immagine GPU in un servizio di Azure, ad esempio istanze di contenitore di Azure, Azure Machine Learning calcolo, macchine virtuali di Azure e il servizio Azure Kubernetes. Il valore predefinito è False. Se `environment` è impostato, questo viene ignorato.|
| `baseImage` | `base_image` | facoltativo. Immagine personalizzata da usare come immagine di base. Se non viene specificata alcuna immagine di base, l'immagine sarà basata sul parametro di runtime specificato. Se `environment` è impostato, questo viene ignorato. |
| `baseImageRegistry` | `base_image_registry` | facoltativo. Registro immagini che contiene l'immagine di base. Se `environment` è impostato, questo viene ignorato.|
| `cudaVersion` | `cuda_version` | facoltativo. Versione di CUDA da installare per le immagini che necessitano del supporto GPU. È necessario usare l'immagine GPU in un servizio di Azure, ad esempio istanze di contenitore di Azure, Azure Machine Learning calcolo, macchine virtuali di Azure e il servizio Azure Kubernetes. Le versioni supportate sono 9,0, 9,1 e 10,0. Se `enable_gpu` è impostato, il valore predefinito è 9,1. Se `environment` è impostato, questo viene ignorato. |
| `description` | `description` | Descrizione dell'immagine. Se `environment` è impostato, questo viene ignorato.  |
| `environment` | `environment` | facoltativo.  [Ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)Azure Machine Learning.|

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

Il codice JSON seguente è una configurazione di inferenza di esempio che usa un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) di Azure Machine Learning esistente con una versione specifica da usare con l'interfaccia della riga di comando:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

Il codice JSON seguente è una configurazione di inferenza di esempio che usa un [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) Azure Machine Learning esistente con la versione più recente per l'uso con l'interfaccia della riga di comando

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
