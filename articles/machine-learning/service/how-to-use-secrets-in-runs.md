---
title: Usare i segreti nelle esecuzioni di training
titleSuffix: Azure Machine Learning service
description: Passare i segreti alle esecuzioni di training in modo sicuro usando l'area di lavoro Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 777818b74f935a560047d1eea20e0d0e657efcac
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019420"
---
# <a name="use-secrets-in-training-runs"></a>Usare i segreti nelle esecuzioni di training

In questo articolo si apprenderà come usare i segreti nelle esecuzioni di training in modo sicuro. Ad esempio, per connettersi a un database esterno per eseguire query sui dati di training, è necessario passare nome utente e password al contesto di esecuzione remota. Il codice di questi valori negli script di training in testo non crittografato non è sicuro perché esporrà il segreto. 

Al contrario, il area di lavoro di Azure Machine Learning ha [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) come risorsa associata. Questa Key Vault può essere usata per passare i segreti alle esecuzioni remote in modo sicuro tramite un set di API in Azure Machine Learning Python SDK

Il flusso di base per l'uso dei segreti è:
 1. Nel computer locale accedere ad Azure e connettersi all'area di lavoro
 2. Nel computer locale impostare un segreto nell'area di lavoro Key Vault
 3. Inviare un'esecuzione remota.
 4. All'interno dell'esecuzione remota, ottenere il segreto dal valore della chiave e usarlo.

## <a name="set-secrets"></a>Imposta segreti

In Azure Machine Learning Python SDK, la classe dell'insieme di credenziali delle chiavi contiene metodi per l' [impostazione dei segreti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) . Nella sessione Python locale ottenere prima di tutto un riferimento all'area di lavoro Key Vault, quindi usare il metodo [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) per impostare un segreto in base al nome e al valore.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Non inserire il valore Secret nel codice Python perché non è sicuro archiviarlo nel file come testo non crittografato. Ottenere invece il valore del segreto dalla variabile di ambiente, ad esempio il segreto di compilazione di Azure DevOps o dall'input utente interattivo.

È possibile elencare i nomi dei segreti usando il metodo [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) . Il metodo __set_secret__ aggiorna il valore Secret se il nome esiste già.

## <a name="get-secrets"></a>Get secrets

Nel codice locale è possibile usare il metodo [Vault. Get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) per ottenere il valore Secret in base al nome.

In esecuzioni inviate utilizzando [Experiment. Submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-), utilizzare il metodo [Run. Get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) . Poiché l'esecuzione inviata è a conoscenza della relativa area di lavoro, questo metodo abbrevia la creazione dell'area di lavoro e restituisce direttamente il valore del segreto.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Prestare attenzione a non esporre il valore del segreto scrivendo o stamparlo.

I metodi set e Get hanno anche le versioni batch [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) e [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) per l'accesso a più segreti contemporaneamente.

## <a name="next-steps"></a>Passaggi successivi

 * [Visualizza notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Informazioni sulla sicurezza aziendale con Azure Machine Learning Service](concept-enterprise-security.md)
