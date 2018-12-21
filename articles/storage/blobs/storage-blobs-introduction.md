---
title: Introduzione ad Archiviazione BLOB - Archiviazione di oggetti in Azure
description: Archiviazione BLOB di Azure archivia enormi quantità di dati di oggetti non strutturati, come testo o dati binari, e inoltre garantisce scalabilità e disponibilità elevate. I client possono accedere agli oggetti dati in Archiviazione BLOB da PowerShell o dall'interfaccia della riga di comando di Azure, da codice tramite le librerie client di Archiviazione di Azure o tramite REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 11/19/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 7628260efff34b52ca7d4bd4c35cce279d5474b3
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965411"
---
# <a name="introduction-to-azure-blob-storage"></a>Introduzione all'archiviazione BLOB di Azure

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Risorse dell'archivio BLOB

L’archiviazione BLOB offre tre tipi di risorse:

- **Account di archiviazione**. 
- Un **contenitore**  nell'account di archiviazione
- Un Oggetto **blob** in un contenitore 

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Account di archiviazione

Un account di archiviazione offre uno spazio dei nomi univoco in Azure per i dati. Tutti gli oggetti archiviati in Archiviazione di Azure hanno un indirizzo che include il nome univoco dell'account. La combinazione del nome dell'account e dell'endpoint di servizio di Archiviazione di Azure costituisce gli endpoint per l'account di archiviazione.

Ad esempio, se l'account di archiviazione si chiama *mystorageaccount*, l’endpoint predefinito per l’archivio BLOB è:

```
http://mystorageaccount.blob.core.windows.net 
```

Per creare un account di archiviazione, vedere [Creare un account di archiviazione](../common/storage-quickstart-create-account.md). Per altre informazioni sugli account di archiviazione, vedere la [panoramica degli account di archiviazione di Azure](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Contenitori

Un contenitore consente di organizzare un set di BLOB, in modo simile a una directory in un file system. Un account di archiviazione può contenere un numero illimitato di contenitori, ciascuno dei quali può archiviare un numero illimitato di BLOB. 

  > [!NOTE]
  > Il nome del contenitore deve essere in lettere minuscole. Per altre informazioni sui contenitori di denominazione, vedere [Contenitori di denominazione e di riferimento, BLOB e metadati](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobs
 
Archiviazione di Azure supporta tre tipi di BLOB:

* I **BLOB in blocchi** archiviano testo e dati binari, fino a circa 4,7 TB. I BLOB in blocchi sono costituiti da blocchi di dati che possono essere gestiti individualmente.
* **I BLOB di aggiunta** sono costituiti da blocchi, analogamente ai BLOB in blocchi, ma sono ottimizzati per le operazioni di aggiunta. I BLOB di aggiunta sono ideali per scenari come la registrazione di dati delle macchine virtuali.
* I **BLOB di pagine** archiviano file ad accesso casuale con dimensioni fino a 8 TB. I BLOB di pagine archiviano i file disco rigido virtuale (VHD) che vengono utilizzati come dischi per macchine virtuali di Azure. Per altre informazioni sui BLOB di pagine, vedere (../articles/storage/blobs/storage-blob-pageblob-overview.md)

Per altre informazioni sui diversi tipi di BLOB, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Spostare i dati nell'archivio BLOB

Esistono diverse soluzioni per la migrazione dei dati esistenti nell'archivio BLOB:

- **AzCopy** è uno strumento da riga di comando facile da usare per Windows e Linux che copia i dati da e verso l’archivio BLOB, tra contenitori o tra account di archiviazione. Per altre informazioni su AzCopy, vedere [Trasferire dati con AzCopy v10 (Anteprima)](../common/storage-use-azcopy-v10.md). 
- La **libreria di spostamento dei dati di archiviazione di Azure** è una libreria .NET per lo spostamento dei dati tra i servizi di archiviazione di Azure. L'utilità AzCopy viene compilata con la libreria di spostamento dei dati. Per ulteriori informazioni, vedere la [documentazione di riferimento](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement) per la libreria di spostamento dei dati. 
- **Azure Data Factory** supporta la copia dei dati da e verso l’archivio BLOB, tramite autenticazione con chiave dell'account, firma di accesso condiviso, entità servizio o identità gestite per le risorse di Azure. Per altre informazioni, vedere [Copiare dati da e verso l’archivio BLOB di Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** è un driver virtuale file system per l'archivio BLOB di Azure. È possibile usare blobfuse per accedere ai dati di BLOB in blocchi esistenti nell'account di archiviazione tramite il file system di Linux. Per altre informazioni, vedere [Come montare l'archivio BLOB come file system con blobfuse](storage-how-to-mount-container-linux.md).
- **Azure Data Box Disk** è un servizio per il trasferimento di dati locali nell'archivio BLOB, quando grandi set di dati o vincoli di rete complicano il caricamento dei dati in rete. È possibile usare [Azure Data Box Disk](../../databox/data-box-disk-overview.md) per richiedere a Microsoft dischi SSD. È quindi possibile copiare i dati in tali dischi e rispedirli a Microsoft per farli caricare nell'archivio BLOB.
- Il **servizio di importazione/esportazione di Azure** consente di esportare grandi quantità di dati dall'account di archiviazione a unità disco rigido fornite dall’utente e poi rispedite da Microsoft all’utente con i dati. Per altre informazioni, vedere [Usare il servizio di importazione/esportazione di Microsoft Azure per trasferire i dati nell'archivio BLOB](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account di archiviazione](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../common/storage-scalability-targets.md)
