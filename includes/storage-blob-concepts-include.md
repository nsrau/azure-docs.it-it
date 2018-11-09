---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/17/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 7889fbc9373cbdfdfab891bf8b1cd610523c7032
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50088092"
---
L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archivio BLOB è ottimizzato per l'archiviazione di enormi quantità di dati non strutturati, come dati di testo o binari.

L'archivio BLOB è ideale per le operazioni seguenti:

* Invio di immagini o documenti direttamente in un browser.
* Archiviazione di file per l'accesso distribuito.
* Flussi audio e video.
* Scrittura in file di log.
* Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
* Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

Gli utenti o le applicazioni client possono accedere agli oggetti di archiviazione BLOB tramite HTTP o HTTPS&mdash;da ogni parte del mondo&mdash;tramite URL, l'[API REST di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage) oppure una libreria client di Archiviazione di Azure. Le librerie client di archiviazione sono disponibili per vari linguaggi, tra cui [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [PHP](http://azure.github.io/azure-storage-php/) e [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Concetti del servizio BLOB

L'archivio BLOB espone tre risorse: l'account di archiviazione, i contenitori nell'account e i BLOB in un contenitore. Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB (oggetti)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Account di archiviazione

L'accesso a tutti gli oggetti di dati in Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../articles/storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Contenitore

Un contenitore consente di organizzare un set di BLOB, in modo simile a una cartella in un file system. Tutti i BLOB risiedono in un contenitore. Un account di archiviazione può contenere un numero illimitato di contenitori, ciascuno dei quali può archiviare un numero illimitato di BLOB. 

  > [!NOTE]
  > Il nome del contenitore deve essere in lettere minuscole.

### <a name="blob"></a>BLOB
 
Archiviazione di Azure offre tre tipi di BLOB&mdash;BLOB in blocchi, BLOB di aggiunta e [BLOB di pagine](../articles/storage/blobs/storage-blob-pageblob-overview.md) (usati per i file VHD).

* I BLOB in blocchi archiviano testo e dati binari, fino a circa 4,7 TB. I BLOB in blocchi sono costituiti da blocchi di dati che possono essere gestiti individualmente.
* I BLOB di aggiunta sono costituiti da blocchi, analogamente ai BLOB in blocchi, ma sono ottimizzati per le operazioni di aggiunta. I BLOB di aggiunta sono ideali per scenari come la registrazione di dati delle macchine virtuali.
* I BLOB di pagine archiviano file ad accesso casuale con dimensioni fino a 8 TB. I BLOB di pagine archiviano i file VHD su cui si basano le VM.

Tutti i BLOB risiedono in un contenitore. Un contenitore è simile a una cartella in un file system. È possibile organizzare i BLOB in directory virtuali ed esplorarli come se si trattasse di un file system. 

Potrebbe esserci volte in cui set di dati molto grandi e vincoli di rete impediscono il caricamento di dati in un archivio BLOB tramite una connessione. È possibile usare [Azure Data Box Disk](../articles/databox/data-box-disk-overview.md) per richiedere a Microsoft dischi SSD. È quindi possibile copiare i dati in tali dischi e rispedirli a Microsoft per farli caricare nell'archivio BLOB.

Se si vuole esportare grandi quantità di dati dall'account di archiviazione, vedere [Usare il servizio Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](../articles/storage/common/storage-import-export-service.md).
  
Per informazioni sulla denominazione di contenitori e BLOB, vedere [Denominazione e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
