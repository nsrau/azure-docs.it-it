---
title: Modificare le chiavi di accesso account di archiviazione
titleSuffix: Azure Machine Learning service
description: Informazioni su come modificare le chiavi di accesso per l'account di archiviazione di Azure usato per l'area di lavoro. Servizio Azure Machine Learning Usa un account di archiviazione di Azure per archiviare i dati e modelli. Quando si rigenera la chiave di accesso dell'account di archiviazione, è necessario aggiornare il servizio di Azure Machine Learning per usare le nuove chiavi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543838"
---
# <a name="regenerate-storage-account-access-keys"></a>Rigenera chiavi di accesso di account di archiviazione

Informazioni su come modificare le chiavi di accesso per gli account di archiviazione di Azure usati dal servizio Azure Machine Learning. Azure Machine Learning è possibile usare gli account di archiviazione per archiviare i dati o modelli sottoposti a training.

Per motivi di sicurezza, si potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure. Quando si rigenera la chiave di accesso, Azure Machine Learning deve essere aggiornato per usare la nuova chiave. Azure Machine Learning può essere l'account di archiviazione per entrambi archiviazione del modello e come un archivio dati.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere la [creare un'area di lavoro](setup-create-workspace.md) articolo.

* Il [di Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Il [estensione della riga di comando di Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Che cosa deve essere aggiornato

Gli account di archiviazione possono essere usati nell'area di lavoro del servizio Azure Machine Learning (l'archiviazione dei log, i modelli, gli snapshot, e così via) e come un archivio dati. Il processo per aggiornare l'area di lavoro è un singolo comando di Azure e può essere eseguito dopo l'aggiornamento della chiave di archiviazione. Il processo di aggiornamento di archivi dati è più complesso e richiede l'individuazione dei quali archivi di dati attualmente in uso l'account di archiviazione e quindi nuovamente la relativa registrazione.

> [!IMPORTANT]
> Aggiornare l'area di lavoro tramite la CLI di Azure e gli archivi dati con Python, nello stesso momento. L'aggiornamento solo uno o l'altro non è sufficiente e può causare errori fino a quando non vengono entrambe aggiornate.

Per individuare gli account di archiviazione usati da archivi di dati, usare il codice seguente:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

Questo codice è simile per tutti gli archivi dati registrati che usano l'archiviazione di Azure ed elenca le informazioni seguenti:

* Nome archivio dati: Il nome dell'archivio dati registrato con l'account di archiviazione.
* Nome account di archiviazione: Il nome dell'account di archiviazione di Azure.
* Contenitore: Il contenitore nell'account di archiviazione usato da questa registrazione.

Se esiste una voce per l'account di archiviazione che si prevede di rigenerazione delle chiavi di accesso per, salvare il nome di archivio dati, nome account di archiviazione e nome del contenitore.

## <a name="update-the-access-key"></a>Aggiornare la chiave di accesso

Per aggiornare il servizio di Azure Machine Learning per usare la nuova chiave, seguire questa procedura:

> [!IMPORTANT]
> Eseguire tutti i passaggi, l'aggiornamento sia l'area di lavoro usando il comando e gli archivi dati usando Python. L'aggiornamento solo uno o l'altro può causare errori fino a quando non vengono entrambe aggiornate.

1. Rigenerare la chiave. Per informazioni sulla rigenerazione di una chiave di accesso, vedere la [gestire un account di archiviazione](/azure/storage/common/storage-account-manage#access-keys) articolo. Salvare la nuova chiave.

1. Per aggiornare l'area di lavoro per usare la nuova chiave, usare la procedura seguente:

    1. Per accedere alla sottoscrizione di Azure che contiene l'area di lavoro usando il comando di Azure:

        ```azurecli-interactive
        az login
        ```

    1. Per installare l'estensione di Azure Machine Learning, usare il comando seguente:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Per aggiornare l'area di lavoro per usare la nuova chiave, usare il comando seguente. Sostituire `myworkspace` con il nome dell'area di lavoro di Azure Machine Learning e `myresourcegroup` con il nome del gruppo di risorse di Azure che contiene l'area di lavoro.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        Questo comando sincronizzerà automaticamente le nuove chiavi dell'account di archiviazione di Azure usato nell'area di lavoro.

1. Per registrare nuovamente archivi dati che utilizzano l'account di archiviazione, usare i valori di [ciò che deve essere aggiornato](#whattoupdate) sezione e la chiave dal passaggio 1 con il codice seguente:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Poiché `overwrite=True` viene specificato, questo codice sovrascrive la registrazione esistente e aggiorna in modo che usi la nuova chiave.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come registrare gli archivi dati, vedere la [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) di riferimento sulla classe.
