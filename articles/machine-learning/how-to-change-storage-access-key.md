---
title: Modificare le chiavi di accesso dell'account di archiviazioneChange storage account access keys
titleSuffix: Azure Machine Learning
description: Informazioni su come modificare le chiavi di accesso per l'account di archiviazione di Azure usato dall'area di lavoro. Azure Machine Learning usa un account di archiviazione di Azure per archiviare dati e modelli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296924"
---
# <a name="regenerate-storage-account-access-keys"></a>Rigenerare le chiavi di accesso dell'account di archiviazioneRegenerate storage account access keys
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Informazioni su come modificare le chiavi di accesso per gli account di Archiviazione di Azure usati da Azure Machine Learning.Learn how to change the access keys for Azure Storage accounts used by Azure Machine Learning. Azure Machine Learning può usare gli account di archiviazione per archiviare i dati o i modelli con training.

Per motivi di sicurezza, potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure.For security purposes, you may need to change the access keys for an Azure Storage account. Quando si rigenera la chiave di accesso, Azure Machine Learning deve essere aggiornato per usare la nuova chiave. Azure Machine Learning può usare l'account di archiviazione sia per l'archiviazione del modello che come archivio dati.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere l'articolo [Creare un'area di lavoro.](how-to-manage-workspace.md)

* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [L'estensione dell'interfaccia della riga](reference-azure-machine-learning-cli.md)di comando di Azure Machine Learning .

> [!NOTE]
> I frammenti di codice in questo documento sono stati testati con la versione 1.0.83 di Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Cosa deve essere aggiornato

Gli account di archiviazione possono essere usati dall'area di lavoro di Azure Machine Learning (archiviazione di log, modelli, snapshot e così via) e come archivio dati. Il processo di aggiornamento dell'area di lavoro è un singolo comando dell'interfaccia della riga di comando di Azure e può essere eseguito dopo l'aggiornamento della chiave di archiviazione. Il processo di aggiornamento degli archivi dati è più complesso e richiede l'individuazione degli archivi dati che attualmente usano l'account di archiviazione e quindi li registrano nuovamente.

> [!IMPORTANT]
> Aggiornare contemporaneamente l'area di lavoro usando l'interfaccia della riga di comando di Azure e gli archivi dati usando Python.Update the workspace using the Azure CLI, and the datastores using Python, at the same time. L'aggiornamento solo dell'uno o dell'altro non è sufficiente e può causare errori fino a quando entrambi non vengono aggiornati.

Per individuare gli account di archiviazione usati dai datastore, usare il codice seguente:To discover the storage accounts that are used by your datastores, use the following code:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Questo codice cerca tutti gli archivi dati registrati che usano Archiviazione di Azure ed elenca le informazioni seguenti:This code looks for any registered datastores that use Azure Storage and lists the following information:

* Nome archivio dati: nome dell'archivio dati in cui è registrato l'account di archiviazione.
* Nome dell'account di archiviazione: il nome dell'account di archiviazione di Azure.Storage account name: The name of the Azure Storage account.
* Contenitore: contenitore nell'account di archiviazione utilizzato da questa registrazione.

Indica inoltre se l'archivio dati è per un BLOB di Azure o una condivisione File di Azure, in quanto esistono diversi metodi per registrare nuovamente ogni tipo di archivio dati.

Se esiste una voce per l'account di archiviazione per cui si prevede di rigenerare le chiavi di accesso, salvare il nome dell'archivio dati, il nome dell'account di archiviazione e il nome del contenitore.

## <a name="update-the-access-key"></a>Aggiornare il tasto di scelta

Per aggiornare Azure Machine Learning per usare la nuova chiave, usare la procedura seguente:To update Azure Machine Learning to use the new key, use the following steps:

> [!IMPORTANT]
> Eseguire tutti i passaggi, aggiornando sia l'area di lavoro utilizzando l'interfaccia della riga di comando che gli archivi dati utilizzando Python. L'aggiornamento solo dell'uno o dell'altro può causare errori fino a quando entrambi non vengono aggiornati.

1. Rigenerare la chiave. Per informazioni sulla rigenerazione di una chiave di accesso, vedere Gestire le chiavi di [accesso dell'account di archiviazione.](../storage/common/storage-account-keys-manage.md) Salvare la nuova chiave.

1. Per aggiornare l'area di lavoro in modo che utilizzi la nuova chiave, attenersi alla seguente procedura:

    1. Per accedere alla sottoscrizione di Azure che contiene l'area di lavoro usando il comando dell'interfaccia della riga di comando di Azure seguente:To sign in to the Azure subscription that contains your workspace by using the following Azure CLI command:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Per aggiornare l'area di lavoro in modo che utilizzi la nuova chiave, utilizzare il comando seguente. Sostituire `myworkspace` con il nome dell'area `myresourcegroup` di lavoro di Azure Machine Learning e sostituire con il nome del gruppo di risorse di Azure che contiene l'area di lavoro.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Questo comando sincronizza automaticamente le nuove chiavi per l'account di archiviazione di Azure usato dall'area di lavoro.

1. Per registrare nuovamente gli archivi dati che usano l'account di archiviazione, usare i valori della sezione Che cosa deve essere aggiornato e la chiave del passaggio 1 con il codice seguente:To re-register datastore(s) that use the storage account, use the values from the [What needs to be updated](#whattoupdate) section and the key from step 1 with the following code:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Poiché `overwrite=True` viene specificato, questo codice sovrascrive la registrazione esistente e la aggiorna per utilizzare la nuova chiave.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla registrazione degli [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) archivi dati, vedere le informazioni di riferimento sulla classe.
