---
title: Integrazione git per Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning si integra con un repository git locale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377512"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrazione git per Azure Machine Learning

[Git](https://git-scm.com/) è un sistema di controllo della versione noto che consente di condividere e collaborare con i progetti. Quando si invia un processo di training a Azure Machine Learning, se i file di training sono archiviati in un repository git locale, le informazioni sul repository vengono registrate come parte del processo di training.

Poiché Azure Machine Learning tiene traccia delle informazioni da un repository git locale, non è associato ad alcun repository centrale specifico. Il repository può essere clonato da GitHub, GitLab, Bitbucket, Azure DevOps o da qualsiasi altro servizio compatibile con git.

## <a name="how-does-git-integration-work"></a>Funzionamento dell'integrazione con git

Quando si invia un'esecuzione di training da Python SDK o Machine Learning CLI, i file necessari per il training del modello vengono caricati nell'area di lavoro. Se il comando `git` è disponibile nell'ambiente di sviluppo, il processo di caricamento lo usa per verificare se i file sono archiviati in un repository git. In tal caso, vengono caricate anche le informazioni del repository Git come parte dell'esecuzione del training. Queste informazioni vengono archiviate nelle proprietà seguenti per l'esecuzione del training:

| Proprietà | Description |
| ----- | ----- |
| `azureml.git.repository_uri` | URI da cui è stato clonato il repository. |
| `mlflow.source.git.repoURL` | URI da cui è stato clonato il repository. |
| `azureml.git.branch` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `mlflow.source.git.branch` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `azureml.git.commit` | Hash di commit del codice che è stato inviato per l'esecuzione. |
| `mlflow.source.git.commit` | Hash di commit del codice che è stato inviato per l'esecuzione. |
| `azureml.git.dirty` | `True`, se il commit è modificato; in caso contrario, `false`. |

Queste informazioni vengono inviate per le esecuzioni che usano un estimatore, una pipeline di Machine Learning o un'esecuzione di script.

Se i file di training non si trovano in un repository git nell'ambiente di sviluppo oppure il comando `git` non è disponibile, non viene tenuta traccia delle informazioni relative a git.

## <a name="view-the-logged-information"></a>Visualizzare le informazioni registrate

Le informazioni git vengono archiviate nelle proprietà di un'esecuzione di training. È possibile visualizzare queste informazioni usando il portale di Azure, Python SDK e l'interfaccia della riga di comando. 

### <a name="azure-portal"></a>Portale di Azure

1. Dalla [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro.
1. Selezionare __esperimenti__, quindi selezionare uno degli esperimenti.
1. Selezionare una delle esecuzioni dalla colonna __numero di esecuzione__ .
1. Selezionare __logs__, quindi espandere i __registri__ e le voci __azureml__ . Selezionare il collegamento che inizia con __### @ no__t-2azure__.

Le informazioni registrate contengono testo simile al seguente JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

Dopo aver inviato un'esecuzione di training, viene restituito un oggetto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) . L'attributo `properties` di questo oggetto contiene le informazioni git registrate. Il codice seguente, ad esempio, recupera l'hash di commit:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Interfaccia della riga di comando

È possibile usare il comando dell'interfaccia della riga di comando `az ml run` per recuperare le proprietà da un'esecuzione. Ad esempio, il comando seguente restituisce le proprietà dell'ultima esecuzione nell'esperimento denominata `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Per ulteriori informazioni, vedere la documentazione di riferimento [AZ ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) .

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate su come eseguire il training con Azure Machine Learning in Visual Studio Code, vedere [esercitazione: eseguire il training di modelli con Azure Machine Learning](tutorial-train-models-with-aml.md).
* Per una procedura dettagliata su come modificare, eseguire ed eseguire il debug di codice localmente, vedere l'esercitazione relativa a [Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
