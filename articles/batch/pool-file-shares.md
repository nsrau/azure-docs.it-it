---
title: Condivisione file di Azure per i pool di Azure Batch | Microsoft Docs
description: Come montare una condivisione di File di Azure dai nodi di calcolo in un pool Linux o Windows in Azure Batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: cd185035640bf0beaa54fa6a0f4d92a33837442b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093976"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Usare una condivisione file di Azure con un pool di Batch

[File di Azure](../storage/files/storage-files-introduction.md) offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB (Server Message Block). Questo articolo fornisce informazioni ed esempi di codice per il montaggio e l'uso di una condivisione file di Azure nei nodi di calcolo del pool. Gli esempi di codice usano gli SDK di .NET e Python per Batch, ma è possibile eseguire operazioni simili con altri SDK per Batch e strumenti.

Batch fornisce il supporto delle API native per l'uso dei BLOB di archiviazione di Azure per leggere e scrivere dati. In alcuni casi potrebbe tuttavia essere necessario accedere a una condivisione file di Azure dai nodi di calcolo del pool, ad esempio quando è presente un carico di lavoro legacy che dipende da una condivisione file SMB o è necessario che le attività accedano ai dati condivisi o generino un output condiviso. 

## <a name="considerations-for-use-with-batch"></a>Considerazioni per l'uso con Batch

* Considerare la possibilità di usare una condivisione file di Azure quando si hanno pool che eseguono un numero relativamente basso di attività parallele. Esaminare gli [obiettivi di scalabilità e prestazioni](../storage/files/storage-files-scale-targets.md) per determinare se usare File di Azure (che usa un account di archiviazione di Azure), considerate le dimensioni del pool previste e il numero di file di asset. 

* Le condivisioni file di Azure sono [convenienti](https://azure.microsoft.com/pricing/details/storage/files/) e possono essere configurate con la replica dei dati in un'altra area e sono quindi ridondanti a livello globale. 

* È possibile montare simultaneamente una condivisione file di Azure da un computer locale.

* Vedere anche le [considerazioni sulla pianificazione](../storage/files/storage-files-planning.md) generali per le condivisioni file di Azure.


## <a name="create-a-file-share"></a>Creare una condivisione file

[Creare una condivisione file](../storage/files/storage-how-to-create-file-share.md) in un account di archiviazione collegato all'account Batch o in un account di archiviazione separato.

## <a name="mount-a-share-on-a-windows-pool"></a>Montare una condivisione in un pool Windows

Questa sezione fornisce i passaggi e gli esempi di codice per montare e usare una condivisione file di Azure in un pool di nodi Windows. Per altre informazioni, vedere la [documentazione](../storage/files/storage-how-to-use-files-windows.md) sul montaggio di una condivisione file di Azure in Windows. 

In Batch è necessario montare la condivisione a ogni esecuzione di un'attività in un nodo Windows. Non è attualmente possibile rendere persistente la connessione di rete tra le attività nei nodi Windows.

Ad esempio, includere un comando `net use` per montare la condivisione file come parte della riga di comando di ogni attività. Per montare la condivisione file, sono necessarie le credenziali seguenti:

* **User name** (Nome utente): AZURE\\\<storageaccountname\>, ad esempio AZURE\\*mystorageaccountname*
* **Password**: \<StorageAccountKeyWhichEnds in = = >, ad esempio *XXXXXXXXXXXXXXXXXXXXX = =*

Il comando seguente monta una condivisione file *myfileshare* nell'account di archiviazione *mystorageaccountname* come unità *S:* :

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Per semplicità, gli questi esempi passano le credenziali direttamente nel testo. In pratica, è consigliabile gestire le credenziali usando variabili di ambiente, certificati o una soluzione, ad esempio Azure Key Vault.

Per semplificare l'operazione di montaggio, rendere facoltativamente persistenti le credenziali nei nodi. È quindi possibile montare la condivisione senza credenziali. Eseguire i due passaggi seguenti:

1. Eseguire l'utilità della riga di comando `cmdkey` con un'attività di avvio nella configurazione del pool. In questo modo le credenziali vengono rese persistenti in ogni nodo di Windows. La riga di comando dell'attività di avvio è simile a:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Montare la condivisione in ogni nodo come parte di ogni attività con `net use`. La riga di comando dell'attività seguente, ad esempio, monta la condivisione file come unità *S:* e sarà seguita da un comando o uno script che fa riferimento alla condivisione. Le credenziali memorizzate nella cache vengono usate nella chiamata a `net use`. Questo passaggio presuppone per le attività l'uso della stessa identità utente usata per l'attività di avvio nel pool, il che non è valido per tutti gli scenari.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>Esempio in C#
L'esempio in C# seguente illustra come rendere persistenti le credenziali in un pool di Windows usando un'attività di avvio. Il nome del servizio file di archiviazione e le credenziali di archiviazione vengono passati come costanti definite. In questo caso, l'attività di avvio viene eseguita in un account utente automatico standard (non amministratore) con l'ambito del pool.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Dopo aver archiviato le credenziali, usare le righe di comando dell'attività per montare la condivisione e fare riferimento alla condivisione nelle operazioni di lettura o scrittura. Come esempio di base, la riga di comando dell'attività nel frammento seguente usa il comando `dir` per elencare i file nella condivisione file. Assicurarsi di eseguire ogni attività del processo con la stessa [identità utente](batch-user-accounts.md) usata per eseguire l'attività di avvio nel pool. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Montare una condivisione in un pool di Linux

Le condivisioni di File di Azure possono essere montate nelle distribuzioni di Linux tramite il [client kernel CIFS](https://wiki.samba.org/index.php/LinuxCIFS). L'esempio seguente illustra come montare una condivisione file in un pool di nodi di calcolo Ubuntu 16.04 LTS. Se si usa una distribuzione di Linux diversa, i passaggi generali sono simili, ma è necessario usare la gestione pacchetti appropriata per la distribuzione. Per informazioni dettagliate ed esempi aggiuntivi, vedere [Usare File di Azure con Linux](../storage/files/storage-how-to-use-files-linux.md).

Prima di tutto, con un'identità utente amministratore, installare il pacchetto `cifs-utils` e creare il punto di montaggio (ad esempio, */mnt/MyAzureFileShare*) nel file system locale. Una cartella per un punto di montaggio può essere creata in qualsiasi posizione nel file system, ma normalmente viene creata sotto la cartella `/mnt`. Assicurarsi di non creare un punto di montaggio direttamente in `/mnt` (in Ubuntu) o `/mnt/resource` (in altre distribuzioni).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Eseguire quindi il comando `mount` per montare la condivisione file, specificando queste credenziali:

* **Nome utente**: \<storageaccountname\>, ad esempio *mystorageaccountname*
* **Password**: \<StorageAccountKeyWhichEnds in = = >, ad esempio *XXXXXXXXXXXXXXXXXXXXX = =*

Il comando seguente monta una condivisione file *myfileshare* nell'account di archiviazione *mystorageaccountname* in */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Per semplicità, gli questi esempi passano le credenziali direttamente nel testo. In pratica, è consigliabile gestire le credenziali usando variabili di ambiente, certificati o una soluzione, ad esempio Azure Key Vault.

In un pool di Linux è possibile combinare tutti questi passaggi in una singola attività di avvio o eseguirli in uno script. Eseguire l'attività di avvio come utente amministratore nel pool. Impostare l'attività di avvio in modo da attendere il completamento prima di eseguire altre attività nel pool che fanno riferimento alla condivisione.

### <a name="python-example"></a>Esempio in Python

L'esempio in Python seguente illustra come configurare un pool di Ubuntu per montare la condivisione in un'attività di avvio. Il punto di montaggio, l'endpoint della condivisione file e le credenziali di archiviazione vengono passati come costanti definite. L'attività di avvio viene eseguita in un account utente automatico amministratore con l'ambito del pool.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Dopo il montaggio della condivisione e la definizione di un processo, usare la condivisione nelle righe di comando delle attività. Il comando di base seguente, ad esempio, usa `ls` per elencare i file nella condivisione file.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Passaggi successivi

* Per altre opzioni per leggere e scrivere i dati in Batch, vedere la [panoramica delle funzionalità di Batch](batch-api-basics.md) e [Rendere persistente l'output di processi e attività](batch-task-output.md).

* Per distribuire file system per carichi di lavoro di Batch vedere anche il toolkit [Batch Shipyard](https://github.com/Azure/batch-shipyard), che include [Shipyard Recipes](https://github.com/Azure/batch-shipyard/tree/master/recipes).
