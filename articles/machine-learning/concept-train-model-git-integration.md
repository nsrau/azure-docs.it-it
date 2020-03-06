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
ms.openlocfilehash: 10e4ba16e00a37d532a2eceb69fedb8f5b62be8b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301659"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrazione git per Azure Machine Learning

[Git](https://git-scm.com/) è un sistema di controllo della versione noto che consente di condividere e collaborare con i progetti. 

Azure Machine Learning supporta completamente i repository Git per tenere traccia del lavoro: è possibile clonare i repository direttamente nell'area di lavoro condivisa file system, usare git nella workstation locale o usare git da una pipeline CI/CD.

Quando si invia un processo a Azure Machine Learning, se i file di origine sono archiviati in un repository git locale, le informazioni sul repository vengono registrate come parte del processo di training.

Poiché Azure Machine Learning tiene traccia delle informazioni da un repository git locale, non è associato ad alcun repository centrale specifico. Il repository può essere clonato da GitHub, GitLab, Bitbucket, Azure DevOps o da qualsiasi altro servizio compatibile con git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clonare i repository git nell'area di lavoro file system
Azure Machine Learning fornisce un file system condiviso per tutti gli utenti nell'area di lavoro.
Per clonare un repository git in questa condivisione file, è consigliabile creare un'istanza di calcolo & aprire un terminale.
Una volta aperto il terminale, è possibile accedere a un client Git completo ed è possibile clonare e usare git usando l'interfaccia della riga di comando di git.

Si consiglia di clonare il repository nella directory degli utenti in modo che altri non effettuino conflitti direttamente nel ramo di lavoro.

È possibile clonare qualsiasi repository git a cui è possibile eseguire l'autenticazione (GitHub, Azure Repos, BitBucket e così via)

Per una guida su come usare l'interfaccia della riga di comando di Git, vedere [qui.](https://guides.github.com/introduction/git-handbook/)

## <a name="track-code-that-comes-from-git-repositories"></a>Tenere traccia del codice che deriva da repository git

Quando si invia un'esecuzione di training da Python SDK o Machine Learning CLI, i file necessari per il training del modello vengono caricati nell'area di lavoro. Se il `git` comando è disponibile nell'ambiente di sviluppo, il processo di caricamento lo usa per verificare se i file sono archiviati in un repository git. In tal caso, vengono caricate anche le informazioni del repository Git come parte dell'esecuzione del training. Queste informazioni vengono archiviate nelle proprietà seguenti per l'esecuzione del training:

| Proprietà | Comando git usato per ottenere il valore | Descrizione |
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

> [!TIP]
> Per verificare se il comando git è disponibile nell'ambiente di sviluppo, aprire una sessione della shell, un prompt dei comandi, PowerShell o un'altra interfaccia della riga di comando e digitare il comando seguente:
>
> ```
> git --version
> ```
>
> Se installato e nel percorso, si riceve una risposta simile a `git version 2.4.1`. Per ulteriori informazioni sull'installazione di git nell'ambiente di sviluppo, vedere il [sito Web git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visualizzare le informazioni registrate

Le informazioni git vengono archiviate nelle proprietà di un'esecuzione di training. È possibile visualizzare queste informazioni usando il portale di Azure, Python SDK e l'interfaccia della riga di comando. 

### <a name="azure-portal"></a>Portale di Azure

1. Dalla [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro.
1. Selezionare __esperimenti__, quindi selezionare uno degli esperimenti.
1. Selezionare una delle esecuzioni dalla colonna __numero di esecuzione__ .
1. Selezionare __logs__, quindi espandere i __registri__ e le voci __azureml__ . Selezionare il collegamento che inizia con __###\_Azure__.

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

### <a name="cli"></a>CLI

Il `az ml run` comando CLI può essere usato per recuperare le proprietà da un'esecuzione. Ad esempio, il comando seguente restituisce le proprietà dell'ultima esecuzione nell'esperimento denominata `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Per ulteriori informazioni, vedere la documentazione di riferimento [AZ ml Run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) .

## <a name="next-steps"></a>Passaggi successivi

* [Configurare e usare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md)
