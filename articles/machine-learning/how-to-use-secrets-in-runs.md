---
title: Usare i segreti nelle esecuzioni di allenamentoUse secrets in training runs
titleSuffix: Azure Machine Learning
description: Passare segreti alle corse di formazione in modo sicuro utilizzando Workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942266"
---
# <a name="use-secrets-in-training-runs"></a>Usare i segreti nelle esecuzioni di allenamentoUse secrets in training runs
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo si apprenderà come usare i segreti nel training in modo sicuro. Le informazioni di autenticazione, ad esempio il nome utente e la password, sono segreti. Ad esempio, se ci si connette a un database esterno per eseguire query sui dati di training, è necessario passare il nome utente e la password al contesto di esecuzione remota. La codifica di tali valori negli script di training in testo non crittografato non è sicura in quanto esporrebbe il segreto. 

Al contrario, all'area di lavoro di Azure Machine Learning è associata una risorsa denominata insieme di credenziali delle chiavi di [Azure.](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Usare questo insieme di credenziali delle chiavi per passare segreti alle esecuzioni remote in modo sicuro tramite un set di API in Azure Machine Learning Python SDK.

Il flusso di base per l'utilizzo dei segreti è:The basic flow for using secrets is:
 1. Nel computer locale accedere ad Azure e connettersi all'area di lavoro.
 2. Nel computer locale impostare un segreto nell'insieme di credenziali delle chiavi dell'area di lavoro.
 3. Inviare un'esecuzione remota.
 4. All'interno dell'esecuzione remota, ottenere il segreto da Key Vault e utilizzarlo.

## <a name="set-secrets"></a>Impostare i segreti

In Azure Machine Learning la classe [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) contiene metodi per l'impostazione dei segreti. Nella sessione Python locale, ottenere innanzitutto un riferimento all'insieme di credenziali delle chiavi dell'area di lavoro, quindi utilizzare il [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) metodo per impostare un segreto in base al nome e al valore. Il __metodo set_secret__ aggiorna il valore segreto se il nome esiste già.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Non inserire il valore segreto nel codice Python in quanto non è sicuro per archiviarlo in un file come testo non crittografato. Ottenere invece il valore segreto da una variabile di ambiente, ad esempio il segreto di compilazione DevOps di Azure o l'input dell'utente interattivo.

È possibile elencare [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) i nomi dei segreti utilizzando il metodo e c'è anche una versione batch,[set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) che consente di impostare più segreti alla volta.

## <a name="get-secrets"></a>Get secrets

Nel codice locale è possibile[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) usare il metodo per ottenere il valore segreto in base al nome.

Per le [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) esecuzioni [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) inviate il [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) metodo , utilizzare il metodo con la classe . Poiché un'esecuzione inviata è a conoscenza della relativa area di lavoro, questo metodo esegue il collegamento della creazione di istanze dell'area di lavoro e restituisce direttamente il valore segreto.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Fare attenzione a non esporre il valore segreto scrivendolo o stampandolo.

C'è anche una versione batch, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) per accedere a più segreti contemporaneamente.

## <a name="next-steps"></a>Passaggi successivi

 * [Visualizzare un blocco appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Informazioni sulla sicurezza aziendale con Azure Machine Learning](concept-enterprise-security.md)
