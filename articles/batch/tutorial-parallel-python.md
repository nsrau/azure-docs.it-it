---
title: Eseguire un carico di lavoro parallelo - Python in Azure Batch
description: Esercitazione - Elaborare file multimediali in parallelo con ffmpeg in Azure Batch usando la libreria client Python di Batch
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: dlepow
ms.custom: mvc
ms.openlocfilehash: f9853578962027d6308581a76e00d6619cbbf9ec
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Esercitazione: Eseguire un carico di lavoro parallelo con Azure Batch usando l'API Python

Usare Azure Batch per eseguire in modo efficiente processi batch paralleli e HPC (High Performance Computing) su larga scala in Azure. Questa esercitazione illustra un esempio Python di esecuzione di un carico di lavoro parallelo usando Batch. Vengono fornite informazioni su un flusso di lavoro dell'applicazione Batch comune e su come interagire a livello di codice con le risorse di Archiviazione e Batch. Si apprenderà come:

> [!div class="checklist"]
> * Eseguire l'autenticazione con gli account di archiviazione e Batch
> * Caricare i file di input nella risorsa di archiviazione
> * Creare un pool di nodi di calcolo per eseguire un'applicazione
> * Creare un processo e le attività per elaborare i file di input
> * Monitorare l'esecuzione delle attività
> * Recuperare i file di output

In questa esercitazione file multimediali MP4 vengono convertiti in parallelo in formato MP3 usando lo strumento open source [ffmpeg](http://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

* [Python versione 2.7, 3.3 o successiva](https://www.python.org/downloads/)

* Gestione pacchetti [pip](https://pip.pypa.io/en/stable/installing/)

* Un account Azure Batch e un account di archiviazione di Azure per utilizzo generico collegato. Per creare questi account, vedere le guide introduttive di Batch usando il [portale di Azure](quick-create-portal.md) o l'[interfaccia della riga di comando di Azure](quick-create-cli.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Scaricare ed eseguire l'esempio

### <a name="download-the-sample"></a>Scaricare l'esempio

[Scaricare o clonare l'app di esempio](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) da GitHub. Per clonare il repository dell'app di esempio con un client Git, usare il comando seguente:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Passare alla directory contenente il file `batch_python_tutorial_ffmpeg.py`.

Nell'ambiente Python installare i pacchetti necessari usando `pip`.

```bash
pip install -r requirements.txt
```

Aprire il file `batch_python_tutorial_ffmpeg.py`. Aggiornare le stringhe delle credenziali degli account di archiviazione e Batch con valori univoci per gli account in uso. Ad esempio: 


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Esecuzione dell'app

Per eseguire lo script:

```
python batch_python_tutorial_ffmpeg.py
```

Quando si esegue l'applicazione di esempio, l'output della console è simile al seguente. Durante l'esecuzione si verifica una pausa in corrispondenza di `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` mentre vengono avviati i nodi di calcolo del pool. 
   
```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Passare all'account Batch nel portale di Azure per monitorare il pool, i nodi di calcolo, il processo e le attività. Per visualizzare, ad esempio, una mappa termica dei nodi di calcolo nel pool, fare clic su **Pool** > *LinuxFFmpegPool*.

Quando le attività sono in esecuzione, la mappa termica è simile all'esempio seguente:

![Mappa termica del pool](./media/tutorial-parallel-python/pool.png)

Quando si esegue l'applicazione con la configurazione predefinita, il tempo di esecuzione tipico è di circa **5 minuti**. La maggior parte del tempo è necessaria per la creazione del pool. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Esaminare il codice

Nelle sezioni seguenti si esamineranno in dettaglio i singoli passaggi eseguiti dall'applicazione di esempio per l'elaborazione di un carico di lavoro nel servizio Batch. Fare riferimento al codice Python mentre si legge la parte restante di questo articolo, perché non vengono illustrate tutte le righe di codice dell'esempio.

### <a name="authenticate-blob-and-batch-clients"></a>Autenticare i client BLOB e Batch

Per interagire con un account di archiviazione, l'app usa il pacchetto [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) per creare un oggetto [BlockBlobService](/python/api/azure.storage.blob.blockblobservice.blockblobservice).

```python
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)
```

L'app crea un oggetto [BatchServiceClient](/python/api/azure.batch.batchserviceclient) per creare e gestire pool, processi e attività nel servizio Batch. Il client Batch nell'esempio usa l'autenticazione con chiave condivisa. Batch supporta anche l'autenticazione tramite [Azure Active Directory](batch-aad-auth.md), per autenticare singoli utenti o un'applicazione eseguita automaticamente.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
    _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Caricare i file di input

L'applicazione usa il riferimento `blob_client` per creare un contenitore di archiviazione per i file MP4 di input e un contenitore per l'output dell'attività. Chiama quindi la funzione `upload_file_to_container` per caricare i file MP4 della directory `InputFiles` locale nel contenitore. I file nel contenitore di archiviazione sono definiti come oggetti [ResourceFile](/python/api/azure.batch.models.resourcefile) di Batch che successivamente Batch può scaricare nei nodi di calcolo.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []
    
for folder, subs, files in os.walk('./InputFiles/'):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks. 
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Creare un pool di nodi di calcolo

L'esempio crea quindi un pool di nodi di calcolo nell'account Batch con una chiamata a `create_pool`. Questa funzione definita usa la classe [PoolAddParameter](/python/api/azure.batch.models.pooladdparameter) di Batch per impostare il numero di nodi, le dimensioni delle VM e una configurazione del pool. In questo caso, un oggetto [VirtualMachineConfiguration](/python/api/azure.batch.models.virtualmachineconfiguration) specifica un oggetto [ImageReference](/python/api/azure.batch.models.imagereference) che fa riferimento a un'immagine di Ubuntu Server 16.04 LTS pubblicata in Azure Marketplace. Batch supporta una vasta gamma di immagini di VM in Azure Marketplace, oltre che immagini di VM personalizzate.

Il numero di nodi e le dimensioni delle VM vengono impostati usando costanti definite. Batch supporta nodi dedicati e [nodi per priorità bassa](batch-low-pri-vms.md) e nei pool è possibile usare uno di questi tipi o entrambi. I nodi dedicati sono riservati per il pool. I nodi per priorità bassa vengono offerti a un prezzo ridotto usando la capacità in eccesso delle VM in Azure. I nodi per priorità bassa non sono disponibili se Azure non ha capacità sufficiente. L'esempio crea per impostazione predefinita un pool che contiene solo 5 nodi per priorità bassa con dimensioni *Standard_A1_v2*. 

Oltre alle proprietà dei nodi fisici, la configurazione del pool include un oggetto [StartTask](/python/api/azure.batch.models.starttask). L'attività StartTask viene eseguita in ogni nodo quando questo viene aggiunto al pool e ogni volta che viene riavviato. In questo esempio l'oggetto StartTask esegue i comandi della shell Bash per installare il pacchetto ffmpeg e le dipendenze nei nodi.

Il metodo [pool.add](/python/api/azure.batch.operations.pooloperations#azure_batch_operations_PoolOperations_add) invia il pool al servizio Batch.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Creare un processo

Un processo Batch specifica un pool in cui eseguire le attività e impostazioni facoltative, ad esempio una priorità e una pianificazione per il lavoro. L'esempio crea un processo con una chiamata a `create_job`. La funzione definita usa la classe [JobAddParameter](/python/api/azure.batch.models.jobaddparameter) per creare un processo nel pool. Il metodo [job.add](/python/api/azure.batch.operations.joboperations#azure_batch_operations_JobOperations_add) invia il pool al servizio Batch. Inizialmente il processo è privo di attività.

```python
job = batch.models.JobAddParameter(
    job_id,
    batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Creare le attività

L'app crea le attività nel processo con una chiamata a `add_tasks`. Questa funzione definita crea un elenco di oggetti attività usando la classe [TaskAddParameter](/python/api/azure.batch.models.taskaddparameter). Ogni attività esegue ffmpeg per elaborare un oggetto `resource_files` di input usando un parametro `command_line`. Lo strumento ffmpeg è stato installato in precedenza in ogni nodo al momento della creazione del pool. In questo caso, la riga di comando esegue ffmpeg per convertire ogni file (video) MP4 di input in un file (audio) MP3.

L'esempio crea un oggetto [OutputFile](/python/api/azure.batch.models.outputfile) per il file MP3 dopo l'esecuzione della riga di comando. I file di output di ogni attività, in questo caso uno, vengono caricati in un contenitore nell'account di archiviazione collegato, usando la proprietà `output_files` dell'attività.

L'app aggiunge quindi le attività al processo con il metodo [task.add_collection](/python/api/azure.batch.operations.taskoperations#azure_batch_operations_TaskOperations_add_collection), che le accoda per l'esecuzione nei nodi di calcolo. 

```python
tasks = list()

for idx, input_file in enumerate(input_files): 
    input_file_path=input_file.file_path
    output_file_path="".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(output_file_path,
              destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(output_container_sas_url)),
              upload_options=batchmodels.OutputFileUploadOptions(
                batchmodels.OutputFileUploadCondition.task_success))]
            )
     )
batch_service_client.task.add_collection(job_id, tasks)
```    

### <a name="monitor-tasks"></a>Monitorare le attività

Quando le attività vengono aggiunte a un processo, Batch le accoda automaticamente e ne pianifica l'esecuzione nei nodi di calcolo nel pool associato. In base alle impostazioni specificate, Batch gestisce tutte le operazioni di accodamento, pianificazione, ripetizione di tentativi e tutte le altre operazioni di amministrazione relative alle attività. 

Sono disponibili molti approcci al monitoraggio dell'esecuzione delle attività. La funzione `wait_for_tasks_to_complete` in questo esempio usa l'oggetto [TaskState](/python/api/azure.batch.models.taskstate) per monitorare le attività al fine di individuare un determinato stato, in questo caso lo stato completato, entro un limite di tempo.

```python
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
...
```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esecuzione delle attività, l'app elimina automaticamente il contenitore di archiviazione di input creato e consente di scegliere se eliminare il processo e il pool di Batch. Le classi [JobOperations](/python/api/azure.batch.operations.joboperations) e [PoolOperations](/python/api/azure.batch.operations.pooloperations) di BatchClient hanno entrambe metodi di eliminazione, che vengono chiamati se l'utente conferma l'eliminazione. Anche se non vengono addebitati costi per i processi e per le attività, vengono invece addebiti costi per i nodi di calcolo. È quindi consigliabile allocare solo i pool necessari. Quando si elimina il pool, tutto l'output delle attività nei nodi viene eliminato. I file di input e output rimangono tuttavia nell'account di archiviazione.

Quando non sono più necessari, eliminare il gruppo di risorse, l'account Batch e l'account di archiviazione. A tale scopo, nel portale di Azure selezionare il gruppo di risorse per l'account Batch e fare clic su **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso a:

> [!div class="checklist"]
> * Eseguire l'autenticazione con gli account di archiviazione e Batch
> * Caricare i file di input nella risorsa di archiviazione
> * Creare un pool di nodi di calcolo per eseguire un'applicazione
> * Creare un processo e le attività per elaborare i file di input
> * Monitorare l'esecuzione delle attività
> * Recuperare i file di output

Per altri esempi di uso dell'API Python per pianificare ed elaborare i carichi di lavoro di Batch, vedere gli esempi su GitHub.

> [!div class="nextstepaction"]
> [Esempi Python per Batch](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

