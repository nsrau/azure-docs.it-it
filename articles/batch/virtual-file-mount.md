---
title: Montare un file system virtuale in un pool
description: Informazioni su come montare un file system virtuale in un pool Batch.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145485"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montare un file system virtuale in un pool Batch

Azure Batch supporta ora il montaggio dell'archiviazione cloud o di un file system esterno nei nodi di calcolo Windows o Linux nei pool di Batch. Quando un nodo di calcolo viene aggiunto a un pool, il file system virtuale viene montato e considerato come un'unità locale in tale nodo. È possibile montare diversi tipi di file system, ad esempio File di Azure, archiviazione BLOB di Azure, NFS (Network File System) o anche una [cache Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) o il servizio CIFS (Common Internet file System).

In questo articolo si apprenderà come montare un file system virtuale in un pool di nodi di calcolo usando la [libreria di gestione Batch per .NET](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Il montaggio di un file system virtuale è supportato nei pool Batch creati il 19 agosto 2019 o successivamente. I pool Batch creati prima del 19 agosto 2019 non supportano questa funzionalità.
> 
> Le API per il montaggio dei file system in un nodo di calcolo sono parte della raccolta [Batch .NET](/dotnet/api/microsoft.azure.batch).

## <a name="benefits-of-mounting-on-a-pool"></a>Vantaggi del montaggio in un pool

Il montaggio del file system nel pool, anziché consentire alle attività di recuperare i propri dati da un set di dati di grandi dimensioni, rende più semplice e più efficiente l'accesso ai dati necessari da parte delle attività.

Si consideri uno scenario con più attività che richiedono l'accesso a un set di dati comune, ad esempio il rendering di un film. Ogni attività esegue il rendering di uno o più fotogrammi alla volta dai file della scena. Montando un'unità che contiene i file della scena, è più facile per i nodi di calcolo accedere ai dati condivisi. Inoltre, il file system sottostante può essere scelto e ridimensionato in modo indipendente in base alle prestazioni e alla scalabilità (velocità effettiva e IOPS) richieste dal numero di nodi di calcolo che accedono contemporaneamente ai dati. Ad esempio, è possibile usare una cache [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) in memoria distribuita per supportare il rendering di immagini di movimento di grandi dimensioni con migliaia di nodi di rendering simultanei, accedendo ai dati di origine che risiedono in locale. In alternativa, per i dati che già risiedono nell'archivio BLOB basato sul cloud, è possibile usare [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) per montare questi dati come file system locale. Blobfuse è disponibile solo per i nodi Linux, tuttavia [File di Azure](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) offre un flusso di lavoro simile ed è disponibile sia in Windows che in Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montare un file system virtuale in un pool  

Il montaggio di un file system virtuale in un pool rende disponibile il file system per ogni nodo di calcolo del pool. Il file system viene configurato quando un nodo di calcolo viene aggiunto a un pool o quando il nodo viene riavviato o ne viene ricreata l'immagine.

Per montare un file system in un pool, creare un oggetto `MountConfiguration`. Scegliere l'oggetto più adatto al file system virtuale in uso: `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` o `CifsMountConfiguration`.

Per tutti gli oggetti di configurazione di montaggio sono necessari i parametri di base seguenti. Alcune configurazioni di montaggio hanno parametri specifici per il file system in uso, che verranno descritti in modo più dettagliato negli esempi di codice.

- **Nome account o origine**: per montare una condivisione file virtuale, è necessario indicare il nome dell'account di archiviazione o la relativa origine.
- **Percorso di montaggio relativo o origine**: posizione del file system montato nel nodo di calcolo, relativo alla directory `fsmounts` standard accessibile nel nodo via `AZ_BATCH_NODE_MOUNTS_DIR`. La posizione esatta varia a seconda del sistema operativo usato nel nodo. Ad esempio, la posizione fisica in un nodo Ubuntu viene mappata a `mnt\batch\tasks\fsmounts` e in un nodo CentOS viene mappata a `mnt\resources\batch\tasks\fsmounts`.
- **Opzioni di montaggio o opzioni blobfuse**: queste opzioni descrivono parametri specifici per il montaggio di un file system.

Dopo aver creato l'oggetto `MountConfiguration`, assegnare l'oggetto alla proprietà `MountConfigurationList` quando si crea il pool. Il file system viene montato quando si aggiunge un nodo a un pool o quando il nodo viene riavviato o ne viene ricreata l'immagine.

Quando viene montato il file system, viene creata una variabile di ambiente `AZ_BATCH_NODE_MOUNTS_DIR` che punta alla posizione dei file system montati, nonché ai file di log, utili per la risoluzione dei problemi e il debug. I file di log sono descritti più in dettaglio nella sezione [Diagnosticare gli errori di montaggio](#diagnose-mount-errors).  

> [!IMPORTANT]
> Il numero massimo di file system montati in un pool è 10. Per dettagli e altri limiti, vedere [Quote e limiti del servizio Batch](batch-quota-limit.md#other-limits).

## <a name="examples"></a>Esempi

Gli esempi di codice seguenti illustrano il montaggio di diverse condivisioni file in un pool di nodi di calcolo.

### <a name="azure-files-share"></a>Condivisione di File di Azure

File di Azure è l'offerta standard di file system cloud di Azure. Per altre informazioni su come ottenere uno dei parametri dell'esempio di codice per la configurazione di montaggio, vedere l'articolo sull'[uso di una condivisione di File di Azure](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>File system di BLOB di Azure

Un'altra opzione è usare l'archiviazione BLOB di Azure con [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Il montaggio di un file system BLOB richiede un `AccountKey` o `SasKey` per l'account di archiviazione. Per informazioni su come ottenere queste chiavi, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md) o [Uso delle firme di accesso condiviso](../storage/common/storage-sas-overview.md). Per altre informazioni sull'uso di blobfuse, vedere le [domande frequenti sulla risoluzione dei problemi](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) di blobfuse. Per ottenere l'accesso predefinito alla directory blobfuse montata, eseguire l'attività come **amministratore**. Blobfuse monta la directory nello spazio utente e al momento della creazione del pool viene montata come radice. In Linux tutte le attività di **amministratore** sono radice. Tutte le opzioni per il modulo FUSE sono descritte nella [pagina di riferimento di FUSE](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Oltre alla guida alla risoluzione dei problemi, i problemi GitHub nel repository blobfuse sono un valido aiuto per verificare gli attuali problemi di blobfuse e le relative risoluzioni. Per altre informazioni, vedere i [problemi di blobfuse](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>NFS (Network File System)

È anche possibile montare il servizio NFS nei nodi del pool, in modo da consentire l'accesso ai file system tradizionali da parte dei nodi di Azure Batch. Si può usare un singolo server NFS distribuito nel cloud o un server NFS locale a cui si accede attraverso una rete virtuale. In alternativa, è possibile usufruire dei vantaggi offerti dalla cache [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) in memoria distribuita, che garantisce una connettività senza problemi all'archiviazione locale, leggendo i dati su richiesta nella propria cache, e offre prestazioni elevate e scalabilità ai nodi di calcolo basati sul cloud.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>CIFS (Common Internet File System)

È anche possibile montare CIFS nei nodi del pool, in modo da consentire l'accesso ai file system tradizionali da parte dei nodi di Azure Batch. CIFS è un protocollo di condivisione file che offre un meccanismo aperto e multipiattaforma per la richiesta di file e servizi del server di rete. CIFS è basato sulla versione migliorata del protocollo SMB (Server Message Block) di Microsoft per la condivisione dei file su Internet e Intranet e viene usato per il montaggio dei file system esterni nei nodi Windows. Per altre informazioni su SMB, vedere l'articolo su [file server e SMB](/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnosticare gli errori di montaggio

Se una configurazione di montaggio non riesce, il nodo di calcolo nel pool avrà esito negativo e il nodo non potrà più essere usato. Per diagnosticare un errore di configurazione di montaggio, esaminare la proprietà [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) per informazioni dettagliate sull'errore.

Per recuperare i file di log per il debug, usare [OutputFiles](batch-task-output-files.md) per caricare i file di `*.log`. I file di `*.log` contengono informazioni sul montaggio del file system nella posizione `AZ_BATCH_NODE_MOUNTS_DIR`. Il formato dei file di log di montaggio è `<type>-<mountDirOrDrive>.log` per ogni montaggio. Ad esempio, un montaggio `cifs` in una directory di montaggio denominata `test` avrà un file di log di montaggio denominato `cifs-test.log`.

## <a name="supported-skus"></a>SKU supportati

| Editore | Offerta | SKU | Condivisione di File di Azure | Blobfuse | Montaggio NFS | Montaggio CIFS |
|---|---|---|---|---|---|---|
| o batch | rendering-centos73 | rendering | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Nota: Supporta l'archiviazione A_8 o 9</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul montaggio di una condivisione di File di Azure con [Windows](../storage/files/storage-how-to-use-files-windows.md) o [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Informazioni sull'uso e sul montaggio di file system virtuali [blobfuse](https://github.com/Azure/azure-storage-fuse).
- Per informazioni su NFS e sulle relative applicazioni, vedere [Panoramica di NFS (Network File System)](/windows-server/storage/nfs/nfs-overview).
- Per altre informazioni su CIFS, vedere [Protocollo SMB di Microsoft e panoramica del protocollo CIFS](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview).
