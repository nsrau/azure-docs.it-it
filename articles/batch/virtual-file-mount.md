---
title: Montare un file system virtuale in un pool - Azure Batch Documenti Microsoft
description: Informazioni su come montare un file system virtuale in un pool Batch.Learn how to mount a virtual file system on a Batch pool.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919006"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montare un file system virtuale in un pool Batch

Azure Batch supporta ora il montaggio dell'archiviazione cloud o di un file system esterno nei nodi di calcolo Windows o Linux nei pool Batch.Azure Batch now supports mounting cloud storage or an external file system on Windows or Linux compute nodes in your Batch pools. Quando un nodo di calcolo si unisce a un pool, il file system virtuale viene montato e considerato come un'unità locale in tale nodo. È possibile montare file system come File di Azure, Archiviazione BLOB di Azure, NFS (Network File System), inclusa una [cache di Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)o CIFS (Common Internet File System).

In questo articolo verrà illustrato come montare un file system virtuale in un pool di nodi di calcolo utilizzando la libreria di [gestione batch per .NET.](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)

> [!NOTE]
> Il montaggio di un file system virtuale è supportato nei pool Batch creati in data o dopo il 2019-08-19. I pool batch creati prima del 2019-08-19 non supportano questa funzionalità.
> 
> Le API per il montaggio di file system in un nodo di calcolo fanno parte della libreria [Batch .NET.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Vantaggi del montaggio in piscina

Il montaggio del file system nel pool, invece di consentire alle attività di recuperare i propri dati da un set di dati di grandi dimensioni, semplifica l'accesso dei dati necessari alle attività.

Si consideri uno scenario con più attività che richiedono l'accesso a un set comune di dati, ad esempio il rendering di un filmato. Ogni attività esegue il rendering di uno o più fotogrammi alla volta dai file di scena. Montando un'unità che contiene i file di scena, è più facile per i nodi di calcolo accedere ai dati condivisi. Inoltre, il file system sottostante può essere scelto e ridimensionato in modo indipendente in base alle prestazioni e alla scalabilità (velocità effettiva e Operazioni di I/O al secondo) richieste dal numero di nodi di calcolo che accedono contemporaneamente ai dati. Ad esempio, una cache in memoria distribuita [Con vFXT](../avere-vfxt/avere-vfxt-overview.md) può essere utilizzata per supportare rendering di grandi dimensioni con migliaia di nodi di rendering simultanei, accedendo ai dati di origine che risiedono in locale. In alternativa, per i dati che risiedono già nell'archiviazione BLOB basata su cloud, è possibile usare [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) per montare questi dati come file system locale. Blobfuse è disponibile solo nei nodi Linux, tuttavia, File di Azure offre un flusso di lavoro simile ed è disponibile sia in Windows che in Linux.Blobfuse is only available on Linux nodes, however, [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) provides a similar workflow and is available on sia Windows and Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montare un file system virtuale in un pool  

Il montaggio di un file system virtuale in un pool rende il file system disponibile per ogni nodo di calcolo del pool. Il file system viene configurato quando un nodo di calcolo si unisce a un pool o quando il nodo viene riavviato o ricreato dell'immagine.

Per montare un file system `MountConfiguration` in un pool, creare un oggetto. Scegliere l'oggetto adatto al `AzureBlobFileSystemConfiguration`file `AzureFileShareConfiguration` `NfsMountConfiguration`system `CifsMountConfiguration`virtuale: , , , o .

Tutti gli oggetti di configurazione di montaggio richiedono i seguenti parametri di base. Alcune configurazioni di montaggio hanno parametri specifici per il file system in uso, che vengono descritti in modo più dettagliato negli esempi di codice.

- **Nome o origine dell'account:** per montare una condivisione file virtuale, è necessario il nome dell'account di archiviazione o del relativo origine.
- **Percorso di montaggio relativo o Origine**: percorso del file `fsmounts` system montato `AZ_BATCH_NODE_MOUNTS_DIR`sul nodo di calcolo, relativo alla directory standard accessibile sul nodo tramite . La posizione esatta varia a seconda del sistema operativo utilizzato sul nodo. Ad esempio, la posizione fisica in un nodo `mnt\batch\tasks\fsmounts`Ubuntu viene mappata a `mnt\resources\batch\tasks\fsmounts`, e in un nodo CentOS viene mappata a .
- **Opzioni di montaggio o opzioni blobfuse:** queste opzioni descrivono parametri specifici per il montaggio di un file system.

Una `MountConfiguration` volta creato l'oggetto, `MountConfigurationList` assegnare l'oggetto alla proprietà quando si crea il pool. Il file system viene montato quando un nodo si unisce a un pool o quando il nodo viene riavviato o ricreato dell'immagine.

Quando il file system viene `AZ_BATCH_NODE_MOUNTS_DIR` montato, viene creata una variabile di ambiente che punta al percorso dei file system montati e ai file di log, utili per la risoluzione dei problemi e il debug. I file di registro sono illustrati in modo più dettagliato nella sezione Diagnosticare gli errori di [montaggio.](#diagnose-mount-errors)  

> [!IMPORTANT]
> Il numero massimo di file system montati in un pool è 10. Per informazioni dettagliate e altri limiti, vedere Quote e limiti del [servizio Batch.See Batch service quotas and limits](batch-quota-limit.md#other-limits) for details and other limits.

## <a name="examples"></a>Esempi

Negli esempi di codice seguenti viene illustrato il montaggio di un'ampia gamma di condivisioni file in un pool di nodi di calcolo.

### <a name="azure-files-share"></a>Condivisione file di AzureAzure Files share

File di Azure è l'offerta standard del file system cloud di Azure.Azure Files is the standard Azure cloud file system offering. Per altre informazioni su come ottenere uno dei parametri nell'esempio di codice di configurazione di [montaggio,](../storage/files/storage-how-to-use-files-windows.md)vedere Usare una condivisione File di Azure.To learn more about how to get any of the parameters in the mount configuration code sample, see Use an Azure Files share.

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

### <a name="azure-blob-file-system"></a>File system BLOB di AzureAzure Blob file system

Un'altra opzione consiste nell'usare l'archiviazione BLOB di Azure tramite [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Il montaggio di un `AccountKey` `SasKey` file system BLOB richiede un o per l'account di archiviazione. Per informazioni sul recupero di queste chiavi, vedere [Gestire le chiavi](../storage/common/storage-account-keys-manage.md)di accesso dell'account di archiviazione o Uso delle firme di accesso [condiviso.](../storage/common/storage-dotnet-shared-access-signature-part-1.md) Per altre informazioni sull'uso di blobfuse, vedere le domande frequenti sulla [risoluzione dei problemi](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)relativi ai BLOB. Per ottenere l'accesso predefinito alla directory montata blob, eseguire l'attività come **amministratore**. Blobfuse monta la directory nello spazio utente e alla creazione del pool viene montata come radice. In Linux tutte le attività **dell'amministratore** sono root. Tutte le opzioni per il modulo FUSE sono descritte nella pagina di [riferimento FUSE.](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)

Oltre alla guida alla risoluzione dei problemi, i problemi di GitHub nel repository blobfuse sono un modo utile per controllare i problemi e le risoluzioni blobcorrenti correnti. Per altre informazioni, vedere problemi di [blobfuse](https://github.com/Azure/azure-storage-fuse/issues).

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

Rete File System (NFS) può anche essere montata in nodi di pool consentendo ai file system tradizionali di essere facilmente accessibili dai nodi batch di Azure.Network File Systems (NFS) can also be mounted to pool nodes allowing traditional file systems to easily access by Azure Batch nodes. Può trattarsi di un singolo server NFS distribuito nel cloud o di un server NFS locale a cui si accede tramite una rete virtuale. In alternativa, sfrutta la soluzione di cache in memoria distribuita [Avere vFXT,](../avere-vfxt/avere-vfxt-overview.md) che offre una connettività senza soluzione di continuità all'archiviazione locale, leggendo i dati su richiesta nella cache e offre prestazioni elevate e scalabilità ai nodi di calcolo basati su cloud.

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

### <a name="common-internet-file-system"></a>File system Internet comune

I file system Internet comuni (CIFS) possono anche essere montati in nodi di pool consentendo ai file system tradizionali di accedere facilmente ai nodi batch di Azure.Common Internet File Systems (CIFS) can also be mounted to pool nodes allowing traditional file systems to easily accessed by Azure Batch nodes. CIFS è un protocollo di condivisione file che fornisce un meccanismo aperto e multipiattaforma per la richiesta di servizi e file del server di rete. CIFS si basa sulla versione avanzata del protocollo SMB (Server Message Block) di Microsoft per la condivisione di file Internet e Intranet e viene utilizzato per montare file system esterni nei nodi Windows. Per ulteriori informazioni su SMB, vedere [File server e SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

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

## <a name="diagnose-mount-errors"></a>Diagnosticare gli errori di montaggioDiagnose mount errors

Se si verifica un errore in una configurazione di montaggio, il nodo di calcolo nel pool avrà esito negativo e lo stato del nodo diventa inutilizzabile. Per diagnosticare un errore [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) di configurazione di montaggio, esaminare la proprietà per informazioni dettagliate sull'errore.

Per ottenere i file di log per `*.log` il debug, utilizzare [OutputFiles](batch-task-output-files.md) per caricare i file. I `*.log` file contengono informazioni sul `AZ_BATCH_NODE_MOUNTS_DIR` montaggio del file system nel percorso. I file di registro di montaggio hanno il formato: `<type>-<mountDirOrDrive>.log` per ogni montaggio. Ad esempio, `cifs` un montaggio `test` in una directory di `cifs-test.log`montaggio denominata avrà un file di registro di montaggio denominato: .

## <a name="supported-skus"></a>SKU supportati

| Editore | Offerta | SKU | Condivisione file di AzureAzure Files Share | Blobfuse | Montaggio NFS | Montaggio CIFS |
|---|---|---|---|---|---|---|
|  o batch | rendering-centos73 | rendering | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Nota: Compatibile con CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Nota: supporta A_8 o 9 storage</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| WINDOWS | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul montaggio di una condivisione file di Azure con [Windows](../storage/files/storage-how-to-use-files-windows.md) o [Linux.](../storage/files/storage-how-to-use-files-linux.md)
- Informazioni sull'uso e il montaggio di file system virtuali [BLOBfuse.](https://github.com/Azure/azure-storage-fuse)
- Per informazioni su NFS e le relative applicazioni, [vedere Panoramica](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) del file system di rete.
- Per ulteriori informazioni su CIFS, vedere [Protocollo Microsoft SMB e Panoramica del protocollo CIFS.](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)
