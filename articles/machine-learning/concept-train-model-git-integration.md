---
title: Integrazione Git per Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning si integra con un repository Git locale. Quando si invia un'esecuzione di training da una directory locale che è un repository Git, le informazioni su repo, branch e commit corrente vengono registrate come parte dell'esecuzione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402829"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integrazione Git per Azure Machine Learning

[Git](https://git-scm.com/) è un sistema di controllo della versione popolare che consente di condividere e collaborare ai progetti. 

Azure Machine Learning supporta completamente i repository Git per il rilevamento del lavoro: è possibile clonare i repository direttamente nel file system dell'area di lavoro condivisa, usare Git nella workstation locale o usare Git da una pipeline CI/CD.

Quando si invia un processo ad Azure Machine Learning, se i file di origine vengono archiviati in un repository git locale, le informazioni sul repository vengono registrate come parte del processo di formazione.

Poiché Azure Machine Learning tiene traccia delle informazioni da un repository git locale, non è legato ad alcun repository centrale specifico. Il repository può essere clonato da GitHub, GitLab, Bitbucket, Azure DevOps o qualsiasi altro servizio compatibile con git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clonare i repository Git nel file system dell'area di lavoroClone Git repositories into your workspace file system
Azure Machine Learning offre un file system condiviso per tutti gli utenti nell'area di lavoro.
Per clonare un repository Git in questa condivisione file, è consigliabile creare un'istanza di calcolo & aprire un terminale.
Una volta aperto il terminale, si ha accesso a un client Git completo e si può clonare e lavorare con Git tramite l'esperienza Git CLI.

È consigliabile clonare il repository nella directory degli utenti in modo che altri utenti non compiano conflitti direttamente nel ramo di lavoro.

È possibile clonare qualsiasi repository Git a cui è possibile eseguire l'autenticazione (GitHub, Azure Repos, BitBucket e così via).

Per una guida su come utilizzare l'interfaccia della riga di comando Git, leggere [qui](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Tenere traccia del codice proveniente dai repository GitTrack code that comes from Git repositories

Quando si invia un'esecuzione di formazione da Python SDK o dall'interfaccia della riga di comando di Machine Learning, i file necessari per eseguire il training del modello vengono caricati nell'area di lavoro. Se `git` il comando è disponibile nell'ambiente di sviluppo, il processo di caricamento lo utilizza per verificare se i file sono archiviati in un repository git. In tal caso, anche le informazioni dal repository git vengono caricate come parte dell'esecuzione del training. Queste informazioni vengono archiviate nelle seguenti proprietà per l'esecuzione del training:

| Proprietà | Comando Git utilizzato per ottenere il valore | Descrizione |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | URI da cui è stato clonato il repository. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Il ramo attivo al momento dell'invio dell'esecuzione. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Il ramo attivo al momento dell'invio dell'esecuzione. |
| `azureml.git.commit` | `git rev-parse HEAD` | Hash di commit del codice inviato per l'esecuzione. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Hash di commit del codice inviato per l'esecuzione. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se il ramo/commit è dirty; in `false`caso contrario, . |

Queste informazioni vengono inviate per le esecuzioni che usano uno stimatore, una pipeline di Machine Learning o un'esecuzione di script.

Se i file di training non si trovano in `git` un repository git nell'ambiente di sviluppo o il comando non è disponibile, non viene tenuta traccia di alcuna informazione relativa a git.

> [!TIP]
> Per verificare se il comando git è disponibile nell'ambiente di sviluppo, aprire una sessione della shell, un prompt dei comandi, PowerShell o un'altra interfaccia della riga di comando e digitare il comando seguente:
>
> ```
> git --version
> ```
>
> Se installato, e nel percorso, si `git version 2.4.1`riceve una risposta simile a . Per ulteriori informazioni sull'installazione di git nell'ambiente di sviluppo, vedere il [sito Web Git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visualizzare le informazioni registrate

Le informazioni git vengono archiviate nelle proprietà per un'esecuzione di formazione. È possibile visualizzare queste informazioni usando il portale di Azure, Python SDK e CLI. 

### <a name="azure-portal"></a>Portale di Azure

1. Dal [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro.
1. Seleziona __Esperimenti__, quindi seleziona uno dei tuoi esperimenti.
1. Selezionare una delle condutture dalla colonna __RUN NUMBER.__
1. Selezionare __Registri__, quindi espandere __i log__ e le voci __di azureml__ . Selezionare il collegamento __ ### \___ che inizia con azure .

    ![La voce #_azure nel portale](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

The logged information contains text similar to the following JSON:

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

Dopo aver inviato un'esecuzione di training, viene restituito un oggetto [Run.After](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) submitting a training run, a Run object is returned. L'attributo `properties` di questo oggetto contiene le informazioni git registrate. Ad esempio, il codice seguente recupera l'hash di commit:For example, the following code retrieves the commit hash:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

Il `az ml run` comando CLI può essere utilizzato per recuperare le proprietà da un'esecuzione. Ad esempio, il comando seguente restituisce le proprietà `train-on-amlcompute`per l'ultima esecuzione dell'esperimento denominato :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Per altre informazioni, vedere la documentazione di riferimento [sull'esecuzione di az ml.For](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) more information, see the az ml run reference documentation.

## <a name="next-steps"></a>Passaggi successivi

* [Impostare e usare gli obiettivi di calcolo per il training del modelloSet up and use compute targets for model training](how-to-set-up-training-targets.md)
