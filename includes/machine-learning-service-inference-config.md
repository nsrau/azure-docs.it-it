---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 055909d51fcd1228e8eb26189ba682e09aee6a1a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390610"
---
Le voci nel `inferenceconfig.json` documento vengono mappate ai parametri per la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Descrizione |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Percorso di un file locale che contiene il codice da eseguire per l'immagine. |
| `runtime` | `runtime` | Runtime da usare per l'immagine. I runtime supportati correnti sono `spark-py` e `python`. |
| `condaFile` | `conda_file` | facoltativo. Percorso di un file locale che contiene una definizione di ambiente conda da usare per l'immagine. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | facoltativo. Percorso di un file locale che contiene passaggi aggiuntivi di Docker da eseguire durante la configurazione dell'immagine. |
| `sourceDirectory` | `source_directory` | facoltativo. Percorso delle cartelle che contengono tutti i file per la creazione dell'immagine. |
| `enableGpu` | `enable_gpu` | facoltativo. Indica se abilitare il supporto GPU nell'immagine. È necessario usare l'immagine GPU in un servizio di Azure, ad esempio istanze di contenitore di Azure, Azure Machine Learning calcolo, macchine virtuali di Azure e il servizio Azure Kubernetes. Il valore predefinito è false. |
| `baseImage` | `base_image` | facoltativo. Immagine personalizzata da usare come immagine di base. Se non viene specificata alcuna immagine di base, l'immagine sarà basata sul parametro di runtime specificato. |
| `baseImageRegistry` | `base_image_registry` | facoltativo. Registro immagini che contiene l'immagine di base. |
| `cudaVersion` | `cuda_version` | facoltativo. Versione di CUDA da installare per le immagini che necessitano del supporto GPU. È necessario usare l'immagine GPU in un servizio di Azure, ad esempio istanze di contenitore di Azure, Azure Machine Learning calcolo, macchine virtuali di Azure e il servizio Azure Kubernetes. Le versioni supportate sono 9,0, 9,1 e 10,0. Se `enable_gpu` è impostato, il valore predefinito è 9,1. |
| `description` | `description` | Descrizione dell'immagine. |

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