---
title: Modificare le chiavi di accesso dell'account di archiviazione
titleSuffix: Azure Machine Learning
description: Informazioni su come modificare le chiavi di accesso per l'account di archiviazione di Azure usato dall'area di lavoro. Azure Machine Learning usa un account di archiviazione di Azure per archiviare i dati e i modelli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: f1da7149a41ec8dd08e307394cba3e7feabec42a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320714"
---
# <a name="regenerate-storage-account-access-keys"></a>Rigenera le chiavi di accesso dell'account di archiviazione


Informazioni su come modificare le chiavi di accesso per gli account di archiviazione di Azure usati da Azure Machine Learning. Azure Machine Learning possibile usare gli account di archiviazione per archiviare i dati o i modelli sottoposti a training.

Per motivi di sicurezza, potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure. Quando si rigenera la chiave di accesso, è necessario aggiornare Azure Machine Learning per l'uso della nuova chiave. Azure Machine Learning possibile che utilizzino l'account di archiviazione sia per l'archiviazione del modello sia come archivio dati.

> [!IMPORTANT]
> Le credenziali registrate con gli archivi dati vengono salvate nel Azure Key Vault associato all'area di lavoro. Se l' [eliminazione](../key-vault/general/soft-delete-overview.md) temporanea è abilitata per l'Key Vault, assicurarsi di seguire questo articolo per l'aggiornamento delle credenziali. L'annullamento della registrazione dell'archivio dati e la nuova registrazione con lo stesso nome avranno esito negativo.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](how-to-manage-workspace.md) .

* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

* [Estensione dell'interfaccia](reference-azure-machine-learning-cli.md)della riga di comando Azure Machine Learning.

> [!NOTE]
> I frammenti di codice in questo documento sono stati testati con la versione 1.0.83 di Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Cosa è necessario aggiornare

Gli account di archiviazione possono essere usati dall'area di lavoro Azure Machine Learning (archiviando log, modelli, snapshot e così via) e come archivio dati. Il processo di aggiornamento dell'area di lavoro è un singolo comando dell'interfaccia della riga di comando di Azure e può essere eseguito dopo l'aggiornamento della chiave di archiviazione. Il processo di aggiornamento degli archivi dati è più necessario ed è necessario individuare gli archivi dati che usano attualmente l'account di archiviazione e quindi registrarli di nuovo.

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

* Nome archivio dati: il nome dell'archivio dati in cui è registrato l'account di archiviazione.
* Nome dell'account di archiviazione: il nome dell'account di archiviazione di Azure.
* Container: contenitore nell'account di archiviazione usato da questa registrazione.

Indica inoltre se l'archivio dati è per un BLOB di Azure o una condivisione file di Azure, in quanto esistono diversi metodi per registrare di nuovo ogni tipo di archivio dati.

Se esiste una voce per l'account di archiviazione per cui si prevede di rigenerare le chiavi di accesso, salvare il nome dell'archivio dati, il nome dell'account di archiviazione e il nome del contenitore.

## <a name="update-the-access-key"></a>Aggiornare la chiave di accesso

Per aggiornare Azure Machine Learning per utilizzare la nuova chiave, attenersi alla procedura seguente:

> [!IMPORTANT]
> Eseguire tutti i passaggi, aggiornare l'area di lavoro usando l'interfaccia della riga di comando e gli archivi dati usando Python. L'aggiornamento di un solo o l'altro può causare errori fino a quando non vengono aggiornati entrambi.

1. Rigenerare la chiave. Per informazioni sulla rigenerazione di una chiave di accesso, vedere [gestire le chiavi di accesso all'account di archiviazione](../storage/common/storage-account-keys-manage.md). Salvare la nuova chiave.

1. L'area di lavoro Azure Machine Learning sincronizza automaticamente la nuova chiave e inizia a usarla dopo un'ora. Per forzare immediatamente la sincronizzazione dell'area di lavoro con la nuova chiave, attenersi alla procedura seguente:

    1. Per accedere alla sottoscrizione di Azure che contiene l'area di lavoro usando il comando dell'interfaccia della riga di comando di Azure seguente:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Per aggiornare l'area di lavoro per l'utilizzo della nuova chiave, utilizzare il comando seguente. Sostituire `myworkspace` con il nome dell'area di lavoro Azure Machine Learning e sostituire `myresourcegroup` con il nome del gruppo di risorse di Azure che contiene l'area di lavoro.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Questo comando Sincronizza automaticamente le nuove chiavi per l'account di archiviazione di Azure usato dall'area di lavoro.

1. È possibile registrare nuovamente gli archivi dati che usano l'account di archiviazione tramite l'SDK o [il Azure Machine Learning Studio](https://ml.azure.com).
    1. **Per registrare di nuovo gli archivi dati tramite Python SDK** , usare i valori della sezione [elementi che devono essere aggiornati](#whattoupdate) e la chiave del passaggio 1 con il codice seguente. 
    
        Poiché `overwrite=True` è specificato, questo codice sovrascrive la registrazione esistente e la Aggiorna per l'uso della nuova chiave.
    
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
    
    1. **Per registrare di nuovo gli archivi dati tramite lo studio** , selezionare **archivi dati** dal riquadro sinistro di studio. 
        1. Selezionare l'archivio dati che si desidera aggiornare.
        1. Selezionare il pulsante **Aggiorna credenziali** in alto a sinistra. 
        1. Usare la nuova chiave di accesso del passaggio 1 per popolare il modulo e fare clic su **Salva**.
        
            Se si aggiornano le credenziali per l' **archivio dati predefinito** , completare questo passaggio e ripetere il passaggio 2b per risincronizzare la nuova chiave con l'archivio dati predefinito dell'area di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla registrazione di archivi dati, vedere il [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py) riferimento alla classe.