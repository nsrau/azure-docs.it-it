---
title: Montare un file system virtuale in un pool-Azure Batch | Microsoft Docs
description: Informazioni su come montare un file system virtuale in un pool di batch.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.openlocfilehash: 3bbf8cf126c80b9111f2bcbe24353c2731995bc1
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036891"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montare un file system virtuale in un pool di batch

Azure Batch supporta ora il montaggio dell'archiviazione cloud o di un file system esterno nei nodi di calcolo Windows o Linux nei pool di batch. Quando un nodo di calcolo viene aggiunto a un pool, il file system virtuale viene montato e considerato come un'unità locale in tale nodo. È possibile montare file System, ad esempio File di Azure, archiviazione BLOB di Azure, NFS (Network File System), tra cui una [cache vFXT](../avere-vfxt/avere-vfxt-overview.md)o un Common Internet file System (CIFS).

In questo articolo si apprenderà come montare un file system virtuale in un pool di nodi di calcolo usando la [libreria di gestione batch per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> Il montaggio di un file system virtuale è supportato nei pool di batch creati il o dopo il 2019-08-19. I pool di batch creati prima di 2019-08-19 non supportano questa funzionalità.
> 
> Le API per il montaggio di file System in un nodo di calcolo fanno parte della libreria [batch .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) .

## <a name="benefits-of-mounting-on-a-pool"></a>Vantaggi del montaggio in un pool

Il montaggio del file system al pool, anziché consentire alle attività di recuperare i propri dati da un set di dati di grandi dimensioni, rende più semplice e più efficiente l'accesso ai dati necessari da parte delle attività.

Si consideri uno scenario con più attività che richiedono l'accesso a un set di dati comune, ad esempio il rendering di un film. Ogni attività esegue il rendering di uno o più frame alla volta dai file della scena. Montando un'unità che contiene i file della scena, è più facile per i nodi di calcolo accedere ai dati condivisi. Inoltre, il file system sottostante può essere scelto e ridimensionato in modo indipendente in base alle prestazioni e alla scalabilità (velocità effettiva e IOPS) richieste dal numero di nodi di calcolo che accedono contemporaneamente ai dati. Ad esempio, è possibile usare una cache in memoria distribuita [vFXT](../avere-vfxt/avere-vfxt-overview.md) per supportare i rendering di grandi dimensioni delle immagini di movimento con migliaia di nodi di rendering simultanei, accedendo ai dati di origine che si trovano in locale. In alternativa, per i dati che si trovano già nell'archivio BLOB basato sul cloud, è possibile usare [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) per montare questi dati come file system locali. Blobfuse è disponibile solo nei nodi Linux, tuttavia [file di Azure](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) fornisce un flusso di lavoro simile ed è disponibile sia in Windows che in Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montare un file system virtuale in un pool  

Il montaggio di un file system virtuale in un pool rende disponibile il file system per ogni nodo di calcolo nel pool. Il file system viene configurato quando un nodo di calcolo viene aggiunto a un pool o quando il nodo viene riavviato o ne viene ricreata l'immagine.

Per montare un file System in un pool, creare un `MountConfiguration` oggetto. Scegliere l'oggetto che corrisponde al file system virtuale: `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration`o `CifsMountConfiguration`.

Per tutti gli oggetti di configurazione di montaggio sono necessari i parametri di base seguenti. Alcune configurazioni di montaggio presentano parametri specifici per la file system in uso, descritti in modo più dettagliato negli esempi di codice.

- **Nome account o origine**: Per montare una condivisione file virtuale, è necessario il nome dell'account di archiviazione o della relativa origine.
- **Origine o percorso di montaggio relativo**: Il percorso del file system montato sul nodo di calcolo, relativo alla directory standard `fsmounts` accessibile sul nodo tramite. `AZ_BATCH_NODE_MOUNTS_DIR` La posizione esatta varia a seconda del sistema operativo utilizzato nel nodo. Ad esempio, il percorso fisico in un nodo Ubuntu viene mappato `mnt\batch\tasks\fsmounts`a e su un nodo CentOS a `mnt\resources\batch\tasks\fsmounts`cui è stato eseguito il mapping.
- **Opzioni di montaggio o opzioni di blobfuse**: Queste opzioni descrivono parametri specifici per il montaggio di un file system.

Una volta `MountConfiguration` creato l'oggetto, assegnare l'oggetto `MountConfigurationList` alla proprietà quando si crea il pool. Il file system viene montato quando un nodo viene aggiunto a un pool o quando il nodo viene riavviato o ne viene ricreata l'immagine.

Quando viene montata la file System, viene creata `AZ_BATCH_NODE_MOUNTS_DIR` una variabile di ambiente che punta alla posizione dei file system montati, nonché ai file di log, utili per la risoluzione dei problemi e il debug. I file di log vengono illustrati più dettagliatamente nella sezione [diagnostica errori di montaggio](#diagnose-mount-errors) .  

> [!IMPORTANT]
> Il numero massimo di file system montati in un pool è 10. Per informazioni dettagliate e altri limiti, vedere [quote e limiti del servizio batch](batch-quota-limit.md#other-limits) .

## <a name="examples"></a>Esempi

Gli esempi di codice seguenti illustrano il montaggio di un'ampia gamma di condivisioni file in un pool di nodi di calcolo.

### <a name="azure-files-share"></a>Condivisione File di Azure

File di Azure è l'offerta standard di file system cloud di Azure. Per ulteriori informazioni su come ottenere uno dei parametri nell'esempio di codice per la configurazione di Mount, vedere [utilizzare una condivisione file di Azure](../storage/files/storage-how-to-use-files-windows.md).

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
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>file system BLOB di Azure

Un'altra opzione consiste nell'usare l'archiviazione BLOB di Azure tramite [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Per montare un file System BLOB è `AccountKey` necessario `SasKey` un o per l'account di archiviazione. Per informazioni su come ottenere queste chiavi, vedere [visualizzare le chiavi dell'account](../storage/common/storage-account-manage.md#view-account-keys-and-connection-string)o usare le firme di [accesso condiviso (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Per altre informazioni sull'uso di blobfuse, vedere le [domande frequenti sulla risoluzione dei problemi](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)di blobfuse. Per ottenere l'accesso predefinito alla directory montata blobfuse, eseguire l'attività come **amministratore**. Blobfuse monta la directory nello spazio utente e, al momento della creazione del pool, viene montata come radice. In Linux tutte le attività dell' **amministratore** sono radice. Tutte le opzioni per il modulo FUSE sono descritte nella [pagina di riferimento di fuse](http://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Oltre alla guida alla risoluzione dei problemi, i problemi di GitHub nel repository blobfuse sono un modo utile per verificare i problemi e le risoluzioni correnti di blobfuse. Per ulteriori informazioni, vedere [blobfuse issues](https://github.com/Azure/azure-storage-fuse/issues).

> [!NOTE]
> Blobfuse non è attualmente supportato in Debian. Per ulteriori informazioni, vedere [SKU supportati](#supported-skus) .

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

### <a name="network-file-system"></a>File System di rete

È anche possibile montare i file System di rete (NFS) nei nodi del pool, in modo da consentire l'accesso ai file System tradizionali tramite nodi Azure Batch. Potrebbe trattarsi di un singolo server NFS distribuito nel cloud o di un server NFS locale a cui si accede tramite una rete virtuale. In alternativa, è possibile sfruttare la soluzione per la cache in memoria distribuita [vFXT](../avere-vfxt/avere-vfxt-overview.md) , che offre una connettività senza problemi all'archiviazione locale, alla lettura dei dati su richiesta nella propria cache e offre prestazioni elevate e scalabilità a risorse di calcolo basate sul cloud. nodi.

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

### <a name="common-internet-file-system"></a>Common Internet file System

È anche possibile montare Common Internet file System (CIFS) in nodi del pool, in modo da consentire l'accesso ai file System tradizionali tramite nodi Azure Batch. CIFS è un protocollo di condivisione di file che fornisce un meccanismo aperto e multipiattaforma per la richiesta di file e servizi del server di rete. CIFS è basato sulla versione migliorata del protocollo SMB (Server Message Block) di Microsoft per la condivisione di file Internet e Intranet e viene usato per montare file System esterni nei nodi Windows. Per ulteriori informazioni su SMB, vedere [file server e SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

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

Se una configurazione di montaggio ha esito negativo, il nodo di calcolo nel pool avrà esito negativo e lo stato del nodo diventerà inutilizzabile. Per diagnosticare un errore di configurazione di montaggio, [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) controllare la proprietà per informazioni dettagliate sull'errore.

Per ottenere i file di log per il debug, usare [OutputFiles](batch-task-output-files.md) per `*.log` caricare i file. I `*.log` file contengono informazioni sul montaggio file System `AZ_BATCH_NODE_MOUNTS_DIR` nella posizione. Il formato dei file di log di `<type>-<mountDirOrDrive>.log` montaggio è il seguente: per ogni montaggio. Ad esempio, un `cifs` montaggio in una directory di montaggio `test` denominata avrà un file di log di montaggio `cifs-test.log`denominato:.

## <a name="supported-skus"></a>SKU supportati

| Pubblicato da | Offerta | SKU | Condivisione File di Azure | Blobfuse | Montaggio NFS | Montaggio CIFS |
|---|---|---|---|---|---|---|
| o batch | rendering-centos73 | rendering | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7,7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonico | UbuntuServer | 16,04-LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8, 9 | :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7,4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Nota: Supporta A_8 o 9 archiviazione</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul montaggio di una condivisione di File di Azure con [Windows](../storage/files/storage-how-to-use-files-windows.md) o [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Informazioni sull'uso e sul montaggio dei file system virtuali [blobfuse](https://github.com/Azure/azure-storage-fuse) .
- Per informazioni su NFS e le relative applicazioni, vedere [Panoramica di file System di rete](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) .
- Per altre informazioni su CIFS, vedere Cenni preliminari sul protocollo [SMB Microsoft e sul protocollo CIFS](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) .
