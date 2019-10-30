---
title: Livelli di prestazioni dell'archiviazione BLOB in blocchi di Azure-archiviazione di Azure
description: Livelli di prestazioni per archiviazione BLOB di Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063247"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Livelli di prestazioni dell'archiviazione BLOB in blocchi di Azure

Poiché le aziende distribuiscono applicazioni native del cloud sensibili alle prestazioni, è importante disporre di opzioni per l'archiviazione dei dati conveniente a livelli di prestazioni diversi.

Archiviazione BLOB in blocchi di Azure offre due livelli di prestazioni diversi:

- Premium: ottimizzato per frequenze di transazioni elevate e latenza di archiviazione coerente a un solo numero
- Standard: ottimizzato per capacità elevata e velocità effettiva elevata

Le considerazioni seguenti si applicano ai diversi livelli di prestazioni:

- Le prestazioni standard sono disponibili in tutte le [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=storage). Le prestazioni Premium sono disponibili in [aree selezionate](https://azure.microsoft.com/global-infrastructure/services/?products=storage).
- Prestazioni Premium offre prezzi ottimizzati per le applicazioni con tariffe di transazione elevate, per [ridurre i costi di archiviazione totali](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) per questi carichi di lavoro.
- Per ottenere prestazioni Premium per i BLOB in blocchi, è necessario usare il tipo di account BlockBlobStorage.
- Le prestazioni standard sono disponibili con gli account di archiviazione per utilizzo generico V1, per utilizzo generico V2 e BLOB.
- Le prestazioni standard e Premium supportano entrambi [BLOB in blocchi con velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). I BLOB in blocchi con velocità effettiva elevata sono disponibili per prestazioni Premium superiori a 256 KiB. I BLOB in blocchi con velocità effettiva elevata sono disponibili per prestazioni standard superiori a 4 MB Put Block o Put Blob size.
- Le prestazioni Premium sono attualmente disponibili solo con archiviazione con ridondanza locale (con ridondanza locale).

## <a name="premium-performance"></a>Prestazioni Premium

Archiviazione BLOB in blocchi di prestazioni Premium rende disponibili i dati tramite hardware ad alte prestazioni. I dati vengono archiviati in unità SSD (Solid-State Drive) ottimizzate per la bassa latenza. Le SSD forniscono una velocità effettiva superiore rispetto ai dischi rigidi tradizionali.

L'archiviazione BLOB in blocchi di prestazioni Premium è ideale per i carichi di lavoro che richiedono tempi di risposta rapidi e coerenti. È ideale per i carichi di lavoro che eseguono molte transazioni di piccole dimensioni.

## <a name="standard-performance"></a>Prestazioni standard

Prestazioni standard supporta diversi [livelli di accesso](storage-blob-storage-tiers.md) per archiviare i dati in modo più conveniente. È ottimizzato per la capacità elevata e la velocità effettiva elevata su set di dati di grandi dimensioni.

## <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita dell'archiviazione BLOB offre criteri avanzati basati su regole:

- Premium-scadenza dei dati alla fine del ciclo di vita
- Dati di transizione standard al livello di accesso migliore e scadenza dei dati alla fine del ciclo di vita

Per altre informazioni, vedere [gestire il ciclo di vita dell'archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).

I dati archiviati in un account di archiviazione BLOB in blocchi Premium non possono essere spostati tra livelli ad accesso frequente, sporadico e archivio. Tuttavia, è possibile copiare i BLOB da un account di archiviazione BLOB in blocchi al livello di accesso frequente in un account *diverso* . Usare il [blocco put dall'API URL](/rest/api/storageservices/put-block-from-url) o [AzCopy V10](../common/storage-use-azcopy-v10.md) per copiare i dati in un altro account. Il **blocco put dall'API URL** copia in modo sincrono i dati nel server. La chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Valutazione di accesso frequente, ad accesso sporadico e archivio negli account di archiviazione BLOB e GPv2

- [Controllare la disponibilità di accesso frequente, accesso sporadico e archivio in base all'area](https://azure.microsoft.com/regions/#services)
- [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
- [Informazioni sulla reidratazione dei dati BLOB dal livello archivio](storage-blob-rehydration.md)
- [Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md)
- [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
- [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
