---
title: Connettersi ai servizi di archiviazione di AzureConnect to Azure storage services
titleSuffix: Azure Machine Learning
description: Informazioni su come usare gli archivi dati per connettersi in modo sicuro ai servizi di archiviazione di Azure durante il training con Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: c5972b602d92b2e08fd70850dd1af5c1236e2b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234468"
---
# <a name="connect-to-azure-storage-services"></a>Connettersi ai servizi di archiviazione di AzureConnect to Azure storage services
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come connettersi ai servizi di archiviazione di Azure tramite gli archivi dati di Azure Machine Learning.In this article, learn how to connect to Azure storage services via Azure Machine Learning datastores. Gli archivi dati archiviano le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione del token nell'insieme di credenziali delle chiavi [associato](https://azure.microsoft.com/services/key-vault/) all'area di lavoro, in modo da poter accedere in modo sicuro allo spazio di archiviazione senza doverli codificare come hard negli script.

È possibile creare datastore da queste soluzioni di archiviazione di [Azure.You](#matrix)can create datastores from these Azure storage solutions . Per le soluzioni di archiviazione non supportate e per risparmiare i costi di uscita dei dati durante gli esperimenti di apprendimento automatico, è consigliabile [spostare i dati](#move) nelle soluzioni di archiviazione di Azure supportate. 

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Un account di archiviazione di Azure con un [contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) di Azure o una condivisione file di Azure.An Azure storage account with an Azure blob container or [Azure file share.](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

- [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o accesso allo studio di Azure Machine [Learning.](https://ml.azure.com/)

- Un'area di lavoro di Azure Machine Learning.
  
  Creare [un'area](how-to-manage-workspace.md) di lavoro di Azure Machine Learning o usarne una esistente tramite Python SDK. Importare `Workspace` la `Datastore` classe e caricare le informazioni `config.json` sulla `from_config()`sottoscrizione dal file utilizzando la funzione . In questo modo viene cercato il file JSON nella directory corrente per impostazione `from_config(path="your/file/path")`predefinita, ma è anche possibile specificare un parametro di percorso in modo che punti al file utilizzando .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipi di servizi di archiviazione dati supportatiSupported data storage service types

Gli archivi dati supportano attualmente l'archiviazione delle informazioni di connessione ai servizi di archiviazione elencati nella matrice seguente. Al momento, il data warehouse di Azure non è supportato. 

| Tipo&nbsp;di archiviazione | Tipo&nbsp;di autenticazione | [Studio&nbsp;&nbsp;di Azure Machine Learning](https://ml.azure.com/) | [SDK&nbsp;&nbsp;di&nbsp; Python di Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Interfaccia&nbsp;&nbsp;della riga di comando di Azure Machine LearningAzure Machine Learning CLI](reference-azure-machine-learning-cli.md) | [API&nbsp;&nbsp;Rest&nbsp; di Azure Machine LearningAzure Machine Learning Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Archiviazione&nbsp;&nbsp;BLOB di AzureAzure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chiave account <br> Token di firma di accesso condiviso | ✓ | ✓ | ✓ |✓
[Condivisione file di AzureAzure&nbsp;File&nbsp;Share](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chiave account <br> Token di firma di accesso condiviso | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Entità servizio| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Entità servizio| ✓ | ✓ | ✓ |✓
[Database&nbsp;&nbsp;SQL di AzureAzure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticazione SQL <br>Entità servizio| ✓ | ✓ | ✓ |✓
[PostgreSQL di Azure&nbsp;](https://docs.microsoft.com/azure/postgresql/overview) | Autenticazione SQL| ✓ | ✓ | ✓ |✓
[Database&nbsp;&nbsp;di&nbsp;Azure per MySQLAzure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticazione SQL|  | ✓* | ✓* |✓*
[File&nbsp;system&nbsp;di Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Nessuna autenticazione | | ✓** | ✓ ** |✓** 

MySQL è supportato solo per la pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
Databricks è supportato solo per [databricks pipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Linee guida per l'archiviazione

È consigliabile creare un archivio dati per un contenitore BLOB di Azure.We recommend creating a [datastore for](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)an Azure Blob container . Sono disponibili sia l'archiviazione standard che premium per i BLOB. Anche se l'archiviazione premium è più costosa, le velocità di trasmissione più elevate potrebbero migliorare la velocità delle esecuzioni di training, in particolare se si esegue il training su un set di dati di grandi dimensioni. Per informazioni sul costo degli account di archiviazione, vedere il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)di Azure.

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) si basa sull'archiviazione BLOB di Azure e progettato per l'analisi dei Big Data aziendali. Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno [spazio dei nomi gerarchico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) all'archivio BLOB. Lo spazio dei nomi gerarchico organizza gli oggetti e i file in una gerarchia di directory per un accesso ai dati efficiente.

Quando si crea un'area di lavoro, un contenitore BLOB di Azure e una condivisione file di Azure vengono registrati automaticamente nell'area di lavoro. Sono denominati `workspaceblobstore` `workspacefilestore`e , rispettivamente. `workspaceblobstore`viene usato per archiviare gli elementi dell'area di lavoro e i log dell'esperimento di apprendimento automatico. `workspacefilestore`viene utilizzato per archiviare i blocchi appunti e gli script R autorizzati tramite [l'istanza](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)di calcolo . Il `workspaceblobstore` contenitore viene impostato come archivio dati predefinito.

> [!IMPORTANT]
> La finestra di progettazione di Azure Machine Learning (anteprima) creerà automaticamente un archivio dati denominato **azureml_globaldatasets** quando si apre un esempio nella home page della finestra di progettazione. Questo archivio dati contiene solo set di dati di esempio. Si prega **di non** utilizzare questo archivio dati per qualsiasi accesso ai dati riservati!
> ![Archivio dati creato automaticamente per set di dati di progettazioneAuto-created datastore for designer sample datasets](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Creare e registrare datastore

Quando si registra una soluzione di archiviazione di Azure come archivio dati, è possibile creare e registrare automaticamente tale archivio dati in un'area di lavoro specifica. È possibile creare e registrare archivi dati in un'area di lavoro usando Python SDK o Azure Machine Learning Studio.You can create and register datastores to a workspace by using the Python SDK or Azure Machine Learning studio.

>[!IMPORTANT]
> Come parte del processo di creazione e registrazione dell'archivio dati iniziale, Azure Machine Learning verifica che il servizio di archiviazione sottostante esista e che l'entità fornita dall'utente (nome utente, entità servizio o token di firma di accesso condiviso) abbia accesso a tale archiviazione. Per gli archivi dati di Azure Data Lake Storage Gen 1 e [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) 2, tuttavia, questa convalida viene eseguita in un secondo momento, quando vengono chiamati metodi di accesso ai dati come o [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) vengono chiamati. 
<br><br>
Dopo la creazione dell'archivio dati, questa convalida viene eseguita solo per i metodi che richiedono l'accesso al contenitore di archiviazione sottostante, **non** ogni volta che vengono recuperati gli oggetti datastore. Ad esempio, la convalida avviene se si desidera scaricare file dall'archivio dati; ma se si desidera solo modificare l'archivio dati predefinito, la convalida non viene eseguita.

### <a name="python-sdk"></a>Python SDK

Tutti i metodi di [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) registro sono `register_azure_*`sulla classe e hanno il formato .

È possibile trovare le informazioni necessarie `register()` per popolare il metodo nel portale di [Azure](https://portal.azure.com).
Selezionare **Account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione da registrare. La pagina **Panoramica** fornisce informazioni quali il nome dell'account, il contenitore e il nome della condivisione file. 

* Per gli elementi di autenticazione, ad esempio la chiave dell'account o il token di firma di accesso condiviso, passare a Chiavi account nel riquadro Impostazioni.For authentication items, like account key or SAS token, go to **Account Keys** on the **Settings** pane. 

* Per gli elementi dell'entità servizio, ad esempio l'ID tenant e l'ID client, passare alle **registrazioni dell'app** e selezionare l'app da usare. La pagina **Panoramica** corrispondente conterrà questi elementi.

> [!IMPORTANT]
> Se l'account di archiviazione si trova in una rete virtuale, è supportata solo la creazione di BLOB, condivisione file, archivi dati ADLS gen 1 e ADLS Gen 2 **tramite l'SDK.** Per concedere all'area di lavoro l'accesso all'account di archiviazione, impostare il parametro `grant_workspace_access` su `True`.

Gli esempi seguenti illustrano come registrare un contenitore BLOB di Azure, una condivisione file di Azure e Azure Data Lake Storage Generation 2 come archivio dati. Per altri servizi di archiviazione, vedere la [documentazione di riferimento per i metodi applicabili `register_azure_*` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Contenitore BLOB

Per registrare un contenitore BLOB [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)di Azure come archivio dati, usare .

Il codice seguente crea `blob_datastore_name` e registra `ws` l'archivio dati nell'area di lavoro. Questo archivio dati `my-container-name` accede al `my-account-name` contenitore BLOB nell'account di archiviazione usando la chiave dell'account fornita.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Condivisione file

Per registrare una condivisione file [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)di Azure come archivio dati, usare . 

Il codice seguente crea `file_datastore_name` e registra `ws` l'archivio dati nell'area di lavoro. Questo archivio dati `my-fileshare-name` accede alla `my-account-name` condivisione file nell'account di archiviazione usando la chiave dell'account fornita.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Generazione di Archiviazione data Lake di Azure 2Azure Data Lake Storage Generation 2

Per un archivio dati di Azure Data Lake Storage Generation 2 (ADLS Gen 2), usare [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) per registrare un archivio dati delle credenziali connesso a un archivio di Azure DataLake Gen 2 con [autorizzazioni dell'entità servizio.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Per utilizzare l'entità servizio è necessario [registrare l'applicazione](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e concedere all'entità servizio l'accesso ai dati corretto. Ulteriori informazioni sul controllo di [accesso impostato per ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Il codice seguente crea `adlsgen2_datastore_name` e registra `ws` l'archivio dati nell'area di lavoro. Questo archivio dati accede `test` al `account_name` file system nell'account di archiviazione usando le credenziali dell'entità servizio fornite.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Creare un nuovo archivio dati in pochi passaggi in Azure Machine Learning Studio:Create a new datastore in a few steps in Azure Machine Learning studio:

> [!IMPORTANT]
> Se l'account di archiviazione si trova in una rete virtuale, è supportata solo la creazione di archivi dati [tramite l'SDK.](#python-sdk) 

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **Datastores** nel riquadro sinistro in **Manage**.
1. Selezionare **: Nuovo archivio dati**.
1. Compilare il modulo per un nuovo archivio dati. Il modulo si aggiorna in modo intelligente in base alle selezioni per il tipo di archiviazione e il tipo di autenticazione di Azure.The form intelligently updates itself based on your selections for Azure storage type and authentication type.
  
È possibile trovare le informazioni necessarie per popolare il modulo nel portale di [Azure.](https://portal.azure.com) Selezionare **Account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione da registrare. La pagina **Panoramica** fornisce informazioni quali il nome dell'account, il contenitore e il nome della condivisione file. 

* Per gli elementi di autenticazione, ad esempio la chiave dell'account o il token di firma di accesso condiviso, passare a Chiavi account nel riquadro Impostazioni.For authentication items, like account key or SAS token, go to **Account Keys** on the **Settings** pane. 

* Per gli elementi dell'entità servizio, ad esempio l'ID tenant e l'ID client, passare alle **registrazioni dell'app** e selezionare l'app da usare. La pagina **Panoramica** corrispondente conterrà questi elementi. 

L'esempio seguente illustra l'aspetto del modulo quando si crea un archivio dati BLOB di Azure:The following example demonstrates what the form looks like when you create an Azure blob datastore: 
    
![Modulo per un nuovo archivio dati](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Ottenere archivi dati dall'area di lavoroGet datastores from your workspace

Per ottenere un archivio dati specifico registrato [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) nell'area `Datastore` di lavoro corrente, usare il metodo statico nella classe:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Per ottenere l'elenco degli archivi dati registrati con [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) una determinata area di lavoro, è possibile utilizzare la proprietà in un oggetto dell'area di lavoro:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Per ottenere l'archivio dati predefinito dell'area di lavoro, utilizzare questa riga:To get the default datastore, use this line:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Caricamento e download dei dati

I [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) metodi e descritti negli esempi seguenti sono specifici e funzionano in modo identico per le classi AzureBlobDatastore e [AzureFileDatastore.The](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) and methods described in the following examples are specific to, and operate identically for, the [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) and AzureFileDatastore classes.

### <a name="upload"></a>Caricamento

Caricare una directory o singoli file nell'archivio dati utilizzando Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Il `target_path` parametro specifica il percorso nella condivisione file (o contenitore BLOB) da caricare. Il valore `None`predefinito è , in modo che i dati vengano caricati in root. Se `overwrite=True`, tutti `target_path` i dati esistenti in viene sovrascritto.

È inoltre possibile caricare un elenco di singoli `upload_files()` file nell'archivio dati tramite il metodo .

### <a name="download"></a>Download

Scaricare i dati da un archivio dati nel file system locale:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Il `target_path` parametro è il percorso della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` `None`è , verrà scaricato tutto il contenuto della condivisione file (o contenitore BLOB).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Accedere ai dati durante l'allenamento

Per interagire con i dati negli archivi dati o per creare un pacchetto dei dati in un oggetto utilizzabile per le attività di Machine Learning, ad esempio il training, creare un set di dati di Azure Machine Learning.To interact with data in your datastores or to package your data into a consumable object for machine learning tasks, like training, [create an Azure Machine Learning dataset](how-to-create-register-datasets.md). I set di dati forniscono funzioni che caricano dati tabulari in un panda o in un frame di dati Spark.Datasets provide functions that load tabular data into a pandas or Spark DataFrame. I set di dati offrono anche la possibilità di scaricare o montare file di qualsiasi formato da Archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Database SQL di Azure e Database di Azure per PostgreSQL. [Ulteriori informazioni su come eseguire il training con i set di dati](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Accesso al codice sorgente durante il training

L'archiviazione BLOB di Azure ha velocità di velocità di velocità effettiva più elevate rispetto a una condivisione file di Azure e verrà ridimensionata a un numero elevato di processi avviati in parallelo. Per questo motivo, è consigliabile configurare le esecuzioni per l'utilizzo dell'archiviazione BLOB per il trasferimento dei file di codice sorgente.

L'esempio di codice seguente specifica nella configurazione di esecuzione quale archivio dati BLOB usare per i trasferimenti di codice sorgente.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Accedere ai dati durante il punteggio

Azure Machine Learning offre diversi modi per usare i modelli per il punteggio. Alcuni di questi metodi non forniscono l'accesso ai datastore. Usare la tabella seguente per comprendere quali metodi consentono di accedere ai datastore durante il punteggio:Use the following table to understand which methods allow you to access datastores during scoring:

| Metodo | Accesso all'archivio dati | Descrizione |
| ----- | :-----: | ----- |
| [Stima in batch](how-to-use-parallel-run-step.md) | ✔ | Eseguire stime su grandi quantità di dati in modo asincrono. |
| [Servizio Web](how-to-deploy-and-where.md) | &nbsp; | Distribuire modelli come servizio Web.Deploy models as a web service. |
| [Modulo Edge IoT di AzureAzure IoT Edge module](how-to-deploy-and-where.md) | &nbsp; | Distribuire modelli ai dispositivi IoT Edge.Deploy models to IoT Edge devices. |

Per le situazioni in cui l'SDK non fornisce l'accesso agli archivi dati, è possibile creare codice personalizzato usando l'SDK di Azure pertinente per accedere ai dati. Ad esempio, [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python) è una libreria client che è possibile usare per accedere ai dati archiviati in BLOB o file.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Spostare i dati nelle soluzioni di archiviazione di Azure supportateMove data to supported Azure storage solutions

Azure Machine Learning supporta l'accesso ai dati da Archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Database SQL di Azure e Database di Azure per PostgreSQL. Se si usa l'archiviazione non supportata, è consigliabile spostare i dati nelle soluzioni di archiviazione di Azure supportate usando [Azure Data Factory e questi passaggi.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool) Lo spostamento dei dati nell'archiviazione supportata consente di risparmiare sui costi di uscita dei dati durante gli esperimenti di apprendimento automatico. 

Azure Data Factory offre un trasferimento dei dati efficiente e resiliente con più di 80 connettori predefiniti senza costi aggiuntivi. Questi connettori includono servizi dati di Azure, origini dati locali, Amazon S3 e Redshift e Google BigQuery.These connectors include Azure data services, on-premises data sources, Amazon S3 and Redshift, and Google BigQuery.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure di Machine LearningCreate an Azure machine learning dataset](how-to-create-register-datasets.md)
* [Eseguire il training di un modello](how-to-train-ml-models.md)
* [Distribuire un modello](how-to-deploy-and-where.md)
