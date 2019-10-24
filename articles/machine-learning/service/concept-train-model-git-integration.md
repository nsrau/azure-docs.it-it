---
title: Integrazione git per Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning si integra con un repository git locale. Quando si invia un'esecuzione di training da una directory locale che è un repository git, le informazioni sul repository, il ramo e il commit corrente vengono rilevate come parte dell'esecuzione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: c8b2407b18f0d7115ce51fc28b956e7fd764c71e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756006"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrazione git per Azure Machine Learning

[Git](https://git-scm.com/) è un sistema di controllo della versione noto che consente di condividere e collaborare con i progetti. Quando si invia un processo di training a Azure Machine Learning, se i file di training sono archiviati in un repository git locale, le informazioni sul repository vengono registrate come parte del processo di training.

Poiché Azure Machine Learning tiene traccia delle informazioni da un repository git locale, non è associato ad alcun repository centrale specifico. Il repository può essere clonato da GitHub, GitLab, Bitbucket, Azure DevOps o da qualsiasi altro servizio compatibile con git.

## <a name="how-does-git-integration-work"></a>Funzionamento dell'integrazione con git

Quando si invia un'esecuzione di training da Python SDK o Machine Learning CLI, i file necessari per il training del modello vengono caricati nell'area di lavoro. Se il comando `git` è disponibile nell'ambiente di sviluppo, il processo di caricamento lo usa per verificare se i file sono archiviati in un repository git. In tal caso, vengono caricate anche le informazioni del repository Git come parte dell'esecuzione del training. Queste informazioni vengono archiviate nelle proprietà seguenti per l'esecuzione del training:

| Proprietà | Comando git usato per ottenere il valore | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Ramo attivo al momento dell'invio dell'esecuzione. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hash di commit del codice che è stato inviato per l'esecuzione. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hash di commit del codice che è stato inviato per l'esecuzione. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se il ramo/commit è modificato; in caso contrario, `false`. |

Queste informazioni vengono inviate per le esecuzioni che usano un estimatore, una pipeline di Machine Learning o un'esecuzione di script.

Se i file di training non si trovano in un repository git nell'ambiente di sviluppo oppure il comando `git` non è disponibile, non viene tenuta traccia delle informazioni relative a git.

## <a name="view-the-logged-information"></a>Visualizzare le informazioni registrate

Le informazioni git vengono archiviate nelle proprietà di un'esecuzione di training. È possibile visualizzare queste informazioni usando il portale di Azure, Python SDK e l'interfaccia della riga di comando. 

### <a name="azure-portal"></a>Portale di Azure

1. Dalla [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro.
1. Selezionare __esperimenti__, quindi selezionare uno degli esperimenti.
1. Selezionare una delle esecuzioni dalla colonna __numero di esecuzione__ .
1. Selezionare __logs__, quindi espandere i __registri__ e le voci __azureml__ . Selezionare il collegamento che inizia con __### \_azure__.

    ![La voce # # #_azure nel portale](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

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

* [Configurare e usare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md)