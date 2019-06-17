---
title: Archiviazione e spostamento dati per il rendering - Azure Batch
description: Opzioni di archiviazione e spostamento dati per i carichi di lavoro del rendering
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 5a0d4dc82995e63697cc673bc54695c9c6d586df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773996"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opzioni di archiviazione e spostamento dati per i file di asset e output del rendering

È possibile sfruttare numerose opzioni per rendere i file di scena e di asset disponibili alle applicazioni per il rendering nelle macchine virtuali (VM) del pool:

* [Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * I file di scena e di asset vengono caricati nell'archiviazione BLOB da un file system locale. Quando l'applicazione viene eseguita da un'attività, i file richiesti vengono copiati dall'archiviazione BLOB alla VM in modo da essere accessibili dall'applicazione per il rendering. I file di output vengono scritti dall'applicazione per il rendering nel disco della VM e quindi copiati nell'archiviazione BLOB.  Se necessario, i file di output possono essere scaricati dall'archiviazione BLOB a un file system locale.
  * Archiviazione BLOB di Azure è un'opzione semplice ed economicamente conveniente per i progetti di dimensioni più piccole.  Poiché tutti i file di asset sono necessari in ogni VM del pool, in caso di aumento del numero e delle dimensioni dei file di asset è necessario assicurarsi che i trasferimenti di file siano il più possibile efficienti.  
* Archiviazione di Azure come file system con [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Per le VM Linux, è possibile esporre un account di archiviazione e usarlo come file system quando si usa il driver di file system virtuale Blobfuse.
  * Questa opzione ha il vantaggio di essere molto conveniente, in quanto non sono necessarie VM per il file system, inoltre la memorizzazione nella cache di Blobfuse nelle VM consente di evitare download ripetuti degli stessi file per più processi e attività.  Anche lo spostamento dei dati è semplice, poiché i file sono semplicemente BLOB ed è possibile usare API e strumenti standard, come azcopy, per copiare i file tra un file system locale e Archiviazione di Azure.
* File system o condivisione file:
  * A seconda del sistema operativo della macchina virtuale e dei requisiti di prestazioni e scalabilità, le opzioni includono [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), l'uso di una VM con dischi collegati per NFS, l'uso di più VM con dischi collegati per un file system distribuito, ad esempio GlusterFS, o l'uso di un'offerta di terze parti.
  * [Avere Systems](https://www.averesystems.com/) fa ora parte di Microsoft e saranno presto disponibili soluzioni ideali per il rendering su larga scala e a elevate prestazioni.  La soluzione Avere consentirà la creazione di una cache NFS o SMB basata su Azure che funziona in combinazione con l'archiviazione BLOB o con i dispositivi NAS locali.
  * Con un file system, i file possono essere letti o scritti direttamente nel file system o possono essere copiati tra il file system e le VM del pool.
  * Un file system condiviso consente la condivisione di un numero elevato di asset tra progetti e processi destinato all'utilizzo, con l'accesso delle attività di rendering solo agli asset necessari.

## <a name="using-azure-blob-storage"></a>Uso di Archiviazione BLOB di Azure

È necessario usare un account di archiviazione BLOB o un account di archiviazione v2 per utilizzo generico.  Questi due tipi di account di archiviazione possono essere configurati con limiti significativamente più elevati rispetto a un account di archiviazione v1 per utilizzo generico, come descritto in [questo post di blog](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Dopo la configurazione, i limiti più elevati consentiranno prestazioni e scalabilità migliori, soprattutto quando molte VM del pool accedono all'account di archiviazione.

### <a name="copying-files-between-client-and-blob-storage"></a>Copia di file tra client e archiviazione BLOB

Per copiare i file da e verso Archiviazione di Azure, è possibile usare vari meccanismi tra cui l'API del BLOB di archiviazione, la [libreria di Azure Storage Data Movement](https://github.com/Azure/azure-storage-net-data-movement), lo strumento da riga di comando azcopy per [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) o [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Ad esempio, se si usa azcopy, tutti gli asset in una cartella possono essere trasferiti nel modo seguente:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Per copiare solo i file modificati, è possibile usare il parametro /XO:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copia dei file di asset di input da Archiviazione BLOB alle VM del pool di Batch

Sono disponibili due diversi approcci per copiare i file. L'opzione migliore è determinata dalla dimensione degli asset di processo.
L'approccio più semplice consiste nel copiare tutti i file di asset nelle VM del pool per ogni processo:

* Quando sono presenti file univoci per un processo, ma questi sono necessari per tutte le attività di un processo, è possibile specificare un'[attività di preparazione del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) per copiare tutti i file.  L'attività di preparazione del processo viene eseguita una volta alla prima esecuzione dell'attività di processo su una VM, ma non viene ripetuta per le attività di processo successive.
* È consigliabile specificare anche un'[attività di rilascio del processo](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) per rimuovere i file al termine di ogni processo. In questo modo si evita che il disco della VM venga riempito con tutti i file di asset di processo.
* Quando sono presenti più processi che usano gli stessi asset, con solo modifiche incrementali agli asset per ogni processo, tutti i file di asset vengono comunque copiati, anche se è stato aggiornato solo un subset.  Ciò può risultare poco efficiente se sono presenti numerosi file di asset di grandi dimensioni.

Quando nei processi vengono riutilizzati i file di asset, solo con modifiche incrementali da un processo all'altro, un approccio più efficiente ma leggermente più complesso consiste nell'archiviare gli asset nella cartella condivisa sulla VM e nel sincronizzare i file modificati.

* L'attività di preparazione del processo esegue la copia usando azcopy con il parametro /XO nella cartella condivisa della VM specificata dalla variabile di ambiente AZ_BATCH_NODE_SHARED_DIR.  In questo modo, in ogni VM vengono copiati solo i file modificati.
* Tenere presente la dimensione di tutti gli asset per assicurarsi di poterli inserire nell'unità temporanea delle VM del pool.

In Azure Batch è integrato il supporto per la copia dei file tra un account di archiviazione e le VM del pool di Batch.  L'attività [resourcefile](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) copia i file dall'archiviazione alle VM del pool e può essere specificata per l'attività di preparazione del processo.  Tuttavia, quando sono presenti centinaia di file, è possibile che venga raggiunto un limite e che le attività abbiano esito negativo.  Quando è presente un numero elevato di asset, è consigliabile usare la riga di comando azcopy nell'attività di preparazione del processo. In questo modo, è possibile usare caratteri jolly e non sono previsti limiti.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Copia dei file di output nell'archiviazione BLOB dalle VM del pool di Batch

Per copiare i file da una VM del pool alla risorsa di archiviazione, è possibile usare i [file di output](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile).  Al termine dell'attività, uno o più file possono essere copiati dalla VM a un account di archiviazione specificato.  È consigliabile copiare l'output del rendering, ma può essere utile anche archiviare i file di log.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Uso di un file system virtuale Blobfuse per i pool di VM Linux

Blobfuse è un driver di file system virtuale per Archiviazione BLOB di Azure che consente di accedere ai file archiviati come BLOB in un account di archiviazione tramite il file system Linux.

I nodi del pool possono montare il file system all'avvio oppure il montaggio può essere eseguito nel corso dell'attività di preparazione di un processo, che si svolge solo quando viene eseguita la prima attività di un processo su un nodo.  Blobfuse può essere configurato per l'uso sia di un disco RAM sia dell'unità SSD locale delle VM per la memorizzazione nella cache dei file. In questo modo, le prestazioni migliorano in modo significativo se più attività in un nodo accedono ad alcuni degli stessi file.

[Sono disponibili modelli di esempio](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) per eseguire il rendering autonomo di V-Ray usando un file system Blobfuse. Tali esempi possono essere usati come base per i modelli di altre applicazioni.

### <a name="accessing-files"></a>Accesso ai file

Le attività di processo specificano i percorsi dei file di input e di output tramite il file system montato.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copia dei file di asset di input da Archiviazione BLOB alle VM del pool di Batch

Poiché i file sono semplicemente BLOB in Archiviazione di Azure, per copiare i file tra un file system locale e l'archiviazione BLOB è possibile usare API BLOB standard, strumenti e interfacce utente. Ad esempio, azcopy, Storage Explorer, Batch Explorer e così via.

## <a name="using-azure-files-with-windows-vms"></a>Uso di archiviazione file con VM Windows

[File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) offre condivisioni file completamente gestite nel cloud, accessibili tramite il protocollo SMB.  File di Azure si basa su Archiviazione BLOB di Azure, è [conveniente](https://azure.microsoft.com/pricing/details/storage/files/), può essere configurato con la replica dei dati in un'altra area ed è quindi ridondante a livello globale.  È necessario consultare gli [obiettivi di scalabilità](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) per determinare se è opportuno usare File di Azure in base alla dimensione del pool di previsione e al numero di file di asset.

Sono disponibili un [post di blog](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) e articoli della [documentazione](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) relativi al montaggio di una condivisione di File di Azure.

### <a name="mounting-an-azure-files-share"></a>Montaggio di una condivisione di File di Azure

Per l'uso in Batch, è richiesta un'operazione di montaggio ogni volta che viene eseguita un'attività poiché non è possibile rendere persistente la connessione tra le attività.  Il modo più semplice per eseguire questa operazione è quello di usare cmdkey per rendere persistenti le credenziali usando l'attività di avvio nella configurazione del pool e quindi montare la condivisione prima di ogni attività.

Esempio d'uso di cmdkey in un modello di pool (caratteri di escape per l'uso nel file JSON). Tenere presente che, quando si separa la chiamata a cmdkey dalla chiamata a net use, il contesto utente per l'attività di avvio deve essere uguale a quello usato per l'esecuzione delle attività:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Esempio d'uso della riga di comando per le attività di processo:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Accesso ai file

Le attività di processo specificano i percorsi dei file di input e di output tramite il file system montato, usando un'unità mappata o un percorso UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copia dei file di asset di input da Archiviazione BLOB alle VM del pool di Batch

File di Azure è supportato da tutte le API e gli strumenti principali che dispongono del supporto per Archiviazione di Azure, ad esempio azcopy, l'interfaccia della riga di comando di Azure, Storage Explorer, Azure PowerShell, Batch Explorer e così via.

[Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) è disponibile per sincronizzare automaticamente i file tra un file system locale e una condivisione di File di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di archiviazione, vedere la documentazione di approfondimento:

* [Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
