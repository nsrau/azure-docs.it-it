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
ms.openlocfilehash: 8c9b8489ded264a895d480ed180b411da079e883
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950109"
---
# <a name="access-data-in-azure-storage-services"></a>Accedere ai dati nei servizi di archiviazione di Azure

Questo articolo illustra come accedere facilmente ai dati nei servizi di archiviazione di Azure tramite Azure Machine Learning archivi dati. Gli archivi dati vengono usati per archiviare le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione del token. L'uso di archivi dati consente di accedere alla risorsa di archiviazione senza dover codificare le informazioni di connessione in modo rigido negli script. È possibile creare archivi dati da queste [soluzioni di archiviazione di Azure](#matrix).

In questa procedura vengono illustrati esempi delle attività seguenti:
* [Registra archivi dati](#access)
* [Ottenere gli archivi dati dall'area di lavoro](#get)
* [Caricare e scaricare i dati usando gli archivi dati](#up-and-down)
* [Accedere ai dati durante il training](#train)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Un account di archiviazione di Azure con un [contenitore BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) o una [condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o accesso alla [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com/).

- Un'area di lavoro di Azure Machine Learning. 
    - [Creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md) o utilizzarne una esistente usando Python SDK.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Creare e registrare archivi dati

Quando si registra una soluzione di archiviazione di Azure come archivio dati, l'archivio dati viene creato automaticamente in un'area di lavoro specifica. È possibile creare e registrare archivi dati in un'area di lavoro usando Python SDK o la pagina di destinazione dell'area di lavoro.

### <a name="using-the-python-sdk"></a>Uso di Python SDK

Tutti i metodi Register si trovano nella classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) e hanno il formato register_azure_ *.

Le informazioni necessarie per popolare il metodo Register () sono reperibili tramite il [portale di Azure](https://ms.portal.azure.com). Selezionare **account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione che si vuole registrare. Nella pagina **Panoramica** sono disponibili informazioni quali, il nome dell'account e il contenitore o il nome della condivisione file. Per informazioni di autenticazione, ad esempio chiave account o token di firma di accesso condiviso, passare a **chiavi account** nel riquadro **Impostazioni** a sinistra. 

Gli esempi seguenti illustrano come registrare un contenitore BLOB di Azure o una condivisione file di Azure come archivio dati.

+ Per un **archivio dati del contenitore BLOB di Azure**, usare [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

    Il codice seguente crea e registra l'archivio dati, `my_datastore`, nell'area di lavoro, `ws`. Questo archivio dati accede al contenitore BLOB di Azure, `my_blob_container`, nell'account di archiviazione di Azure `my_storage_account` usando la chiave dell'account fornita.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```

+ Per un **archivio dati della condivisione file di Azure**, usare [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

    Il codice seguente crea e registra l'archivio dati, `my_datastore`, nell'area di lavoro, `ws`. Questo archivio dati accede alla condivisione file di Azure, `my_file_share`, nell'account di archiviazione di Azure `my_storage_account` usando la chiave dell'account fornita.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Linee guida per l'archiviazione

Si consiglia il contenitore BLOB di Azure. Per i BLOB sono disponibili sia l'archiviazione standard che Premium. Sebbene sia più costoso, è consigliabile disporre di archiviazione Premium a causa di velocità effettiva più veloci che possono migliorare la velocità delle esecuzioni di training, in particolare se si esegue il training su un set di dati di grandi dimensioni. Per informazioni sui costi dell'account di archiviazione, vedere il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) .

### <a name="using-the-workspace-landing-page"></a>Uso della pagina di destinazione dell'area di lavoro 

Creare un nuovo archivio dati in pochi passaggi nella pagina di destinazione dell'area di lavoro.

1. Effettuare l'accesso alla [pagina di destinazione dell'area di lavoro](https://ml.azure.com/).
1. Selezionare **archivi dati** nel riquadro sinistro in **Gestisci**.
1. Selezionare **+ nuovo archivio dati**.
1. Completare il nuovo modulo datastore. Il modulo viene aggiornato in modo intelligente in base alle selezioni del tipo di archiviazione di Azure e del tipo di autenticazione.
  
Le informazioni necessarie per popolare il modulo possono essere trovate tramite la [portale di Azure](https://ms.portal.azure.com). Selezionare **account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione che si vuole registrare. Nella pagina **Panoramica** sono disponibili informazioni quali, il nome dell'account e il contenitore o il nome della condivisione file. Per gli elementi di autenticazione, ad esempio la chiave dell'account o il token di firma di accesso condiviso, passare a **chiavi dell'account** nel riquadro **Impostazioni** a sinistra.

Nell'esempio seguente viene illustrato l'aspetto del modulo per la creazione di un archivio dati BLOB di Azure. 
    
 ![Nuovo archivio dati](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Ottenere gli archivi dati dall'area di lavoro

Per ottenere un archivio dati specifico registrato nell'area di lavoro corrente, usare il metodo statico [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) sulla classe datastore:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Per ottenere l'elenco degli archivi dati registrati in una determinata area di lavoro, è possibile usare la proprietà [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) in un oggetto dell'area di lavoro:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Quando si crea un'area di lavoro, un contenitore BLOB di Azure e una condivisione file di Azure vengono registrati nell'area di lavoro denominata rispettivamente `workspaceblobstore` e `workspacefilestore`. Archiviano le informazioni di connessione del contenitore BLOB e la condivisione file di cui è stato effettuato il provisioning nell'account di archiviazione collegato all'area di lavoro. Il `workspaceblobstore` è impostato come archivio dati predefinito.

Per ottenere l'archivio dati predefinito dell'area di lavoro:

```Python
datastore = ws.get_default_datastore()
```

Per definire un archivio dati predefinito diverso per l'area di lavoro corrente, usare il metodo [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) sull'oggetto dell'area di lavoro:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Carica & scaricare i dati
I metodi [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) e [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) descritti negli esempi seguenti sono specifici di e funzionano in modo identico per le classi [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

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

Il parametro `target_path` specifica il percorso nella condivisione file o nel contenitore BLOB da caricare. Per impostazione predefinita in `None`, in tal caso i dati vengono caricati nella radice. Quando `overwrite=True` vengono sovrascritti tutti i dati esistenti in `target_path`.

In alternativa, caricare un elenco di singoli file nell'archivio dati tramite il metodo `upload_files()`.

### <a name="download"></a>Scarica

Analogamente, è possibile scaricare dati da un archivio dati al file system locale.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Il parametro `target_path` è il percorso della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` è `None`, tutto il contenuto della condivisione file (o contenitore Blob) verrà scaricato.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Accedi ai dati durante il training

> [!IMPORTANT]
> L'uso di [Azure Machine Learning set di dati (anteprima)](how-to-create-register-datasets.md) è il nuovo metodo consigliato per accedere ai dati nel training. I set di dati forniscono funzioni che caricano dati tabulari in Pandas o dataframe di Spark e la possibilità di scaricare o montare file di qualsiasi formato da BLOB di Azure, file di Azure, Azure Data Lake generazione 1, Azure Data Lake generazione 2, Azure SQL, Azure PostgreSQL. Altre informazioni su [come eseguire il training con i set di impostazioni](how-to-train-with-datasets.md).

La tabella seguente elenca i metodi che indicano alla destinazione di calcolo come usare gli archivi dati durante le esecuzioni. 

Modo|Metodo|Descrizione|
----|-----|--------
Montaggio| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Usare per montare l'archivio dati nella destinazione di calcolo.
Scarica|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Usare per scaricare il contenuto dell'archivio dati nel percorso specificato da `path_on_compute`. <br><br> Questo download si verifica prima dell'esecuzione.
Caricamento|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Usare per caricare un file dal percorso specificato da `path_on_compute` nell'archivio dati. <br><br> Questo caricamento si verifica dopo l'esecuzione.

Per fare riferimento a una cartella o un file specifico nell'archivio dati e renderlo disponibile nella destinazione di calcolo, usare il metodo datastore [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) .

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Qualsiasi oggetto `datastore` o `datastore.path` specificato viene risolto in un nome di variabile di ambiente con formato `"$AZUREML_DATAREFERENCE_XXXX"`, il cui valore rappresenta il percorso di montaggio/download nel calcolo di destinazione. Il percorso dell'archivio dati nel calcolo di destinazione potrebbe non corrispondere al percorso di esecuzione dello script di training.

### <a name="examples"></a>Esempi 

Gli esempi di codice seguenti sono specifici della classe [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) per l'accesso ai dati durante il training. 

`script_params` è un dizionario contenente i parametri per entry_script. Usarlo per passare un archivio dati e descrivere come vengono resi disponibili i dati nella destinazione di calcolo. Per altre informazioni, vedere l' [esercitazione](tutorial-train-models-with-aml.md)end-to-end.

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

È anche possibile passare un elenco di archivi dati al costruttore di Estimator @no__t parametro-0 per montare o copiare i dati da e verso gli archivi dati. Questo esempio di codice:
* Scarica tutti i contenuti in `datastore1` nella destinazione di calcolo prima dell'esecuzione dello script di training `train.py`
* Scarica la cartella `'./foo'` in `datastore2` alla destinazione di calcolo prima dell'esecuzione di `train.py`
* Carica il file `'./bar.pkl'` dalla destinazione di calcolo al `datastore3` dopo l'esecuzione dello script

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Matrice di calcolo e datastore

Gli archivi dati supportano attualmente l'archiviazione delle informazioni di connessione nei servizi di archiviazione elencati nella matrice seguente. Questa matrice consente di visualizzare le funzionalità di accesso ai dati disponibili per le diverse destinazioni di calcolo e gli scenari di archivio dati. Altre informazioni sulle [destinazioni di calcolo per Azure Machine Learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Calcolo|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Locale|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/D         |N/D                                                                         |
| Ambiente di calcolo di Azure Machine Learning |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/D         |N/D                                                                         |
| Macchine virtuali               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/D         |N/D                                                                         |
| Trasferimento dati                  |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                               |N/D                                           |[ML @ no__t-1pipelines](concept-ml-pipelines.md)            |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                              |N/D                                           |[ML @ no__t-1pipelines](concept-ml-pipelines.md)             |N/D                                                                         |
| Azure Batch                    |[ML @ no__t-1pipelines](concept-ml-pipelines.md)                                               |N/D                                           |N/D         |N/D                                                                         |
| Azure datalake Analytics       |N/D                                           |N/D                                           |[ML @ no__t-1pipelines](concept-ml-pipelines.md)             |N/D                                                                         |

> [!NOTE]
> Potrebbero verificarsi scenari in cui processi di dati di grandi dimensioni estremamente iterativi vengono eseguiti più rapidamente utilizzando `as_download()` anziché `as_mount()`; Questa operazione può essere convalidata sperimentalmente.

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

Per le situazioni in cui l'SDK non fornisce l'accesso agli archivi dati, è possibile creare codice personalizzato usando Azure SDK pertinente per accedere ai dati. [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python) , ad esempio, è una libreria client che è possibile usare per accedere ai dati archiviati in BLOB o file.


## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello](how-to-train-ml-models.md)

* [Distribuire un modello](how-to-deploy-and-where.md)
