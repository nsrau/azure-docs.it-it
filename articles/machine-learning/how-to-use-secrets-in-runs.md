---
title: Segreti di autenticazione nel training
titleSuffix: Azure Machine Learning
description: Passare i segreti alle esecuzioni di training in modo sicuro usando l'area di lavoro Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 11bcfa21b1a12df945a0dec6dc291c912a0e99c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047893"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Usare i segreti delle credenziali di autenticazione nelle esecuzioni di training Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo si apprenderà come usare i segreti nelle esecuzioni di training in modo sicuro. Le informazioni di autenticazione, ad esempio il nome utente e la password sono segreti. Se ad esempio ci si connette a un database esterno per eseguire query sui dati di training, è necessario passare il nome utente e la password al contesto di esecuzione remota. Il codice di questi valori negli script di training in testo non crittografato non è sicuro perché esporrà il segreto. 

Al contrario, l'area di lavoro Azure Machine Learning dispone di una risorsa associata denominata [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Usare questa Key Vault per passare i segreti alle esecuzioni remote in modo sicuro tramite un set di API in Azure Machine Learning Python SDK.

Il flusso di base per l'uso dei segreti è:
 1. Nel computer locale accedere ad Azure e connettersi all'area di lavoro.
 2. Nel computer locale impostare un segreto nell'area di lavoro Key Vault.
 3. Inviare un'esecuzione remota.
 4. All'interno dell'esecuzione remota, ottenere il segreto da Key Vault e usarlo.

## <a name="set-secrets"></a>Imposta segreti

Nella Azure Machine Learning la classe dell'insieme di credenziali delle chiavi contiene metodi [per l'impostazione](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) dei segreti. Nella sessione Python locale ottenere prima di tutto un riferimento all'area di lavoro Key Vault, quindi usare il [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) metodo per impostare un segreto in base al nome e al valore. Il metodo __set_secret__ aggiorna il valore del segreto se il nome esiste già.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Non inserire il valore Secret nel codice Python perché non è sicuro archiviarlo nel file come testo non crittografato. Ottenere invece il valore del segreto da una variabile di ambiente, ad esempio il segreto di compilazione di Azure DevOps o dall'input utente interattivo.

È possibile elencare i nomi dei segreti usando il [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) Metodo ed esiste anche una versione batch,[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) che consente di impostare più segreti alla volta.

## <a name="get-secrets"></a>Get secrets

Nel codice locale è possibile usare il [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) metodo per ottenere il valore del segreto in base al nome.

Per le esecuzioni inviate [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) , usare il [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) metodo con la [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) classe. Poiché un'esecuzione inviata è a conoscenza della relativa area di lavoro, questo metodo abbrevia la creazione dell'area di lavoro e restituisce direttamente il valore del segreto.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Prestare attenzione a non esporre il valore del segreto scrivendo o stamparlo.

È disponibile anche una versione batch, [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) per l'accesso a più segreti contemporaneamente.

## <a name="next-steps"></a>Passaggi successivi

 * [Visualizza notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Informazioni sulla sicurezza aziendale con Azure Machine Learning](concept-enterprise-security.md)
