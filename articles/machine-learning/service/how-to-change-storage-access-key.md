---
title: Modificare le chiavi di accesso dell'account di archiviazione
titleSuffix: Azure Machine Learning service
description: Informazioni su come modificare le chiavi di accesso per l'account di archiviazione di Azure usato dall'area di lavoro. Azure Machine Learning servizio usa un account di archiviazione di Azure per archiviare i dati e i modelli. Quando si rigenera la chiave di accesso per l'account di archiviazione, è necessario aggiornare il servizio Azure Machine Learning per usare le nuove chiavi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/16/2019
ms.openlocfilehash: e386e34a8326a51753631ee9ea4215d01ba7ceb3
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558219"
---
# <a name="regenerate-storage-account-access-keys"></a>Rigenera le chiavi di accesso dell'account di archiviazione

Informazioni su come modificare le chiavi di accesso per gli account di archiviazione di Azure usati dal servizio Azure Machine Learning. Azure Machine Learning possibile usare gli account di archiviazione per archiviare i dati o i modelli sottoposti a training.

Per motivi di sicurezza, potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure. Quando si rigenera la chiave di accesso, è necessario aggiornare Azure Machine Learning per l'uso della nuova chiave. Azure Machine Learning possibile che utilizzino l'account di archiviazione sia per l'archiviazione del modello sia come archivio dati.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](how-to-manage-workspace.md) .

* [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [Estensione dell'interfaccia](reference-azure-machine-learning-cli.md)della riga di comando Azure Machine Learning.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Cosa è necessario aggiornare

Gli account di archiviazione possono essere usati dall'area di lavoro del servizio Azure Machine Learning (archiviando log, modelli, snapshot e così via) e come archivio dati. Il processo di aggiornamento dell'area di lavoro è un singolo comando dell'interfaccia della riga di comando di Azure e può essere eseguito dopo l'aggiornamento della chiave di archiviazione. Il processo di aggiornamento degli archivi dati è più necessario ed è necessario individuare gli archivi dati che usano attualmente l'account di archiviazione e quindi registrarli di nuovo.

> [!IMPORTANT]
> Aggiornare l'area di lavoro usando l'interfaccia della riga di comando di Azure e gli archivi dati usando Python, allo stesso tempo. L'aggiornamento di un solo o l'altro non è sufficiente e può causare errori fino a quando non vengono aggiornati entrambi.

Per individuare gli account di archiviazione usati dagli archivi dati, usare il codice seguente:

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

Questo codice Cerca tutti gli archivi dati registrati che usano archiviazione di Azure e elenca le informazioni seguenti:

* Nome archivio dati: Nome dell'archivio dati in cui è registrato l'account di archiviazione.
* Nome account di archiviazione: Nome dell'account di archiviazione di Azure.
* Contenitore Il contenitore nell'account di archiviazione usato dalla registrazione.

Indica inoltre se l'archivio dati è per un BLOB di Azure o una condivisione file di Azure, in quanto esistono diversi metodi per registrare di nuovo ogni tipo di archivio dati.

Se esiste una voce per l'account di archiviazione per cui si prevede di rigenerare le chiavi di accesso, salvare il nome dell'archivio dati, il nome dell'account di archiviazione e il nome del contenitore.

## <a name="update-the-access-key"></a>Aggiornare la chiave di accesso

Per aggiornare Azure Machine Learning servizio per l'utilizzo della nuova chiave, attenersi alla procedura seguente:

> [!IMPORTANT]
> Eseguire tutti i passaggi, aggiornare l'area di lavoro usando l'interfaccia della riga di comando e gli archivi dati usando Python. L'aggiornamento di un solo o l'altro può causare errori fino a quando non vengono aggiornati entrambi.

1. Rigenerare la chiave. Per informazioni sulla rigenerazione di una chiave di accesso, vedere l'articolo [gestire un account di archiviazione](/azure/storage/common/storage-account-manage#access-keys) . Salvare la nuova chiave.

1. Per aggiornare l'area di lavoro in modo da usare la nuova chiave, seguire questa procedura:

    1. Per accedere alla sottoscrizione di Azure che contiene l'area di lavoro usando il comando dell'interfaccia della riga di comando di Azure seguente:

        ```azurecli-interactive
        az login
        ```

    1. Per aggiornare l'area di lavoro per l'utilizzo della nuova chiave, utilizzare il comando seguente. Sostituire `myworkspace` con il nome dell'area di lavoro Azure Machine Learning `myresourcegroup` e sostituire con il nome del gruppo di risorse di Azure che contiene l'area di lavoro.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

        Questo comando Sincronizza automaticamente le nuove chiavi per l'account di archiviazione di Azure usato dall'area di lavoro.

1. Per registrare di nuovo gli archivi dati che usano l'account di archiviazione, usare i valori della sezione [elementi che devono essere aggiornati](#whattoupdate) e la chiave del passaggio 1 con il codice seguente:

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

    Poiché `overwrite=True` è specificato, questo codice sovrascrive la registrazione esistente e la Aggiorna per l'uso della nuova chiave.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla registrazione di archivi dati, vedere il riferimento [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) alla classe.
