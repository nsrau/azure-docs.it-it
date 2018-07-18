---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 63593ff5f02f5e37fc25c988c4cef071a03a00b4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37066034"
---
L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archivio BLOB è ottimizzato per l'archiviazione di enormi quantità di dati non strutturati, come dati di testo o binari.

L'archivio BLOB è ideale per le operazioni seguenti:

* Invio di immagini o documenti direttamente in un browser.
* Archiviazione di file per l'accesso distribuito.
* Flussi audio e video.
* Scrittura in file di log.
* Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
* Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

È possibile accedere agli oggetti nell'archivio BLOB da ogni parte del mondo tramite HTTP o HTTPS. Gli utenti o le applicazioni client possono accedere ai BLOB tramite URL, l'[API REST di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage) oppure una libreria client di Archiviazione di Azure. Le librerie client di archiviazione sono disponibili per molti linguaggi, tra cui [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [PHP](http://azure.github.io/azure-storage-php/) e [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Concetti del servizio BLOB

L'archivio BLOB espone tre risorse: l'account di archiviazione, i contenitori nell'account e i BLOB in un contenitore. Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB (oggetti)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Account di archiviazione

L'accesso a tutti gli oggetti di dati in Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Contenitore

Un contenitore consente di organizzare un set di BLOB, in modo simile a una cartella in un file system. Tutti i BLOB risiedono in un contenitore. Un account di archiviazione può contenere un numero illimitato di contenitori, ciascuno dei quali può archiviare un numero illimitato di BLOB. Il nome del contenitore deve essere in lettere minuscole.

### <a name="blob"></a>BLOB
 
Archiviazione di Azure offre tre tipi di BLOB: BLOB in blocchi, BLOB di aggiunta e [BLOB di pagine](../articles/storage/blobs/storage-blob-pageblob-overview.md) (usati per i file VHD).

* I BLOB in blocchi archiviano testo e dati binari, fino a circa 4,7 TB. I BLOB in blocchi sono costituiti da blocchi di dati che possono essere gestiti individualmente.
* I BLOB di aggiunta sono costituiti da blocchi, analogamente ai BLOB in blocchi, ma sono ottimizzati per le operazioni di aggiunta. I BLOB di aggiunta sono ideali per scenari come la registrazione di dati delle macchine virtuali.
* I BLOB di pagine archiviano file ad accesso casuale con dimensioni fino a 8 TB. I BLOB di pagine archiviano i file VHD su cui si basano le VM.

Tutti i BLOB risiedono in un contenitore. Un contenitore è simile a una cartella in un file system. È possibile organizzare i BLOB in directory virtuali e attraversarli come se si trattasse di un file system. 

Se sono presenti set di dati molto grandi e i vincoli della rete ne impediscono il caricamento o il download in archivi BLOB tramite una connessione, è possibile spedire un set di unità disco rigido a Microsoft per importare o esportare i dati direttamente dal data center. Per altre informazioni, vedere [Usare il servizio di Importazione/Esportazione di Microsoft Azure per trasferire i dati nell'archiviazione BLOB](../articles/storage/common/storage-import-export-service.md).
  
Per informazioni sulla denominazione di contenitori e BLOB, vedere l'articolo relativo alla [denominazione e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
