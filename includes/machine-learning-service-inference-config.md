---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348551"
---
Le voci nel `inferenceconfig.json` documento vengono mappate ai parametri per la classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Descrizione |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Percorso del file locale che contiene il codice da eseguire per l'immagine. |
| `runtime` | `runtime` | Runtime da usare per l'immagine. I runtime supportati correnti sono "Spark-py" e "Python". |
| `condaFile` | `conda_file` | facoltativo. Percorso del file locale contenente una definizione di ambiente conda da usare per l'immagine. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | facoltativo. Percorso del file locale contenente i passaggi aggiuntivi di Docker da eseguire durante la configurazione dell'immagine. |
| `sourceDirectory` | `source_directory` | facoltativo. Percorso delle cartelle che contengono tutti i file per la creazione dell'immagine. |
| `enableGpu` | `enable_gpu` | facoltativo. Indica se abilitare il supporto GPU nell'immagine. È necessario usare l'immagine GPU nel servizio Microsoft Azure. Ad esempio, istanze di contenitore di Azure, Azure Machine Learning calcolo, macchine virtuali di Azure e servizio Azure Kubernetes. Il valore predefinito è false. |
| `baseImage` | `base_image` | facoltativo. Immagine personalizzata da usare come immagine di base. Se non viene specificata alcuna immagine di base, l'immagine di base verrà usata in base al parametro di runtime specificato. |
| `baseImageRegistry` | `base_image_registry` | facoltativo. Registro immagini che contiene l'immagine di base. |
| `cudaVersion` | `cuda_version` | facoltativo. Versione di CUDA da installare per le immagini che necessitano del supporto GPU. È necessario usare l'immagine GPU nei servizi Microsoft Azure. Ad esempio, istanze di contenitore di Azure, Azure Machine Learning calcolo, macchine virtuali di Azure e servizio Azure Kubernetes. Le versioni supportate sono 9,0, 9,1 e 10,0. Se è impostato ' enable_gpu ', il valore predefinito è' 9,1'. |
| `description` | `description` |  Descrizione dell'immagine. |

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