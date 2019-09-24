---
title: Accedere ai dati nei servizi di archiviazione di Azure
titleSuffix: Azure Machine Learning
description: Informazioni su come usare gli archivi dati per accedere ai servizi di archiviazione di Azure durante il training con Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: ecd1212385473e33d05f38f67db5708bff060daa
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218251"
---
# <a name="access-data-in-azure-storage-services"></a>Accedere ai dati nei servizi di archiviazione di Azure

 Questo articolo illustra come accedere facilmente ai dati nei servizi di archiviazione di Azure tramite Azure Machine Learning archivi dati. Gli archivi dati vengono usati per archiviare le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione del token, per accedere alla risorsa di archiviazione senza dover codificare in modo rigido tali informazioni negli script.

In questa procedura vengono illustrati esempi delle attività seguenti:
* [Registra archivi dati](#access)
* [Ottenere gli archivi dati dall'area di lavoro](#get)
* [Caricare e scaricare i dati usando gli archivi dati](#up-and-down)
* [Accedere ai dati durante il training](#train)

## <a name="prerequisites"></a>Prerequisiti

Per usare archivi dati, è necessaria prima di tutto un'[area di lavoro](concept-workspace.md).

Per iniziare, [creare una nuova area di lavoro](how-to-manage-workspace.md) o recuperarne una esistente:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Registra archivi dati

Tutti i metodi Register si trovano nella [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) classe e hanno il formato register_azure_ *.

Gli esempi seguenti illustrano come registrare un contenitore BLOB di Azure o una condivisione file di Azure come archivio dati.

+ Per un **archivio dati del contenitore BLOB di Azure**, usare[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ Per un **archivio dati della condivisione file**di Azure [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-), usare. Esempio: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Linee guida per l'archiviazione

Si consiglia il contenitore BLOB di Azure. Per i BLOB sono disponibili sia l'archiviazione standard che Premium. Sebbene sia più costoso, è consigliabile disporre di archiviazione Premium a causa di velocità effettiva più veloci che possono migliorare la velocità delle esecuzioni di training, in particolare se si esegue il training su un set di dati di grandi dimensioni. Per informazioni sui costi dell'account di archiviazione, vedere il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Ottenere gli archivi dati dall'area di lavoro

Per ottenere un archivio dati specifico registrato nell'area di lavoro corrente, usare [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) il metodo statico sulla classe datastore:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Per ottenere l'elenco degli archivi dati registrati con una determinata area di lavoro, è possibile usare `datastores` la proprietà in un oggetto dell'area di lavoro:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Quando si crea un'area di lavoro, un contenitore BLOB di Azure e una condivisione file di Azure vengono registrati `workspaceblobstore` nell' `workspacefilestore` area di lavoro denominata e rispettivamente. Archiviano le informazioni di connessione del contenitore BLOB e la condivisione file di cui è stato effettuato il provisioning nell'account di archiviazione collegato all'area di lavoro. `workspaceblobstore` Viene impostato come archivio dati predefinito.

Per ottenere l'archivio dati predefinito dell'area di lavoro:

```Python
datastore = ws.get_default_datastore()
```

Per definire un archivio dati predefinito diverso per l'area di lavoro corrente [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) , utilizzare il metodo sull'oggetto area di lavoro:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carica & scaricare i dati
I [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metodi [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) e descritti negli esempi seguenti sono specifici di e funzionano in modo identico per le classi [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Caricamento

 Caricare una directory o singoli file nell'archivio dati tramite Python SDK.

Per caricare una directory in un archivio dati `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Il `target_path` parametro specifica il percorso nella condivisione file o nel contenitore BLOB da caricare. Per impostazione predefinita in `None`, in tal caso i dati vengono caricati nella radice. Quando `overwrite=True` vengono sovrascritti i `target_path` dati esistenti in.

In alternativa, caricare un elenco di singoli file nell'archivio dati tramite `upload_files()` il metodo.

### <a name="download"></a>Scarica

Analogamente, è possibile scaricare dati da un archivio dati al file system locale.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Il `target_path` parametro è il percorso della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` è `None`, tutto il contenuto della condivisione file (o contenitore Blob) verrà scaricato.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Accedi ai dati durante il training

> [!IMPORTANT]
> L'uso di [Azure Machine Learning set di dati (anteprima)](how-to-create-register-datasets.md) è il nuovo metodo consigliato per accedere ai dati nel training. I set di dati forniscono funzioni che caricano dati tabulari in Pandas o dataframe di Spark e la possibilità di scaricare o montare file di qualsiasi formato da BLOB di Azure, file di Azure, Azure Data Lake generazione 1, Azure Data Lake generazione 2, Azure SQL, Azure PostgreSQL. Altre informazioni su [come eseguire il training con i set di impostazioni](how-to-train-with-datasets.md).

La tabella seguente elenca i metodi che indicano alla destinazione di calcolo come usare gli archivi dati durante le esecuzioni. 

Modo|Metodo|Descrizione|
----|-----|--------
Montaggio| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Usare per montare l'archivio dati nella destinazione di calcolo.
Scarica|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Usare per scaricare il contenuto dell'archivio dati nel percorso specificato da `path_on_compute`. <br> Questo download si verifica prima dell'esecuzione.
Caricamento|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Usare per caricare un file dal percorso specificato da `path_on_compute` nell'archivio dati. <br> Questo caricamento si verifica dopo l'esecuzione.

Per fare riferimento a una cartella o un file specifico nell'archivio dati e renderlo disponibile nella destinazione di calcolo, usare il metodo [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) datastore.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Qualsiasi `datastore` oggetto `datastore.path` o viene risolto in un nome di variabile di ambiente del formato `"$AZUREML_DATAREFERENCE_XXXX"`, il cui valore rappresenta il percorso di montaggio/download nel calcolo di destinazione. Il percorso dell'archivio dati nel calcolo di destinazione potrebbe non corrispondere al percorso di esecuzione dello script di training.

### <a name="examples"></a>Esempi 

Gli esempi di codice seguenti sono specifici della [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe per l'accesso ai dati durante il training. 

`script_params`è un dizionario contenente i parametri per entry_script. È possibile usarlo per passare un archivio dati e descrivere come rendere disponibili i dati nella destinazione di calcolo. Per altre informazioni, vedere l' [esercitazione](tutorial-train-models-with-aml.md)end-to-end.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

È anche possibile passare un elenco di archivi dati al parametro del costruttore `inputs` di Estimator per montare o copiare i dati da e verso gli archivi dati. Questo esempio di codice:
* Scarica tutto il contenuto nella `datastore1` destinazione di calcolo prima dell'esecuzione dello script `train.py` di training
* Scarica la cartella `'./foo'` nella `datastore2` destinazione di calcolo prima `train.py` dell'esecuzione di
* Carica il file `'./bar.pkl'` dalla destinazione `datastore3` di calcolo in dopo l'esecuzione dello script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>Matrice di calcolo e datastore

Gli archivi dati supportano attualmente l'archiviazione delle informazioni di connessione nei servizi di archiviazione elencati nella matrice seguente. Questa matrice consente di visualizzare le funzionalità di accesso ai dati disponibili per le diverse destinazioni di calcolo e gli scenari di archivio dati. Altre informazioni sulle [destinazioni di calcolo per Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Calcolo|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Locale|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/D         |N/D                                                                         |
| Ambiente di calcolo di Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/D         |N/D                                                                         |
| Macchine virtuali               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| Trasferimento dati                  |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                               |N/D                                           |[Pipeline&nbsp;ml](concept-ml-pipelines.md)            |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                              |N/D                                           |[Pipeline&nbsp;ml](concept-ml-pipelines.md)             |N/D                                                                         |
| Azure Batch                    |[Pipeline&nbsp;ml](concept-ml-pipelines.md)                                               |N/D                                           |N/D         |N/D                                                                         |
| Azure datalake Analytics       |N/D                                           |N/D                                           |[Pipeline&nbsp;ml](concept-ml-pipelines.md)             |N/D                                                                         |

> [!NOTE]
> Potrebbero verificarsi scenari in cui processi di dati di grandi dimensioni estremamente iterativi vengono `as_download()` eseguiti più `as_mount()`rapidamente utilizzando anziché. questa operazione può essere convalidata sperimentalmente.

### <a name="accessing-source-code-during-training"></a>Accesso al codice sorgente durante il training

Archiviazione BLOB di Azure offre velocità di velocità effettiva più elevate rispetto alla condivisione file di Azure e scalabilità a un numero elevato di processi avviati in parallelo. Per questo motivo, è consigliabile configurare le esecuzioni in modo da usare l'archiviazione BLOB per il trasferimento dei file di codice sorgente.

Nell'esempio di codice seguente viene specificato nella configurazione di esecuzione quale archivio dati BLOB utilizzare per i trasferimenti del codice sorgente.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Accedere ai dati durante il Punteggio

Azure Machine Learning offre diversi modi per usare i modelli per l'assegnazione dei punteggi. Alcuni di questi metodi non forniscono l'accesso agli archivi dati. Usare la tabella seguente per informazioni sui metodi che consentono di accedere agli archivi dati durante il Punteggio:

| Metodo | Accesso all'archivio dati | Descrizione |
| ----- | :-----: | ----- |
| [Stima in batch](how-to-run-batch-predictions.md) | ✔ | Eseguire stime su grandi quantità di dati in modo asincrono. |
| [Servizio Web](how-to-deploy-and-where.md) | &nbsp; | Distribuire i modelli come servizio Web. |
| [Modulo IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Distribuire i modelli nei dispositivi IoT Edge. |

Per le situazioni in cui l'SDK non fornisce l'accesso agli archivi dati, è possibile creare codice personalizzato usando Azure SDK pertinente per accedere ai dati. Ad esempio, l'uso di [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python) per accedere ai dati archiviati nei BLOB.


## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello](how-to-train-ml-models.md)

* [Distribuire un modello](how-to-deploy-and-where.md)
