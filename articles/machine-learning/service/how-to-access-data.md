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
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319317"
---
# <a name="access-data-from-your-datastores"></a>Accedere ai dati dagli archivi dati

 Nel servizio Azure Machine Learning, gli archivi dati sono meccanismi indipendenti dalla posizione di calcolo per accedere all'archiviazione senza richiedere modifiche al codice sorgente. Sia che si scriva il codice di training per l'uso di un percorso come parametro o si fornisca un archivio dati direttamente a uno strumento di stima, Azure Machine Learning i flussi di lavoro assicurano che i percorsi dell'archivio dati siano accessibili e resi disponibili per il contesto di calcolo.

In questa procedura vengono illustrati esempi delle attività seguenti:
* [Scegliere un archivio dati](#access)
* [Ottenere i dati](#get)
* [Caricare e scaricare i dati negli archivi dati](#up-and-down)
* [Accedi a datastore durante il training](#train)

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

È possibile usare l'archivio dati predefinito o portarne di personalizzati.

### <a name="use-the-default-datastore-in-your-workspace"></a>Usare l'archivio dati predefinito nell'area di lavoro

 Ogni area di lavoro dispone di un archivio dati predefinito registrato che è possibile usare immediatamente.

Per ottenere l'archivio dati predefinito dell'area di lavoro:

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>Registrare un archivio dati personalizzato con l'area di lavoro

Se si ha già una risorsa di Archiviazione di Azure, è possibile registrarla come archivio dati nell'area di lavoro. 

<a name="store"></a>

####  <a name="storage-guidance"></a>Linee guida per l'archiviazione

Si consiglia l'archiviazione BLOB e i dati BLOB. Per i BLOB sono disponibili sia l'archiviazione standard che Premium. Sebbene sia più costoso, è consigliabile disporre di archiviazione Premium a causa di velocità effettiva più veloci che possono migliorare la velocità delle esecuzioni di training, in particolare se si esegue il training su un set di dati di grandi dimensioni. Per informazioni sui costi dell'account di archiviazione, vedere il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

>[!NOTE]
> Azure Machine Learning servizio supporta altri tipi di archivi dati, che possono essere utili per scenari specifici. Se, ad esempio, è necessario eseguire il training usando i dati archiviati in un database, è possibile usare AzureSQLDatabaseDatastore o AzurePostgreSqlDatastore. Vedere [questa tabella](#matrix) per i tipi di archivio dati disponibili.

#### <a name="register-your-datastore"></a>Registrare l'archivio dati
Tutti i metodi Register si trovano nella [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e hanno il formato register_azure_ *.

Gli esempi seguenti illustrano come registrare un contenitore BLOB di Azure o una condivisione file di Azure come archivio dati.

+ Per un **archivio dati del contenitore BLOB di Azure**, usare[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ Per un **archivio dati della condivisione file**di Azure [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-), usare. Ad esempio: 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>Trovare & definire gli archivi dati

Per ottenere un archivio dati specificato registrato nell'area di lavoro corrente, [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) usare:

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

Per ottenere un elenco di tutti gli archivi dati in una determinata area di lavoro, usare il codice seguente:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

Per definire un archivio dati predefinito diverso per l'area di lavoro corrente [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-), usare:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carica & scaricare i dati
I [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metodi [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e descritti negli esempi seguenti sono specifici di e funzionano in modo identico per le classi [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

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
## <a name="access-datastores-during-training"></a>Accedere agli archivi dati durante il training

Una volta reso disponibile l'archivio dati nella destinazione di calcolo di training, è possibile accedervi durante le esecuzioni di training, ad esempio i dati di training o di convalida, passando semplicemente il percorso come parametro nello script di training.

La tabella seguente elenca i [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) metodi che indicano alla destinazione di calcolo come usare l'archivio dati durante le esecuzioni.

Modo|Metodo|Descrizione|
----|-----|--------
Eseguire il montaggio| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| Usare per montare l'archivio dati nella destinazione di calcolo.
Download|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|Usare per scaricare il contenuto dell'archivio dati nel percorso specificato da `path_on_compute`. <br> Per il contesto di esecuzione del training, questo download si verifica prima dell'esecuzione.
Caricamento|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| Usare per caricare un file dal percorso specificato da `path_on_compute` nell'archivio dati. <br> Per il contesto di esecuzione del training, questo caricamento si verifica dopo l'esecuzione.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

Per fare riferimento a una cartella o un file specifico nell'archivio dati e renderlo disponibile nella destinazione di calcolo, usare la [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) funzione dell'archivio dati.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Qualsiasi `ds` oggetto `ds.path` o viene risolto in un nome di variabile di ambiente del formato `"$AZUREML_DATAREFERENCE_XXXX"` il cui valore rappresenta il percorso di montaggio/download nel calcolo di destinazione. Il percorso dell'archivio dati nel calcolo di destinazione potrebbe non corrispondere al percorso di esecuzione dello script di training.

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>Training di calcolo e matrice di datastore

La matrice seguente mostra le funzionalità di accesso ai dati disponibili per le diverse destinazioni di calcolo di training e per gli scenari di archivio dati. Altre informazioni sulle [destinazioni di calcolo per il training per Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Calcolo|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/D         |N/D                                                                         |
| Ambiente di calcolo di Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/D         |N/D                                                                         |
| Macchine virtuali               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| Trasferimento dati                  |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                               |N/D                                           |[Pipeline&nbsp;ml](concept-ml-pipelines.md)            |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                              |N/D                                           |[Pipeline&nbsp;ml](concept-ml-pipelines.md)             |N/D                                                                         |
| Azure Batch                    |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                               |N/D                                           |N/D         |N/D                                                                         |
| Azure datalake Analytics       |N/D                                           |N/D                                           |[Pipeline&nbsp;ml](concept-ml-pipelines.md)             |N/D                                                                         |

> [!NOTE]
> Potrebbero verificarsi scenari in cui processi di dati di grandi dimensioni estremamente iterativi vengono `as_download()` eseguiti più `as_mount()`rapidamente utilizzando anziché. questa operazione può essere convalidata sperimentalmente.

### <a name="examples"></a>Esempi 

Gli esempi di codice seguenti sono specifici della [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe per l'accesso all'archivio dati durante il training.

Questo codice crea un estimatore usando lo script di `train.py`training,, dalla directory di origine indicata usando i parametri `script_params`definiti in, tutti nella destinazione di calcolo di training specificata.

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

È anche possibile passare un elenco di archivi dati al parametro del costruttore `inputs` di Estimator per montare o copiare negli archivi dati. Questo esempio di codice:
* Scarica tutto il contenuto nell'archivio dati `ds1` nella destinazione di calcolo prima dell'esecuzione dello script `train.py` di training
* Scarica la cartella `'./foo'` nell'archivio dati `ds2` nella destinazione di calcolo prima `train.py` dell'esecuzione di.
* Carica il file `'./bar.pkl'` dalla destinazione di calcolo fino all'archivio dati `ds3` dopo l'esecuzione dello script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>Accedere agli archivi dati durante l'assegnazione dei punteggi

Il servizio Azure Machine Learning offre diversi modi per usare i modelli per l'assegnazione dei punteggi. Alcuni di questi metodi non forniscono l'accesso agli archivi dati. Usare la tabella seguente per informazioni sui metodi che consentono di accedere agli archivi dati durante il Punteggio:

| Metodo | Accesso all'archivio dati | Descrizione |
| ----- | :-----: | ----- |
| [Stima in batch](how-to-run-batch-predictions.md) | ✔ | Eseguire stime su grandi quantità di dati in modo asincrono. |
| [Servizio Web](how-to-deploy-and-where.md) | &nbsp; | Distribuire i modelli come servizio Web. |
| [Modulo IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Distribuire i modelli nei dispositivi IoT Edge. |

Per le situazioni in cui l'SDK non fornisce l'accesso agli archivi dati, è possibile creare codice personalizzato usando Azure SDK pertinente per accedere ai dati. Ad esempio, l'uso di [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python) per accedere ai dati archiviati nei BLOB.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello](how-to-train-ml-models.md)

* [Distribuire un modello](how-to-deploy-and-where.md)
