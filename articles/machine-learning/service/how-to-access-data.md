---
title: Accedere ai dati negli archivi dati/BLOB per il training
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare gli archivi dati per accedere alle risorse di archiviazione dati BLOB durante il training con il servizio Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: c171e35c6542febffc666ad5abfab50e093bb698
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359280"
---
# <a name="access-data-from-your-datastores"></a>Accedere ai dati da archivi di dati

Archivi dati consentono di interagire con e accedere ai dati se si esegue il codice in locale, in un cluster di calcolo o in una macchina virtuale. In questo articolo si Scopri i flussi di lavoro di Azure Machine Learning, assicurarsi di archivi dati sono accessibili e resi disponibili per il contesto di calcolo.

Questo argomento vengono illustrati esempi per le attività seguenti:
* [Scegliere un archivio dati](#access)
* [Ottenere i dati](#get)
* [Caricare e scaricare i dati in archivi dati](#up-and-down)
* [Archivio dati di accesso durante il training](#train)

## <a name="prerequisites"></a>Prerequisiti

Per usare gli archivi dati, è necessario un [dell'area di lavoro](concept-azure-machine-learning-architecture.md#workspace) prima. 

Per iniziare, [creare una nuova area di lavoro](setup-create-workspace.md#sdk) o recuperarne una esistente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>Scegliere un archivio dati

È possibile usare l'archivio dati predefinito o personale.

### <a name="use-the-default-datastore-in-your-workspace"></a>Usare l'archivio dati predefinito nell'area di lavoro

Non è necessario creare o configurare un account di archiviazione, poiché ogni area di lavoro dispone di un archivio dati predefinito. È possibile usare l'archivio dati immediatamente perché già registrato nell'area di lavoro. 

Per ottenere l'archivio dati predefinito dell'area di lavoro:
```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrare il proprio archivio dati con l'area di lavoro
Se si ha già una risorsa di Archiviazione di Azure, è possibile registrarla come archivio dati nell'area di lavoro.   Tutti i metodi di registrazione sono sul [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e disporre i form register_azure_ *. 

Gli esempi seguenti illustrano è possibile registrare un contenitore Blob di Azure o una condivisione di File di Azure come un archivio dati.

+ Per un **Datastore contenitore Blob di Azure**, usare [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Per un **Azure File Share Datastore**, usare [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). Ad esempio:  
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Trova & definire gli archivi dati

Per ottenere un archivio dati specificato registrato nell'area di lavoro corrente, usare [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Per ottenere un elenco di tutti gli archivi dati in un'area di lavoro, usare questo codice:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Per definire un archivio dati predefinito diverso per l'area di lavoro corrente, usare [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Caricare e scaricare i dati
Il [ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metodi descritti negli esempi seguenti sono specifici e funzionare in modo identico per i [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) classi.

### <a name="upload"></a>Caricamento

 Caricare una directory o singoli file nell'archivio dati tramite Python SDK.

Per caricare una directory in un archivio dati `ds`:

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```
`target_path` specifica il percorso nella condivisione file (o contenitore Blob) da caricare. Per impostazione predefinita in `None`, in tal caso i dati vengono caricati nella radice. `overwrite=True` sovrascriverà tutti i dati esistenti in `target_path`.

In alternativa, è possibile caricare un elenco di singoli file nell'archivio dati tramite il metodo `upload_files()` dell'archivio dati.

### <a name="download"></a>Download
Analogamente, è possibile scaricare dati da un archivio dati al file system locale.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```
`target_path` è la posizione della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` è `None`, tutto il contenuto della condivisione file (o contenitore Blob) verrà scaricato.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>Archivi dati di accesso durante il training

Dopo aver effettuato l'archivio dati disponibile in attività di calcolo remoto, è possibile accedervi durante le esecuzioni di training (ad esempio, i dati di training o la convalida) semplicemente passando il percorso a esso come parametro nello script di training.

La tabella seguente elenca i comuni [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) i metodi che rendono disponibili gli archivi dati in attività di calcolo remoto.

# #

metodo|Metodo|DESCRIZIONE
----|-----|--------
Eseguire il montaggio| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Usare per montare un archivio dati nell'attività di calcolo remoto. Modalità predefinita per gli archivi dati.
Download|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Consente di scaricare i dati dalla posizione specificata da `path_on_compute` nell'archivio dati per le risorse di calcolo remoto.
Caricamento|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Per caricare i dati alla radice dell'archivio dati dalla posizione specificata da `path_on_compute`.

```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Per fare riferimento a una cartella o un file specifico nell'archivio dati, usare la funzione [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) dell'archivio dati.

```Python
#download the contents of the `./bar` directory from the datastore to the remote compute
ds.path('./bar').as_download()
```



> [!NOTE]
> Eventuali oggetti `ds` oppure `ds.path` si risolvono in un nome di variabile di ambiente nel formato `"$AZUREML_DATAREFERENCE_XXXX"` il cui valore rappresenta il percorso di esecuzione del montaggio o download in attività di calcolo remoto. Il percorso di archivio dati nell'attività di calcolo remoto potrebbe non essere quello utilizzato per il percorso di esecuzione per script di training.

### <a name="examples"></a>Esempi 

I seguenti illustrano gli esempi specifici per il [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe per l'accesso all'archivio dati durante il training.


```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

Poiché `as_mount()` è la modalità predefinita per un archivio dati, è possibile passare direttamente `ds` per il `'--data_dir'` argomento.

Oppure passare al costruttore di strumento di stima in un elenco di archivi dati `inputs` parametro a montare o copiare da/in archivi di dati. Questo esempio di codice:
* Scarica tutto il contenuto nell'archivio dati `ds1` per le risorse di calcolo remoto prima lo script di training `train.py` viene eseguito
* Nella cartella downloads `'./foo'` nell'archivio dati `ds2` le risorse di calcolo remoto prima `train.py` viene eseguito
* Il file viene caricato `'./bar.pkl'` dall'attività di calcolo remoto fino a nell'archivio dati `ds3` dopo l'esecuzione di script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```


## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello](how-to-train-ml-models.md)

* [Distribuire un modello](how-to-deploy-and-where.md)
