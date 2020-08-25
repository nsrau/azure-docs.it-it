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
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: c5200214946b52ce974a8b7557e38eb57481028a
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782992"
---
# <a name="connect-to-azure-storage-services"></a>Connettersi ai servizi di archiviazione di Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come **connettersi ai servizi di archiviazione di Azure tramite Azure Machine Learning archivi dati**. Gli archivi dati si connettono in modo sicuro al servizio di archiviazione di Azure senza inserire le credenziali di autenticazione e l'integrità dell'origine dati originale a rischio. Le informazioni di connessione vengono archiviate, ad esempio l'ID sottoscrizione e l'autorizzazione token nell' [Key Vault](https://azure.microsoft.com/services/key-vault/) associata all'area di lavoro, in modo da poter accedere in modo sicuro alla risorsa di archiviazione senza che sia necessario codificarli negli script. Per creare e registrare archivi dati, è possibile usare [Azure Machine Learning Python SDK](#python) o [Azure Machine Learning Studio](#studio) .

Se si preferisce creare e gestire archivi dati utilizzando l'estensione Azure Machine Learning VS Code; per altre informazioni, vedere la Guida alle procedure per la [gestione delle risorse vs code](how-to-manage-resources-vscode.md#datastores) .

È possibile creare archivi dati da [queste soluzioni di archiviazione di Azure](#matrix). **Per le soluzioni di archiviazione non supportate**e per risparmiare i costi di uscita durante gli esperimenti di ml, [spostare i dati](#move) in una soluzione di archiviazione di Azure supportata.  

Per informazioni sul ruolo degli archivi dati nel flusso di lavoro generale di accesso ai dati di Azure Machine Learning, vedere l'articolo [Proteggere l'accesso ai dati](concept-data.md#data-workflow).

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:
- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Un account di archiviazione di Azure con un [tipo di archiviazione supportato](#matrix).

- [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) o accesso a [Azure Machine Learning Studio](https://ml.azure.com/).

- Un'area di lavoro di Azure Machine Learning.
  
  [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) o usarne una esistente tramite Python SDK. 

    Importare le classi `Workspace` e `Datastore` e caricare le informazioni sulla sottoscrizione dal file `config.json` usando la funzione `from_config()`. Per impostazione predefinita, il file JSON viene cercato nella directory corrente, ma è possibile anche specificare un parametro del percorso che punti al file usando `from_config(path="your/file/path")`.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Quando si crea un'area di lavoro, un contenitore BLOB di Azure e una condivisione file di Azure vengono registrati automaticamente come archivi dati nell'area di lavoro. A questi due elementi vengono assegnati, rispettivamente, i nomi `workspaceblobstore` e `workspacefilestore`. `workspaceblobstore`Viene usato per archiviare gli elementi dell'area di lavoro e i log dell'esperimento di machine learning. Viene anche impostato come **archivio dati predefinito** e non può essere eliminato dall'area di lavoro. Il `workspacefilestore` viene usato per archiviare notebook e script R autorizzati tramite l' [istanza di calcolo](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files).
    
    > [!NOTE]
    > La finestra di progettazione di Azure Machine Learning (anteprima) crea automaticamente un archivio dati denominato **azureml_globaldatasets** quando si apre un campione nella home page della finestra di progettazione. Questo archivio dati contiene solo i set di dati del campione. **Non** usare questo archivio dati per l'accesso ai dati riservati.

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

## <a name="storage-access-and-permissions"></a>Accesso e autorizzazioni di archiviazione

Per garantire la connessione sicura al servizio di archiviazione di Azure, Azure Machine Learning necessario avere l'autorizzazione per accedere al contenitore di archiviazione dati corrispondente. Questo accesso dipende dalle credenziali di autenticazione usate per registrare l'archivio dati. 

### <a name="virtual-network"></a>Rete virtuale 

Se l'account di archiviazione dati si trova in una **rete virtuale**, sono necessari passaggi di configurazione aggiuntivi per garantire che Azure Machine Learning abbia accesso ai dati. Vedere [isolamento rete & privacy](how-to-enable-virtual-network.md#machine-learning-studio) per assicurarsi che vengano applicati i passaggi di configurazione appropriati quando si crea e si registra l'archivio dati.  

### <a name="access-validation"></a>Convalida dell'accesso

**Come parte del processo di creazione e registrazione dell'archivio dati iniziale**, Azure Machine Learning convalida automaticamente il servizio di archiviazione sottostante e l'entità fornita dall'utente (nome utente, entità servizio o token SAS) ha accesso alla risorsa di archiviazione specificata.

**Dopo la creazione dell'archivio dati**, questa convalida viene eseguita solo per i metodi che richiedono l'accesso al contenitore di archiviazione sottostante, **non** ogni volta che vengono recuperati gli oggetti dell'archivio dati. La convalida viene ad esempio eseguita se è necessario scaricare i file dall'archivio dati, mentre se si vuole solo modificare l'archivio dati predefinito, la convalida non viene eseguita.

Per autenticare l'accesso al servizio di archiviazione sottostante, è possibile specificare la chiave dell'account, le firme di accesso condiviso (SAS) o l'entità servizio nel metodo corrispondente `register_azure_*()` del tipo di archivio dati che si vuole creare. Nella [matrice del tipo di archiviazione](#matrix) sono elencati i tipi di autenticazione supportati che corrispondono a ogni tipo di archivio dati.

È possibile trovare la chiave dell'account, il token SAS e le informazioni sull'entità servizio nel [portale di Azure](https://portal.azure.com).

* Se si prevede di usare una chiave dell'account o un token di firma di accesso condiviso per l'autenticazione, selezionare **Account di archiviazione** nel riquadro sinistro e scegliere l'account di archiviazione da registrare. 
  * La pagina **Panoramica** fornisce informazioni come il nome dell'account, il contenitore e il nome della condivisione file. 
      1. Per le chiavi dell'account, passare a **Chiavi di accesso** nel riquadro **Impostazioni**. 
      1. Per i token di firma di accesso condiviso, passare a **Shared access signatures** (Firme di accesso condiviso) nel riquadro **Impostazioni**.

* Se si prevede di usare un'entità servizio per l'autenticazione, passare alla **registrazioni app** e selezionare l'app che si vuole usare. 
    * La pagina di **Panoramica** corrispondente conterrà informazioni obbligatorie come ID tenant e ID client.

> [!IMPORTANT]
> Per motivi di sicurezza, potrebbe essere necessario modificare le chiavi di accesso per un account di archiviazione di Azure (chiave dell'account o token SAS). Quando si esegue questa operazione, assicurarsi di sincronizzare le nuove credenziali con l'area di lavoro e gli archivi dati connessi. Informazioni su come sincronizzare le credenziali aggiornate con [questi passaggi](how-to-change-storage-access-key.md). 

### <a name="permissions"></a>Autorizzazioni

Per il contenitore BLOB di Azure e l'archiviazione Azure Data Lake generazione 2, assicurarsi che le credenziali di autenticazione dispongano dell'accesso in **lettura dati BLOB di archiviazione** . Altre informazioni sul [lettore di dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). 

<a name="python"></a>

## <a name="create-and-register-datastores-via-the-sdk"></a>Creare e registrare archivi dati tramite l'SDK

Quando si registra una soluzione di archiviazione di Azure come archivio dati, l'archivio dati viene creato e registrato automaticamente in un'area di lavoro specifica. Per informazioni su dove trovare le credenziali di autenticazione necessarie, vedere la sezione relativa alle [autorizzazioni di accesso all'archiviazione &](#storage-access-and-permissions) .

In questa sezione sono riportati alcuni esempi di come creare e registrare un archivio dati tramite Python SDK per i tipi di archiviazione seguenti. I parametri forniti in questi esempi sono i **parametri obbligatori** per creare e registrare un archivio dati.

* [Contenitore BLOB di Azure](#azure-blob-container)
* [Condivisione file di Azure](#azure-file-share)
* [Azure Data Lake Storage Gen2](#azure-data-lake-storage-generation-2)

 Per creare archivi dati per altri servizi di archiviazione supportati, vedere la [documentazione di riferimento relativa ai `register_azure_*` metodi applicabili](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

Se si preferisce un'esperienza di codice ridotta, vedere [creare archivi dati in Azure Machine Learning Studio](#studio).

> [!NOTE]
> Il nome dell'archivio dati deve essere costituito solo da lettere minuscole, cifre e caratteri di sottolineatura. 

### <a name="azure-blob-container"></a>Contenitore BLOB di Azure

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

### <a name="azure-file-share"></a>Condivisione file di Azure

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen2

Per un archivio dati Azure Data Lake Storage Gen2 (ADLS Gen2), usare [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) per registrare un archivio dati delle credenziali connesso a una risorsa di archiviazione di Azure Data Lake Storage Gen2 con [autorizzazioni dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). 

Per usare l'entità servizio, è necessario [registrare l'applicazione](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) e concedere all'entità servizio l'accesso in **lettura dati BLOB di archiviazione** . Vedere altre informazioni sulla [configurazione del controllo di accesso per ADLS Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

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

<a name="studio"></a>


## <a name="create-datastores-in-the-studio"></a>Creare archivi dati in studio 


Creare un nuovo archivio dati in pochi passaggi con il Azure Machine Learning Studio.

> [!IMPORTANT]
> Se l'account di archiviazione dati si trova in una rete virtuale, sono necessari passaggi di configurazione aggiuntivi per garantire che lo studio abbia accesso ai dati. Vedere [isolamento rete & privacy](how-to-enable-virtual-network.md#machine-learning-studio) per assicurarsi che vengano applicati i passaggi di configurazione appropriati. 

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selezionare **Archivi dati** nel riquadro sinistro in **Gestisci**.
1. Selezionare **+ Nuovo archivio dati**.
1. Completare il modulo per un nuovo archivio dati. Il modulo si aggiorna in modo intelligente in base alle selezioni effettuate per il tipo di archiviazione di Azure e il tipo di autenticazione. Vedere la [sezione accesso e autorizzazioni di archiviazione](#access-validation) per informazioni su dove trovare le credenziali di autenticazione necessarie per popolare il modulo.

Nell'esempio seguente viene illustrato l'aspetto del form quando si crea un **archivio dati BLOB di Azure**: 
    
![Modulo per un nuovo archivio dati](media/how-to-access-data/new-datastore-form.png)

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Usare i dati negli archivi dati

Dopo aver creato un archivio dati, [creare un set](how-to-create-register-datasets.md) di dati Azure Machine Learning per interagire con i dati. I set di dati impacchettano i dati in un oggetto utilizzabile differito per le attività di Machine Learning, ad esempio il training. Offrono inoltre la possibilità di [scaricare o montare](how-to-train-with-datasets.md#mount-vs-download) file di qualsiasi formato da servizi di archiviazione di Azure, ad esempio archiviazione BLOB di Azure e ADLS generazione 2. È anche possibile usarli per caricare dati tabulari in un frame di dati Pandas o Spark.

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

## <a name="access-data-during-scoring"></a>Accedere ai dati durante l'assegnazione dei punteggi

Azure Machine Learning offre diversi modi per usare i modelli per l'assegnazione dei punteggi. Alcuni di questi metodi non forniscono l'accesso agli archivi dati. Usare la tabella seguente per informazioni sui metodi che consentono di accedere agli archivi dati durante l'assegnazione dei punteggi:

| Metodo | Accesso all'archivio dati | Descrizione |
| ----- | :-----: | ----- |
| [Stima in batch](how-to-use-parallel-run-step.md) | ✔ | Eseguire stime su grandi quantità di dati in modo asincrono. |
| [Servizio Web](how-to-deploy-and-where.md) | &nbsp; | Distribuire modelli come servizi Web. |
| [Modulo Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Distribuire modelli nei dispositivi IoT Edge. |

Per le situazioni in cui l'SDK non fornisce l'accesso agli archivi dati, è possibile creare codice personalizzato usando la soluzione Azure SDK pertinente per accedere ai dati. [Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python), ad esempio, è una libreria client che è possibile usare per accedere ai dati archiviati in BLOB o file.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Spostare i dati in soluzioni di archiviazione di Azure supportate

Azure Machine Learning supporta l'accesso ai dati da Archiviazione BLOB di Azure, File di Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, database SQL di Azure e Database di Azure per PostgreSQL. Se si usa una soluzione di archiviazione non supportata, è consigliabile spostare i dati in soluzioni di archiviazione di Azure supportate usando [Azure Data Factory e questi passaggi](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Lo spostamento dei dati in una soluzione di archiviazione supportata può consentire di risparmiare sui costi relativi al traffico in uscita durante gli esperimenti di Machine Learning. 

Azure Data Factory consente un trasferimento dei dati efficiente e resiliente con più di 80 connettori predefiniti senza costi aggiuntivi. Questi connettori includono i servizi dati di Azure, le origini dati locali, Amazon S3 Redshift, nonché Google BigQuery.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md)
* [Eseguire il training di un modello](how-to-train-ml-models.md)
* [Distribuire un modello](how-to-deploy-and-where.md)
