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
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 638d7bfb0e396874415c1055c4b707a65caffa4e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269294"
---
# <a name="access-data-from-your-datastores"></a>Accedere ai dati da archivi di dati

 Nel servizio di Azure Machine Learning, archivi dati sono di calcolo meccanismi indipendente dalla posizione per accedere all'archiviazione senza dover apportare modifiche al codice sorgente. Scrivere il codice di training per richiedere un percorso come parametro, o fornire un archivio dati direttamente a un Estimatore, assicurarsi che i flussi di lavoro di Azure Machine Learning i percorsi di archivio dati sono accessibili e resi disponibili per il contesto di calcolo.

Questo argomento vengono illustrati esempi delle attività seguenti:
* [Scegliere un archivio dati](#access)
* [Ottenere i dati](#get)
* [Caricare e scaricare i dati in archivi dati](#up-and-down)
* [Archivio dati di accesso durante il training](#train)

## <a name="prerequisites"></a>Prerequisiti

Per usare archivi dati, è necessaria prima di tutto un'[area di lavoro](concept-workspace.md).

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

 Ogni area di lavoro dispone di un archivio di dati predefinito registrato, che è possibile utilizzare sin da subito.

Per ottenere l'archivio dati predefinito dell'area di lavoro:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrare il proprio archivio dati con l'area di lavoro

Se si ha già una risorsa di Archiviazione di Azure, è possibile registrarla come archivio dati nell'area di lavoro. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Linee guida per l'archiviazione

Si consiglia di archivi dati di blob e archiviazione blob. Archiviazione standard e premium sono disponibili per i BLOB. Anche se più costosa, si consiglia di archiviazione premium a causa di velocità più elevate della velocità effettiva che può migliorare la velocità della formazione viene eseguito, particlularly se si esegue il training rispetto a un set di dati di grandi dimensioni. Vedere le [calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) per account di archiviazione di informazioni sui costi.

>[!NOTE]
> Servizio Azure Machine Learning supporta altri tipi di archivi dati, che può essere utile per scenari specifici. Ad esempio, se è necessario eseguire il training con dati archiviati in un database, è possibile utilizzare il AzureSQLDatabaseDatastore o AzurePostgreSqlDatastore. Visualizzare [questa tabella](#matrix) per i tipi di archivio dati disponibile.

#### <a name="register-your-datastore"></a>Registrare l'archivio dati
Tutti i metodi di registrazione sono sul [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e disporre i form register_azure_ *.

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
                                           file_share_name='your file share name',
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
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

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

Dopo aver effettuato l'archivio dati disponibile nella destinazione di calcolo il training, è possibile accedere durante le esecuzioni di training (ad esempio, i dati di training o la convalida), semplicemente passando il percorso a esso come parametro nello script di training.

La tabella seguente elenca i [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metodi che indicano come usare l'archivio dati durante le esecuzioni della destinazione di calcolo.

metodo|Metodo|Descrizione|
----|-----|--------
Eseguire il montaggio| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Usare per montare l'archivio dati nella destinazione di calcolo.
Download|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Usare per scaricare il contenuto dell'archivio dati nella posizione specificata da `path_on_compute`. <br> Per il contesto di esecuzione di training, questo download viene eseguita prima dell'esecuzione.
Caricamento|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Consente di caricare un file dal percorso specificato dal `path_on_compute` all'archivio dati. <br> Per il contesto di esecuzione di training, questo caricamento avviene dopo l'esecuzione.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Per fare riferimento a file nell'archivio dati o una cartella specifica e renderla disponibile nella destinazione di calcolo, usare l'archivio dati [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) (funzione).

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Eventuali `ds` oppure `ds.path` oggetto si risolve in un nome di variabile di ambiente nel formato `"$AZUREML_DATAREFERENCE_XXXX"` il cui valore rappresenta il percorso di montaggio e scaricare sulle risorse di calcolo di destinazione. Il percorso dell'archivio dati a risorse di calcolo di destinazione potrebbe non essere quello utilizzato per il percorso di esecuzione per script di training.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Matrice di calcolo e l'archivio dati di training

La matrice seguente consente di visualizzare le funzionalità di accesso di dati disponibili per le destinazioni di calcolo di training diversi e gli scenari di archivio dati. Altre informazioni sul [training destinazioni di calcolo di Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Calcolo|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/D         |N/D                                                                         |
| Ambiente di calcolo di Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/D         |N/D                                                                         |
| Macchine virtuali               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| Trasferimento dati                  |[Machine Learning&nbsp;pipeline](concept-ml-pipelines.md)                                               |N/D                                           |[Machine Learning&nbsp;pipeline](concept-ml-pipelines.md)            |[Machine Learning&nbsp;pipeline](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Machine Learning&nbsp;pipeline](concept-ml-pipelines.md)                                              |N/D                                           |[Machine Learning&nbsp;pipeline](concept-ml-pipelines.md)             |N/D                                                                         |
| Azure Batch                    |[Machine Learning&nbsp;pipeline](concept-ml-pipelines.md)                                               |N/D                                           |N/D         |N/D                                                                         |
| Azure Data Lake Analitica       |N/D                                           |N/D                                           |[Machine Learning&nbsp;pipeline](concept-ml-pipelines.md)             |N/D                                                                         |

> [!NOTE]
> Possono esistere scenari in cui altamente iterativi, i processi di dati di grandi dimensioni eseguiti usando più veloce `as_download()` invece di `as_mount()`; ciò può essere convalidato sperimentale.

### <a name="examples"></a>Esempi 

Esempi di codice seguenti sono specifici per il [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe per l'accesso all'archivio dati durante il training.

Questo codice crea un Estimatore usando lo script di training `train.py`, dalla directory di origine indicato utilizzando i parametri definiti `script_params`, tutte nella formazione specificate destinazione di calcolo.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

È anche possibile passare in un elenco di archivi dati per il costruttore Estimator `inputs` parametro a montare o copiare da/in archivi di dati. Questo esempio di codice:
* Scarica tutto il contenuto nell'archivio dati `ds1` per la destinazione di calcolo prima lo script di training `train.py` viene eseguito
* Nella cartella downloads `'./foo'` nell'archivio dati `ds2` per la destinazione di calcolo prima `train.py` viene eseguito
* Il file viene caricato `'./bar.pkl'` dalla destinazione di calcolo fino a nell'archivio dati `ds3` dopo l'esecuzione di script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello](how-to-train-ml-models.md)

* [Distribuire un modello](how-to-deploy-and-where.md)
