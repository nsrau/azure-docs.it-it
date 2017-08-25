---
title: 'Esercitazione: Usare Azure Batch SDK per Python | Documentazione Microsoft'
description: Apprendere i concetti di base di Azure Batch e creare una soluzione semplice mediante Python.
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: bd5a977c10d3955639beb893cd7a37581b14f7c0
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Introduzione all'SDK di Batch per Python

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Questo articolo illustra i concetti di base relativi ad [Azure Batch][azure_batch] e al client [Python di Batch][py_azure_sdk] esaminando una piccola applicazione Batch scritta in Python. Verrà illustrato il modo in cui due script di esempio usano il servizio Batch per elaborare un carico di lavoro parallelo in macchine virtuali Linux nel cloud e come interagiscono con [Archiviazione di Azure](../storage/common/storage-introduction.md) per lo staging e il recupero di file. Saranno disponibili informazioni su un flusso di lavoro comune dell'applicazione Batch e sui principali componenti di Batch, ad esempio processi, attività, pool e nodi di calcolo.

![Flusso di lavoro della soluzione Batch (di base)][11]<br/>

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente sappia usare Python e Linux e possa soddisfare i requisiti di creazione dell'account specificati di seguito per Azure e per i servizi Batch e di archiviazione.

### <a name="accounts"></a>Account
* **Account Azure**: se non si ha già una sottoscrizione di Azure, [creare un account Azure gratuito][azure_free_account].
* **Account Batch**: dopo aver creato una sottoscrizione di Azure, [creare un account Azure Batch](batch-account-create-portal.md).
* **Account di archiviazione**: vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) in [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).

### <a name="code-sample"></a>Esempio di codice
L'[esempio di codice][github_article_samples] Python usato nell'esercitazione è uno dei molti esempi di codice Batch disponibili nel repository [azure-batch-samples][github_samples] in GitHub. È possibile scaricare tutti gli esempi facendo clic su **Clone or download > Download ZIP** (Clona o scarica > Scarica ZIP) nella home page del repository oppure facendo clic sul collegamento di download diretto [azure-batch-samples-master.zip][github_samples_zip]. Dopo aver estratto il contenuto del file ZIP, i due script per questa esercitazione sono disponibili nella directory `article_samples` :

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Ambiente Python
Per eseguire lo script di esempio *python_tutorial_client.py* nella workstation locale è necessario un **interprete Python** compatibile con la versione **2.7** o **3.3+**. Lo script è stato verificato in ambiente Linux e Windows.

### <a name="cryptography-dependencies"></a>Dipendenze di cryptography
È necessario installare le dipendenze per la libreria [cryptography][crypto], richieste dai pacchetti Python `azure-batch` e `azure-storage`. Eseguire una di queste operazioni appropriate per la piattaforma o vedere i dettagli dell'[installazione di cryptography][crypto_install] per altre informazioni:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-devel libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Se si esegue l'installazione per Python 3.3+ in Linux, usare gli equivalenti di python3 per le dipendenze di Python. Ad esempio, in Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Pacchetti di Azure
Installare poi i pacchetti Python per **Azure Batch** e **Archiviazione di Azure**. È possibile installare entrambi i pacchetti usando **pip** e *requirements.txt* disponibili qui:

`/azure-batch-samples/Python/Batch/requirements.txt`

Per installare i pacchetti per Batch e Archiviazione, eseguire il comando **pip** seguente:

`pip install -r requirements.txt`

In alternativa, è possibile installare i pacchetti Python [azure-batch][pypi_batch] e [azure-storage][pypi_storage] manualmente:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Se si una un account senza privilegi, può essere necessario anteporre `sudo` ai comandi. Ad esempio: `sudo pip install -r requirements.txt`. Per altre informazioni sull'installazione dei pacchetti Python, vedere [Installing Packages][pypi_install] (Installazione di pacchetti) in python.org.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Esempio di codice per l'esercitazione di Batch Python
L'esempio di codice per l'esercitazione di Batch Python è costituito da due script Python e alcuni file di dati.

* **python_tutorial_client.py**: interagisce con i servizi Batch e Archiviazione per eseguire un carico di lavoro parallelo nei nodi di calcolo (macchine virtuali). Lo script *python_tutorial_client.py* viene eseguito nella workstation locale.
* **python_tutorial_task.py**: script eseguito nei nodi di calcolo in Azure per completare le operazioni effettive. Nell'esempio *python_tutorial_task.py* analizza il testo in un file scaricato da Archiviazione di Azure (file di input). Produce quindi un file di testo (file di output) che contiene un elenco delle prime tre parole visualizzate nel file di input. Dopo aver creato il file di output, *python_tutorial_task.py* carica il file in Archiviazione di Azure. Questo lo rende disponibile per il download nello script client in esecuzione nella workstation. Lo script *python_tutorial_task.py* viene eseguito in parallelo in più nodi di calcolo nel servizio Batch.
* **./data/taskdata\*.txt**: questi tre file di testo forniscono l'input per le attività eseguite nei nodi di calcolo.

Il diagramma seguente illustra le operazioni principali eseguite dagli script client e attività. Questo flusso di lavoro di base è tipico di molte soluzioni di calcolo create con Batch. Anche se non illustra ogni funzionalità disponibile nel servizio Batch, quasi tutti gli scenari di Batch includono parti di questo flusso di lavoro.

![Flusso di lavoro dell'esempio di Batch][8]<br/>

[**Passaggio 1.**](#step-1-create-storage-containers) Creare **contenitori** nell'archivio BLOB di Azure.<br/>
[**Passaggio 2.**](#step-2-upload-task-script-and-data-files) Caricare lo script attività e i file di input nei contenitori.<br/>
[**Passaggio 3.**](#step-3-create-batch-pool) Creare un **pool** di Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** L'attività **StartTask** del pool scarica lo script attività (python_tutorial_task.py) nei nodi quando questi vengono aggiunti al pool.<br/>
[**Passaggio 4.**](#step-4-create-batch-job) Creare un **processo** di Batch.<br/>
[**Passaggio 5.**](#step-5-add-tasks-to-job) Aggiungere **attività** al processo.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Viene pianificata l'esecuzione delle attività nei nodi.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Ogni attività scarica i rispettivi dati di input da Archiviazione di Azure e quindi avvia l'esecuzione.<br/>
[**Passaggio 6.**](#step-6-monitor-tasks) Monitorare le attività.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** Dopo il completamento, le attività caricano i rispettivi dati di output in Archiviazione di Azure.<br/>
[**Passaggio 7.**](#step-7-download-task-output) Scaricare l'output delle attività dal servizio di archiviazione.

Come indicato, non tutte le soluzioni Batch eseguono esattamente questi passaggi e potrebbero includerne molti altri, ma l'esempio illustra i processi comuni presenti in una soluzione Batch.

## <a name="prepare-client-script"></a>Preparare lo script client
Prima di eseguire l'esempio, aggiungere le credenziali dell'account Batch e dell'account di archiviazione a *python_tutorial_client.py*. Se non è già stato fatto, aprire il file in un editor qualsiasi e aggiornare le righe seguenti con le credenziali.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
BATCH_ACCOUNT_NAME = ""
BATCH_ACCOUNT_KEY = ""
BATCH_ACCOUNT_URL = ""

# Storage account credentials
STORAGE_ACCOUNT_NAME = ""
STORAGE_ACCOUNT_KEY = ""
```

Le credenziali dell'account Batch e dell'account di archiviazione sono disponibili nel pannello dell'account di ogni servizio nel [portale di Azure][azure_portal]:

![Credenziali di Batch nel portale][9]
![Credenziali di archiviazione nel portale][10]<br/>

Nelle sezioni seguenti verranno analizzati i passaggi usati dagli script per elaborare un carico di lavoro nel servizio Batch. È consigliabile fare sempre riferimento agli script nell'editor durante la lettura dell'articolo.

Passare alla riga seguente in **python_tutorial_client.py** per iniziare con il passaggio 1:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>Passaggio 1: Creare contenitori di archiviazione
![Creare contenitori in Archiviazione di Azure][1]
<br/>

Batch include il supporto predefinito per l'interazione con Archiviazione di Azure. I contenitori nell'account di archiviazione forniranno i file necessari per le attività eseguite nell'account Batch, oltre a una posizione in cui archiviare i dati di output prodotti. La prima operazione eseguita dallo script *python_tutorial_client.py* consiste nel creare tre contenitori nell'[archivio BLOB di Azure](../storage/common/storage-introduction.md#blob-storage):

* **application**: in questo contenitore verrà archiviato lo script Python eseguito dalle attività, *python_tutorial_task.py*.
* **input**: le attività scaricheranno i file di dati da elaborare dal contenitore *input* .
* **output**: dopo aver completato l'elaborazione dei file di input, le attività caricheranno i risultati nel contenitore *output* .

Per interagire con un account di archiviazione e creare contenitori, viene usato il pacchetto [azure-storage][pypi_storage] per creare un oggetto [BlockBlobService][py_blockblobservice], ovvero il "client BLOB". Verranno quindi creati tre contenitori nell'account di archiviazione usando il client BLOB.

```python
import azure.storage.blob as azureblob

# Create the blob client, for use in obtaining references to
# blob storage containers and uploading files to containers.
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)

# Use the blob client to create the containers in Azure Storage if they
# don't yet exist.
APP_CONTAINER_NAME = 'application'
INPUT_CONTAINER_NAME = 'input'
OUTPUT_CONTAINER_NAME = 'output'
blob_client.create_container(APP_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(INPUT_CONTAINER_NAME, fail_on_exist=False)
blob_client.create_container(OUTPUT_CONTAINER_NAME, fail_on_exist=False)
```

Dopo la creazione dei contenitori, l'applicazione può caricare i file che verranno usati dalle attività.

> [!TIP]
> [Come usare l'archiviazione BLOB di Azure da Python](../storage/blobs/storage-python-how-to-use-blob-storage.md) offre utili informazioni generali sull'uso dei contenitori e dei BLOB di Archiviazione di Azure, quindi è consigliabile prenderne visione quando si inizia a usare Batch.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>Passaggio 2: Caricare lo script attività e i file di dati
![Caricare l'applicazione dell'attività e i file di input (dati) nei contenitori][2]
<br/>

Nell'operazione di caricamento dei file *python_tutorial_client.py* definisce prima di tutto le raccolte dei percorsi di file di **application** e **input** esistenti nel computer locale, quindi carica i file nei contenitori creati nel passaggio precedente.

```python
# Paths to the task script. This script will be executed by the tasks that
# run on the compute nodes.
application_file_paths = [os.path.realpath('python_tutorial_task.py')]

# The collection of data files that are to be processed by the tasks.
input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                    os.path.realpath('./data/taskdata2.txt'),
                    os.path.realpath('./data/taskdata3.txt')]

# Upload the application script to Azure Storage. This is the script that
# will process the data files, and is executed by each of the tasks on the
# compute nodes.
application_files = [
    upload_file_to_container(blob_client, APP_CONTAINER_NAME, file_path)
    for file_path in application_file_paths]

# Upload the data files. This is the data that will be processed by each of
# the tasks executed on the compute nodes in the pool.
input_files = [
    upload_file_to_container(blob_client, INPUT_CONTAINER_NAME, file_path)
    for file_path in input_file_paths]
```

Con la comprensione di lista, la funzione `upload_file_to_container` viene chiamata per ogni file presente nelle raccolte e vengono popolate due raccolte [ResourceFile][py_resource_file]. La funzione `upload_file_to_container` è riportata di seguito:

```python
def upload_file_to_container(block_blob_client, container_name, path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """

    import datetime
    import azure.storage.blob as azureblob
    import azure.batch.models as batchmodels

    blob_name = os.path.basename(path)

    print('Uploading file {} to container [{}]...'.format(path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
Un oggetto [ResourceFile][py_resource_file] fornisce alle attività in Batch l'URL di un file in Archiviazione di Azure che verrà scaricato in un nodo di calcolo prima dell'esecuzione dell'attività. La proprietà [ResourceFile][py_resource_file].**blob_source** specifica l'URL completo del file esistente in Archiviazione di Azure, che può includere anche una firma di accesso condiviso che fornisce l'accesso sicuro al file. La maggior parte dei tipi di attività in Batch include una proprietà *ResourceFiles*, ad esempio:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

Questo esempio non usa i tipi di attività JobPreparationTask o JobReleaseTask, ma altre informazioni in merito sono disponibili in [Eseguire attività di preparazione e completamento di processi in nodi di calcolo di Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Firma di accesso condiviso
Le firme di accesso condiviso sono stringhe che consentono l'accesso sicuro a contenitori e BLOB in Archiviazione di Azure. Lo script *python_tutorial_client.py* usa firme di accesso condiviso per BLOB e contenitori e illustra come ottenere queste stringhe di firma di accesso condiviso dal servizio Archiviazione.

* **Firme di accesso condiviso per BLOB**: l'attività StartTask del pool usa le firme di accesso condiviso dei BLOB durante il download dello script attività e dei file di dati di input da Archiviazione, come illustrato più avanti nel [passaggio 3](#step-3-create-batch-pool) . La funzione `upload_file_to_container` in *python_tutorial_client.py* contiene il codice che ottiene la firma di accesso condiviso di ogni BLOB. L'operazione viene eseguita chiamando [BlockBlobService.make_blob_url][py_make_blob_url] nel modulo di archiviazione.
* **Firma di accesso condiviso per contenitori**: quando completa le operazioni sul nodo di calcolo, ogni attività carica il rispettivo file di output nel contenitore *output* in Archiviazione di Azure. A tale scopo, *python_tutorial_task.py* usa una firma di accesso condiviso del contenitore che fornisce l'accesso in scrittura al contenitore stesso. La funzione `get_container_sas_token` in *python_tutorial_client.py* ottiene la firma di accesso condiviso del contenitore, che viene quindi passata alle attività come argomento della riga di comando. Il passaggio 5, [Aggiungere attività a un processo](#step-5-add-tasks-to-job), illustra l'utilizzo della firma di accesso condiviso del contenitore.

> [!TIP]
> Per altre informazioni su come fornire un accesso sicuro ai dati nell'account di archiviazione, vedere la serie in due parti sulle firme di accesso condiviso, [Parte 1: Informazioni sul modello di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) e [Parte 2: Creare e usare una firma di accesso condiviso con il servizio BLOB](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md).
>
>

## <a name="step-3-create-batch-pool"></a>Passaggio 3: Creare un pool di Batch
![Creare un pool di Batch][3]
<br/>

Un **pool** di Batch è una raccolta di nodi di calcolo (macchine virtuali) in cui Batch esegue le attività di un processo.

Dopo il caricamento dello script attività e dei file di dati nell'account di archiviazione, *python_tutorial_client.py* avvia l'interazione con il servizio Batch usando il modulo Batch Python. A tale scopo viene creato un oggetto [BatchServiceClient][py_batchserviceclient]:

```python
# Create a Batch service client. We'll now be interacting with the Batch
# service in addition to Storage.
credentials = batchauth.SharedKeyCredentials(BATCH_ACCOUNT_NAME,
                                             BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL)
```

Viene quindi creato un pool di nodi di calcolo nell'account Batch con una chiamata a `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Quando si crea un pool, si definisce un elemento [PoolAddParameter][py_pooladdparam] che specifica diverse proprietà per il pool:

* **ID** del pool (*id*: obbligatorio)<p/>Così come la maggior parte delle entità in Batch, il nuovo pool deve avere un ID univoco all'interno dell'account Batch. Il codice fa riferimento a questo pool usando il relativo ID, che costituisce anche il modo con cui si identifica il pool nel [portale][azure_portal] di Azure.
* **Numero di nodi di calcolo** (*target_dedicated*: obbligatorio)<p/>Questa proprietà specifica il numero di macchine virtuali da distribuire nel pool. È importante notare che tutti gli account Batch hanno una **quota** predefinita che limita il numero di **core** e di conseguenza di nodi di calcolo in un account Batch. Le quote predefinite e le istruzioni su come [aumentare una quota](batch-quota-limit.md#increase-a-quota), ad esempio il numero massimo di core nell'account Batch, sono riportate in [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md). Se il pool non raggiunge più di X nodi, ad esempio, questa quota di core può essere la causa.
* **Sistema operativo** per i nodi (*virtual_machine_configuration* **o** *cloud_service_configuration*: obbligatorio)<p/>In *python_tutorial_client.py* viene creato un pool di nodi Linux tramite [VirtualMachineConfiguration][py_vm_config]. La funzione `select_latest_verified_vm_image_with_node_agent_sku` in `common.helpers` semplifica l'uso delle immagini di [Marketplace per Macchine virtuali di Azure][vm_marketplace]. Per altre informazioni sull'uso delle immagini del Marketplace, vedere [Effettuare il provisioning di nodi di calcolo Linux nei pool di Azure Batch](batch-linux-nodes.md) .
* **Dimensione dei nodi di calcolo** (*vm_size*: obbligatorio)<p/>Dato che vengono specificati nodi Linux per [VirtualMachineConfiguration][py_vm_config], viene specificata una dimensione di VM, in questo esempio `STANDARD_A1`, in base a quanto descritto in [Dimensioni delle macchine virtuali in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per altre informazioni, vedere [Effettuare il provisioning di nodi di calcolo Linux nei pool di Azure Batch](batch-linux-nodes.md) .
* **Attività di avvio** (*start_task*: non obbligatorio)<p/>Oltre alle proprietà relative ai nodi fisici sopra descritte, è possibile specificare anche un elemento [StartTask][py_starttask] per il pool, ma non è obbligatorio. L'attività StartTask viene eseguita in ogni nodo quando questo viene aggiunto al pool e ogni volta che viene riavviato. StartTask è particolarmente utile per preparare i nodi di calcolo per l'esecuzione di attività, ad esempio per installare le applicazioni che vengono eseguite dalle attività.<p/>In questa applicazione di esempio StartTask copia i file scaricati dal servizio di archiviazione, specificati usando la proprietà **resource_files** di StartTask, dalla *directory di lavoro* di StartTask alla directory *condivisa* a cui possono accedere tutte le attività in esecuzione sul nodo. Sostanzialmente, viene copiato `python_tutorial_task.py` nella directory condivisa in ogni nodo quando questo viene aggiunto al pool, in modo che qualsiasi attività in esecuzione nel nodo possa accedervi.

Si noti la chiamata alla funzione helper `wrap_commands_in_shell` . Questa funzione acquisisce una raccolta di comandi separati e crea una singola riga di comando appropriata per la proprietà della riga di comando dell'attività.

Nel frammento di codice precedente si può notare anche l'uso di due variabili di ambiente nella proprietà **command_line** di StartTask: `AZ_BATCH_TASK_WORKING_DIR` e `AZ_BATCH_NODE_SHARED_DIR`. Ogni nodo di calcolo in un pool di Batch viene configurato automaticamente con diverse variabili di ambiente specifiche per Batch. Tutti processi eseguiti da un'attività possono accedere a queste variabili di ambiente.

> [!TIP]
> Per altre informazioni sulle variabili di ambiente disponibili nei nodi di calcolo di un pool di Batch, oltre a informazioni sulle directory di lavoro delle attività, vedere **Impostazioni di ambiente per le attività** e **File e directory** in [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Passaggio 4: Creare un processo di Batch
![Creare un processo di Batch][4]<br/>

Un **processo** di Batch è una raccolta di attività ed è associato a un pool di nodi di calcolo. Le attività in un processo vengono eseguite nei nodi di calcolo del pool associato.

È possibile usare un processo non solo per organizzare le attività nei carichi di lavoro correlati e tenerne traccia, ma anche per imporre determinati vincoli, ad esempio il tempo di esecuzione massimo per il processo e, per estensione, per le rispettive attività, nonché per imporre la priorità dei processi rispetto ad altri nell'account Batch. In questo esempio, tuttavia, il processo viene associato solo al pool creato nel Passaggio 3. Non vengono configurate proprietà aggiuntive.

Tutti i processi di Batch sono associati a un pool specifico. Questa associazione indica i nodi in cui verranno eseguite le attività del processo. Il pool viene specificato con la proprietà [PoolInformation][py_poolinfo], come illustrato nel frammento di codice seguente.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Dopo la creazione di un processo, vengono aggiunte attività per l'esecuzione delle operazioni.

## <a name="step-5-add-tasks-to-job"></a>Passaggio 5: Aggiungere attività a un processo
![Aggiungere attività a un processo][5]<br/>
*(1) Le attività vengono aggiunte al processo, (2) viene pianificata l'esecuzione delle attività nei nodi e (3) le attività scaricano i file di dati da elaborare*

Le **attività** di Batch sono le singole unità di lavoro eseguite nei nodi di calcolo. Un'attività ha una riga di comando ed esegue gli script o i file eseguibili specificati in questa riga di comando.

Per eseguire effettivamente le operazioni, è necessario aggiungere attività a un processo. Ogni elemento [CloudTask][py_task] viene configurato con una proprietà della riga di comando e, analogamente all'attività StartTask del pool, con oggetti [ResourceFiles][py_resource_file] scaricati dall'attività nel nodo prima dell'esecuzione automatica della rispettiva riga di comando. Nell'esempio, ogni attività elabora un solo file. Di conseguenza, la rispettiva raccolta ResourceFiles contiene un singolo elemento.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> Quando accedono a variabili di ambiente come `$AZ_BATCH_NODE_SHARED_DIR` o eseguono un'applicazione non presente nell'elemento `PATH` del nodo, le righe di comando delle attività devono richiamare la shell in modo esplicito, ad esempio con `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Questo requisito è superfluo se le attività eseguono un'applicazione nell'elemento `PATH` del nodo e non fanno riferimento a variabili di ambiente.
>
>

Nel ciclo `for` del frammento di codice precedente, la riga di comando per l'attività è costruita con cinque argomenti della riga di comando che vengono passati a *python_tutorial_task.py*:

1. **filepath**: percorso locale del file esistente nel nodo. Durante la creazione dell'oggetto ResourceFile in `upload_file_to_container` nel precedente passaggio 2, per questa proprietà (parametro `file_path` nel costruttore ResourceFile) è stato usato il nome file. Ciò indica che il file si trova nella stessa directory di *python_tutorial_task.py* nel nodo.
2. **numwords**: le prime *N* parole devono essere scritte nel file di output.
3. **storageaccount**: nome dell'account di archiviazione proprietario del contenitore in cui dovrà essere caricato l'output dell'attività.
4. **storagecontainer**: nome del contenitore di archiviazione in cui dovranno essere caricati i file di output.
5. **sastoken**: firma di accesso condiviso che consente l'accesso in scrittura al contenitore **output** in Archiviazione di Azure. Lo script *python_tutorial_task.py* usa questa firma di accesso condiviso quando crea il riferimento BlockBlobService. È così possibile accedere al contenitore in scrittura senza una chiave di accesso dell'account di archiviazione.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>Passaggio 6: Monitorare le attività
![Monitorare le attività][6]<br/>
*(1) Lo script monitora le attività per verificare lo stato di completamento e (2) le attività caricano i dati dei risultati in Archiviazione di Azure*

Quando le attività vengono aggiunte a un processo, vengono accodate automaticamente e ne viene pianificata l'esecuzione nei nodi di calcolo entro il pool associato al progetto. In base alle impostazioni specificate, Batch gestisce tutte le operazioni di accodamento, pianificazione, ripetizione di tentativi dell'attività e tutte le altre operazioni amministrative relative all'attività.

Sono disponibili molti approcci al monitoraggio dell'esecuzione delle attività. La funzione `wait_for_tasks_to_complete` in *python_tutorial_client.py* offre un esempio semplice di monitoraggio delle attività per un determinato stato, in questo caso lo stato [Completed][py_taskstate].

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>Passaggio 7: Scaricare l'output dell'attività
![Scaricare l'output delle attività dal servizio di archiviazione][7]<br/>

Dopo il completamento del processo, l'output delle attività può essere scaricato da Archiviazione di Azure con una chiamata a `download_blobs_from_container` in *python_tutorial_client.py*:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> La chiamata a `download_blobs_from_container` in *python_tutorial_client.py* specifica che i file devono essere scaricati nella home directory dell'utente. È possibile modificare questo percorso di output.
>
>

## <a name="step-8-delete-containers"></a>Passaggio 8: Eliminare i contenitori
Poiché vengono effettuati addebiti per i dati che risiedono in Archiviazione di Azure, è consigliabile rimuovere eventuali BLOB non più necessari per i processi di Batch. In *python_tutorial_client.py*, questa operazione viene eseguita con tre chiamate a [BlockBlobService.delete_container][py_delete_container]:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Passaggio 9: Eliminare il processo e il pool
Nel passaggio finale viene richiesto all'utente di eliminare il processo e il pool creati dallo script *python_tutorial_client.py*. Anche se non vengono addebitati costi per i processi e le attività in sé, *vengono* addebiti costi per i nodi di calcolo. È quindi consigliabile allocare i nodi solo in base alla necessità. L'eliminazione dei pool inutilizzati può fare parte del processo di manutenzione.

Gli elementi [JobOperations][py_job] e [PoolOperations][py_pool] di BatchServiceClient includono metodi di eliminazione corrispondenti, che vengono chiamati se l'utente conferma l'eliminazione:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Occorre ricordare che vengono effettuati addebiti per le risorse di calcolo e che l'eliminazione di pool inutilizzati consente di ridurre al minimo i costi. Si noti anche che l'eliminazione di un pool comporta l'eliminazione di tutti i nodi di calcolo in quel pool e che eventuali dati disponibili nei nodi non potranno essere più recuperati dopo l'eliminazione del pool.
>
>

## <a name="run-the-sample-script"></a>Eseguire lo script di esempio
Quando si esegue lo script *python_tutorial_client.py* dal [codice di esempio][github_article_samples] dell'esercitazione, l'output della console sarà simile al seguente. Si riscontra una pausa in corrispondenza di `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` mentre vengono creati e avviati i nodi di calcolo del pool e vengono eseguiti i comandi nell'attività iniziale del pool. Usare il [portale di Azure][azure_portal] per monitorare il pool, i nodi di calcolo, il processo e le attività durante e dopo l'esecuzione. Usare il [portale di Azure][azure_portal] o [Microsoft Azure Storage Explorer][storage_explorer] per visualizzare le risorse di archiviazione (contenitori e BLOB) create dall'applicazione.

> [!TIP]
> Eseguire lo script *python_tutorial_client.py* dalla directory `azure-batch-samples/Python/Batch/article_samples`. che usa un percorso relativo per l'importazione del modulo `common.helpers`. Perciò se non si esegue lo script da questa directory, è possibile che venga visualizzato il messaggio `ImportError: No module named 'common'`.
>
>

Se si esegue l'esempio con la configurazione predefinita, il tempo di esecuzione tipico è di **circa 5-7 minuti** .

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Passaggi successivi
È possibile modificare *python_tutorial_client.py* e *python_tutorial_task.py* per sperimentare scenari di calcolo diversi. Si può ad esempio provare ad aggiungere un ritardo di esecuzione in *python_tutorial_task.py* per simulare attività con esecuzione prolungata e monitorarle nel portale. Provare ad aggiungere altre attività o a modificare il numero di nodi di calcolo. Aggiungere la logica per la ricerca e l'uso di un pool esistente per ridurre il tempo di esecuzione.

Dopo avere acquisito familiarità con il flusso di lavoro di base di una soluzione Batch, è possibile esaminare in dettaglio le funzionalità aggiuntive del servizio Batch.

* Se non si ha familiarità con il servizio, è consigliabile vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-api-basics.md) .
* Per altri articoli sullo sviluppo in Batch, vedere **Approfondimenti sullo sviluppo** nel [percorso di apprendimento per Batch][batch_learning_path].
* Una diversa implementazione dell'elaborazione del carico di lavoro di tipo "prime N parole" con Batch è disponibile nell'esempio [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Creare contenitori in Archiviazione di Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Caricare l'applicazione dell'attività e i file di input (dati) nei contenitori"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Creare un pool di Batch"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Creare un processo di Batch"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Aggiungere attività a un processo"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Monitorare le attività"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Scaricare l'output delle attività dal servizio di archiviazione"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Flusso di lavoro della soluzione Batch (diagramma completo)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Credenziali di Batch nel portale"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Credenziali del servizio di archiviazione nel portale"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Flusso di lavoro della soluzione Batch (diagramma minimo)"

