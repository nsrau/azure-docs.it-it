---
title: Connettersi ai servizi di archiviazione di Azure
titleSuffix: Azure Machine Learning
description: Informazioni su come usare gli archivi dati per connettersi in modo sicuro ai servizi di archiviazione di Azure durante il training con Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 07/08/2020
ms.custom: how-to, seodec18, tracking-python
ms.openlocfilehash: 45fb9ef25bdfa43db9c167d58011fc6196020b65
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321633"
---
# <a name="connect-to-azure-storage-services"></a>Connettersi ai servizi di archiviazione di Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come **connettersi ai servizi di archiviazione di Azure tramite Azure Machine Learning archivi dati**. Gli archivi dati archiviano le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione con token nell'istanza di [Key Vault](https://azure.microsoft.com/services/key-vault/) associata all'area di lavoro, in modo da poter accedere in modo sicuro all'archiviazione senza che sia necessario che le informazioni siano hardcoded negli script. 

**Per le soluzioni di archiviazione non supportate**e per risparmiare i costi di uscita durante gli esperimenti di ml, [spostare i dati](#move) in una soluzione di archiviazione di Azure supportata.  È possibile creare archivi dati da [queste soluzioni di archiviazione di Azure](#matrix). 

Per informazioni sul ruolo degli archivi dati nel flusso di lavoro generale di accesso ai dati di Azure Machine Learning, vedere l'articolo [Proteggere l'accesso ai dati](concept-data.md#data-workflow).

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Un account di archiviazione di Azure con un [contenitore BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) o una [condivisione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) o accesso a [Azure Machine Learning Studio](https://ml.azure.com/).

- Un'area di lavoro di Azure Machine Learning.
  
  [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) o usarne una esistente tramite Python SDK. Importare le classi `Workspace` e `Datastore` e caricare le informazioni sulla sottoscrizione dal file `config.json` usando la funzione `from_config()`. Per impostazione predefinita, il file JSON viene cercato nella directory corrente, ma è possibile anche specificare un parametro del percorso che punti al file usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Tipi di servizi di archiviazione dati supportati

Gli archivi dati attualmente supportano l'archiviazione delle informazioni di connessione nei servizi di archiviazione elencati nella matrice seguente.

| Tipo di&nbsp;archiviazione | Tipo di&nbsp;autenticazione | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Interfaccia della riga di comando di Azure&nbsp;Machine&nbsp;Learning](reference-azure-machine-learning-cli.md) | [API REST di&nbsp;Azure&nbsp;Machine&nbsp;Learning](https://docs.microsoft.com/rest/api/azureml/) | Visual Studio Code
---|---|---|---|---|---|---
[Archiviazione&nbsp;BLOB&nbsp;di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Chiave account <br> Token di firma di accesso condiviso | ✓ | ✓ | ✓ |✓ |✓
[Condivisione&nbsp;file di&nbsp;Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Chiave account <br> Token di firma di accesso condiviso | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage&nbsp;Gen1](https://docs.microsoft.com/azure/data-lake-store/)| Entità servizio| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage&nbsp;Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Entità servizio| ✓ | ✓ | ✓ |✓|
[Database&nbsp;SQL&nbsp;di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| Autenticazione SQL <br>Entità servizio| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | Autenticazione SQL| ✓ | ✓ | ✓ |✓|
[Database di&nbsp;Azure&nbsp;per&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | Autenticazione SQL|  | ✓* | ✓* |✓*|
[File&nbsp;system di&nbsp;Databricks](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Nessuna autenticazione | | ✓** | ✓ ** |✓** |

\* MySQL è supportato solo per l'oggetto [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) della pipeline <br>
** Databricks è supportato solo per l'oggetto [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) della pipeline

### <a name="storage-guidance"></a>Linee guida per l'archiviazione

È consigliabile creare un archivio dati per un [contenitore BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Per i BLOB sono disponibili i livelli di archiviazione Standard e Premium. Sebbene l'archiviazione Premium sia più costosa, la velocità effettiva maggiore può migliorare la velocità delle esecuzioni dei training, in particolare se si esegue il training su un set di dati di grandi dimensioni. Per informazioni sul costo degli account di archiviazione, vedere [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) si basa su Archiviazione BLOB di Azure ed è una soluzione progettata per le analisi di Big Data aziendali. Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno [spazio dei nomi gerarchico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) all'archivio BLOB. Lo spazio dei nomi gerarchico organizza gli oggetti e i file in una gerarchia di directory per un accesso ai dati efficiente.

Quando si crea un'area di lavoro, vengono registrati automaticamente nell'area di lavoro un contenitore BLOB di Azure e una condivisione file di Azure. A questi due elementi vengono assegnati, rispettivamente, i nomi `workspaceblobstore` e `workspacefilestore`. `workspaceblobstore` consente di archiviare gli artefatti dell'area di lavoro e i log degli esperimenti di Machine Learning. `workspacefilestore` consente di archiviare notebook e script R autorizzati tramite l'[istanza di calcolo](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files). Il `workspaceblobstore` contenitore viene impostato come archivio dati predefinito e non può essere eliminato dall'area di lavoro.

> [!IMPORTANT]
> La finestra di progettazione di Azure Machine Learning (anteprima) crea automaticamente un archivio dati denominato **azureml_globaldatasets** quando si apre un campione nella home page della finestra di progettazione. Questo archivio dati contiene solo i set di dati del campione. **Non** usare questo archivio dati per l'accesso ai dati riservati.
> ![Archivio dati creato automaticamente per i set di dati del campione nella finestra di progettazione](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Creare e registrare archivi dati

Quando si registra una soluzione di archiviazione di Azure come archivio dati, l'archivio dati viene creato e registrato automaticamente in un'area di lavoro specifica. È possibile creare e registrare archivi dati in un'area di lavoro usando [Python SDK](#python-sdk) o [Azure Machine Learning Studio](#azure-machine-learning-studio).

>[!IMPORTANT]
> Come parte del processo di creazione e registrazione dell'archivio dati iniziale, Azure Machine Learning verifica che il servizio di archiviazione sottostante esista e che l'entità di sicurezza fornita dall'utente (nome utente, entità servizio o token di firma di accesso condiviso) abbia accesso a tale archivio. Per Azure Data Lake Storage Gen1 e Gen2, tuttavia, questa convalida viene eseguita in un secondo momento, quando vengono chiamati metodi di accesso ai dati come [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) o [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-). 
<br><br>
Dopo la creazione dell'archivio dati, questa convalida viene eseguita solo per i metodi che richiedono l'accesso al contenitore di archiviazione sottostante, **non** ogni volta che vengono recuperati gli oggetti dell'archivio dati. La convalida viene ad esempio eseguita se è necessario scaricare i file dall'archivio dati, mentre se si vuole solo modificare l'archivio dati predefinito, la convalida non viene eseguita.

### <a name="python-sdk"></a>Python SDK

Tutti i metodi di registrazione si trovano nella classe [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) e hanno il formato `register_azure_*`.

> [!IMPORTANT]
> Se si prevede di creare un archivio dati per gli account di archiviazione che si trovano in una rete virtuale, vedere la sezione [accedere ai dati in una rete virtuale](#access-data-in-a-virtual-network) .

È possibile trovare le informazioni necessarie per popolare il metodo `register_azure_*()` nel [portale di Azure](https://portal.azure.com).

* Il nome dell'archivio dati deve essere costituito solo da lettere minuscole, cifre e caratteri di sottolineatura. 

* Se si prevede di usare una chiave dell'account o un token di firma di accesso condiviso per l'autenticazione, selezionare **Account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione da registrare. 
  * La pagina **Panoramica** fornisce informazioni come il nome dell'account, il contenitore e il nome della condivisione file. 
      1. Per le chiavi dell'account, passare a **Chiavi di accesso** nel riquadro **Impostazioni**. 
      1. Per i token di firma di accesso condiviso, passare a **Shared access signatures** (Firme di accesso condiviso) nel riquadro **Impostazioni**.

* Se si prevede di usare un'entità servizio per l'autenticazione, passare alla **registrazioni app** e selezionare l'app che si vuole usare. 
    * La pagina di **Panoramica** corrispondente conterrà informazioni obbligatorie come ID tenant e ID client.

> [!IMPORTANT]
> Per motivi di sicurezza, potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure (chiave dell'account o token SAS). Quando si esegue questa operazione, assicurarsi di sincronizzare le nuove credenziali con l'area di lavoro e gli archivi dati connessi. Informazioni su come sincronizzare le credenziali aggiornate con [questi passaggi](how-to-change-storage-access-key.md). 

Gli esempi seguenti mostrano come registrare un contenitore BLOB di Azure, una condivisione file di Azure e Azure Data Lake Storage Gen2 come archivio dati. I parametri forniti in questi esempi sono i **parametri obbligatori** per creare e registrare un archivio dati. 

Per creare archivi dati per altri servizi di archiviazione e per informazioni sui parametri facoltativi per questi metodi, vedere la [documentazione di riferimento per i metodi `register_azure_*` applicabili](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Contenitore BLOB

Per registrare un contenitore BLOB di Azure come archivio dati, usare [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Il codice seguente crea e registra l'archivio dati `blob_datastore_name` nell'area di lavoro `ws`. Questo archivio dati accede al contenitore BLOB `my-container-name` nell'account di archiviazione `my-account-name`, usando la chiave di accesso dell'account fornita.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
Se il contenitore BLOB si trova in una rete virtuale, includere il parametro `skip_validation=True` nel metodo [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-). 

#### <a name="file-share"></a>Condivisione file

Per registrare una condivisione file di Azure come archivio dati, usare [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

Il codice seguente crea e registra l'archivio dati `file_datastore_name` nell'area di lavoro `ws`. Questo archivio dati accede alla condivisione file `my-fileshare-name` nell'account di archiviazione `my-account-name`, usando la chiave di accesso dell'account fornita.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
Se la condivisione file si trova in una rete virtuale, includere il parametro `skip_validation=True` nel metodo [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-). 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen2

Per un archivio dati Azure Data Lake Storage Gen2 (ADLS Gen2), usare [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) per registrare un archivio dati delle credenziali connesso a una risorsa di archiviazione di Azure Data Lake Storage Gen2 con [autorizzazioni dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). Per poter usare l'entità servizio, è necessario [registrare l'applicazione](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e concedere all'entità servizio l'accesso con *Ruolo con autorizzazioni di lettura per i dati dei BLOB di archiviazione*. Vedere altre informazioni sulla [configurazione del controllo di accesso per ADLS Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Il codice seguente crea e registra l'archivio dati `adlsgen2_datastore_name` nell'area di lavoro `ws`. Questo archivio dati accede al file system `test` nell'account di archiviazione `account_name` usando le credenziali dell'entità servizio fornite.

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

Creare un nuovo archivio dati in pochi passaggi in Azure Machine Learning Studio:

> [!IMPORTANT]
> Se l'account di archiviazione dati si trova in una rete virtuale, sono necessari passaggi di configurazione aggiuntivi per garantire che lo studio abbia accesso ai dati. Vedere [isolamento rete & privacy](how-to-enable-virtual-network.md#machine-learning-studio) per assicurarsi che vengano applicati i passaggi di configurazione appropriati. 

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **Archivi dati** nel riquadro sinistro in **Gestisci**.
1. Selezionare **+ Nuovo archivio dati**.
1. Completare il modulo per un nuovo archivio dati. Il modulo si aggiorna in modo intelligente in base alle selezioni effettuate per il tipo di archiviazione di Azure e il tipo di autenticazione.
  
È possibile trovare le informazioni necessarie per popolare il modulo nel [portale di Azure](https://portal.azure.com). Selezionare **Account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione da registrare. Nella pagina **Panoramica** sono disponibili informazioni quali, il nome dell'account, il contenitore e il nome della condivisione file. 

* Per gli elementi di autenticazione, ad esempio la chiave dell'account o il token di firma di accesso condiviso, passare a **Chiavi di accesso** nel riquadro **Impostazioni**. 

* Per gli elementi dell'entità servizio, come ID tenant e ID client, passare a **Registrazioni app** e selezionare l'app da usare. La pagina **Panoramica** corrispondente conterrà questi elementi. 

> [!IMPORTANT]
> Per motivi di sicurezza, potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure (chiave dell'account o token SAS). Quando si esegue questa operazione, assicurarsi di sincronizzare le nuove credenziali con l'area di lavoro e gli archivi dati connessi. Informazioni su come sincronizzare le credenziali aggiornate con [questi passaggi](how-to-change-storage-access-key.md). 

Nell'esempio seguente viene illustrato l'aspetto del form quando si crea un **archivio dati BLOB di Azure**: 
    
![Modulo per un nuovo archivio dati](media/how-to-access-data/new-datastore-form.png)

### <a name="vs-code"></a>Visual Studio Code

È possibile creare e gestire archivi dati utilizzando l'estensione Azure Machine Learning VS Code. Per altre informazioni, vedere la Guida alle procedure per la [gestione delle risorse vs code](how-to-manage-resources-vscode.md#datastores) .

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Ottenere gli archivi dati dall'area di lavoro

Per ottenere un archivio dati specifico registrato nell'area di lavoro corrente, usare il metodo statico [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) nella classe `Datastore`:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Per ottenere l'elenco degli archivi dati registrati con un'area di lavoro specifica, usare la proprietà [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) in un oggetto dell'area di lavoro:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Per ottenere l'archivio dati predefinito dell'area di lavoro, usare questa riga:

```Python
datastore = ws.get_default_datastore()
```
È anche possibile modificare l'archivio dati predefinito con il codice seguente. Questa funzionalità è supportata solo tramite l'SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Caricamento e download dei dati

I metodi [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) e [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) descritti negli esempi seguenti sono specifici delle classi [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) e [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py), per le quali presentano lo stesso funzionamento.

> [!NOTE]
> Il caricamento in archivi dati Azure Data Lake Gen2 non è al momento supportato.

### <a name="upload"></a>Caricamento

Caricare una directory o singoli file nell'archivio dati tramite Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Il parametro `target_path` specifica il percorso nella condivisione file (o nel contenitore BLOB) da caricare. Il valore predefinito è `None`, quindi i dati vengono caricati nella radice. Se `overwrite=True`, i dati esistenti in `target_path` vengono sovrascritti.

È anche possibile caricare un elenco di singoli file nell'archivio dati tramite il metodo `upload_files()`.

### <a name="download"></a>Download

Scaricare i dati da un archivio dati nel file system locale:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

Il parametro `target_path` rappresenta la posizione della directory locale in cui scaricare i dati. Per specificare un percorso della cartella o condivisione file (o contenitore Blob) per il download, fornire tale percorso a `prefix`. Se `prefix` è `None`, viene scaricato tutto il contenuto della condivisione file (o del contenitore BLOB).

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Accedere ai dati durante il training

Per interagire con i dati negli archivi dati o per inserire i dati in un pacchetto creando un oggetto utilizzabile per le attività di Machine Learning, ad esempio il training, [creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md). I set di dati forniscono funzioni per il caricamento dei dati tabulari in un dataframe Pandas o Spark. I set di dati consentono inoltre di scaricare o montare file di qualsiasi formato da Archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e Database di Azure per PostgreSQL. [Vedere altre informazioni su come eseguire il training con i set di dati](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Accesso al codice sorgente durante il training

Archiviazione BLOB di Azure offre velocità effettive più elevate rispetto a una condivisione file di Azure e la scalabilità per avviare in parallelo un numero elevato di processi. Per questo motivo, è consigliabile configurare le esecuzioni in modo da usare l'archiviazione BLOB per il trasferimento di file di codice sorgente.

L'esempio di codice seguente specifica nella configurazione di esecuzione quale archivio dati BLOB usare per i trasferimenti del codice sorgente.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Accedere ai dati durante l'assegnazione dei punteggi

Azure Machine Learning offre diversi modi per usare i modelli per l'assegnazione dei punteggi. Alcuni di questi metodi non forniscono l'accesso agli archivi dati. Usare la tabella seguente per informazioni sui metodi che consentono di accedere agli archivi dati durante l'assegnazione dei punteggi:

| Metodo | Accesso all'archivio dati | Descrizione |
| ----- | :-----: | ----- |
| [Stima in batch](how-to-use-parallel-run-step.md) | ✔ | Eseguire stime su grandi quantità di dati in modo asincrono. |
| [Servizio Web](how-to-deploy-and-where.md) | &nbsp; | Distribuire modelli come servizi Web. |
| [Modulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Distribuire modelli nei dispositivi IoT Edge. |

Per le situazioni in cui l'SDK non fornisce l'accesso agli archivi dati, è possibile creare codice personalizzato usando la soluzione Azure SDK pertinente per accedere ai dati. [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python), ad esempio, è una libreria client che è possibile usare per accedere ai dati archiviati in BLOB o file.


## <a name="access-data-in-a-virtual-network"></a>Accedere ai dati in una rete virtuale

Se lo spazio di archiviazione è dietro una rete virtuale, è necessario eseguire passaggi di configurazione aggiuntivi per l'area di lavoro e l'archivio dati per accedere ai dati. Per altre informazioni su come usare gli archivi dati e i set di dati in una rete virtuale, vedere [isolamento rete durante il training & inferenza con reti virtuali private](how-to-enable-virtual-network.md#use-datastores-and-datasets).

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Spostare i dati in soluzioni di archiviazione di Azure supportate

Azure Machine Learning supporta l'accesso ai dati da Archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e Database di Azure per PostgreSQL. Se si usa una soluzione di archiviazione non supportata, è consigliabile spostare i dati in soluzioni di archiviazione di Azure supportate usando [Azure Data Factory e questi passaggi](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Lo spostamento dei dati in una soluzione di archiviazione supportata può consentire di risparmiare sui costi relativi al traffico in uscita durante gli esperimenti di Machine Learning. 

Azure Data Factory consente un trasferimento dei dati efficiente e resiliente con più di 80 connettori predefiniti senza costi aggiuntivi. Questi connettori includono i servizi dati di Azure, le origini dati locali, Amazon S3 Redshift, nonché Google BigQuery.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md)
* [Eseguire il training di un modello](how-to-train-ml-models.md)
* [Distribuire un modello](how-to-deploy-and-where.md)
