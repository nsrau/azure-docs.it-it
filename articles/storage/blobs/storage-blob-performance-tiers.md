---
title: 'Livelli delle prestazioni di archiviazione BLOB a blocchi : Archiviazione di AzureBlock blob storage performance tiers - Azure Storage'
description: Illustra la differenza tra i livelli di prestazioni premium e standard per l'archiviazione BLOB con blocchi di Azure.Discusses the difference between premium and standard performance tiers for Azure block blob storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270216"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Livelli di prestazioni per l'archiviazione BLOB in blocchiPerformance tiers for block blob storage

Poiché le aziende distribuiscono applicazioni native cloud sensibili alle prestazioni, è importante disporre di opzioni per l'archiviazione dei dati conveniente a diversi livelli di prestazioni.

L'archiviazione BLOB di blocchi di Azure offre due diversi livelli di prestazioni:Azure block blob storage offers two different performance tiers:

- **Premium**: ottimizzato per tassi di transazione elevati e latenza di archiviazione coerente a una cifra
- **Standard**: ottimizzato per alta capacità e velocità effettiva elevata

Le considerazioni seguenti si applicano ai diversi livelli di prestazioni:The following considerations apply to the different performance tiers:

| Area |Prestazioni standard  |Prestazioni premium  |
|---------|---------|---------|
|Aree di disponibilità     |   Tutte le regioni      | In [alcune regioni](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|[Tipi di account di archiviazione](../common/storage-account-overview.md#types-of-storage-accounts) supportati     |     Scopo generale v2, BlobStorage, Generic Purpose v1    |    BlockBlobStorage     |
|Supporta BLOB [di blocchi ad alta velocità effettivaSupports high throughput block blobs](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Sì, con dimensioni superiori a 4 MiB PutBlock o PutBlob     |    Sì, con dimensioni superiori a 256 KiB PutBlock o PutBlob    |
|Ridondanza     |     Vedere [Tipi di account di archiviazioneSee Types of storage accounts](../common/storage-account-overview.md#types-of-storage-accounts)   |  Attualmente supporta solo l'archiviazione con ridondanza locale (LRS) e l'archiviazione con ridondanza di zona<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1 : il</sup> nome del L'archiviazione con ridondanza di zona è disponibile in alcune aree selezionate per gli account di archiviazione BLOB con blocchi di prestazioni Premium.zone-redundant storage ('re) is available in select regions for premium performance block blob storage accounts.</div>

Per quanto riguarda i costi, le prestazioni premium offrono prezzi ottimizzati per le applicazioni con tassi di transazione elevati per ridurre il [costo totale di archiviazione](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) per questi carichi di lavoro.

## <a name="premium-performance"></a>Prestazioni premium

L'archiviazione blob con blocchi di prestazioni Premium rende disponibili i dati tramite hardware ad alte prestazioni. I dati vengono archiviati su unità a stato solido (SSD) ottimizzate per una bassa latenza. Gli SSD offrono una maggiore velocità effettiva rispetto ai dischi rigidi tradizionali.

L'archiviazione delle prestazioni premium è ideale per i carichi di lavoro che richiedono tempi di risposta rapidi e coerenti. È consigliabile per i carichi di lavoro che eseguono molte transazioni di piccole dimensioni. I carichi di lavoro di esempio includono:Example workloads include:

- **Carichi di lavoro interattivi**. Questi carichi di lavoro richiedono aggiornamenti istantanei e feedback degli utenti, ad esempio applicazioni di e-commerce e di mapping. Ad esempio, in un'applicazione di e-commerce, gli elementi visualizzati meno frequentemente sono probabilmente non memorizzati nella cache. Tuttavia, devono essere immediatamente visualizzati al cliente su richiesta.

- **Analytics**. In uno scenario IoT, molte operazioni di scrittura più piccole potrebbero essere inviate al cloud ogni secondo. Grandi quantità di dati potrebbero essere prese in, aggregate a scopo di analisi e quindi eliminate quasi immediatamente. Le elevate funzionalità di inserimento dell'archiviazione BLOB a blocchi premium lo rendono efficiente per questo tipo di carico di lavoro.

- **Intelligenza artificiale/machine learning (AI/ML)**. AI/ML si occupa del consumo e dell'elaborazione di diversi tipi di dati come oggetti visivi, riconoscimento vocale e testo. Questo tipo di carico di lavoro di calcolo ad alte prestazioni gestisce grandi quantità di dati che richiedono una risposta rapida e tempi di inserimento efficienti per l'analisi dei dati.

- **Trasformazione dei dati**. I processi che richiedono modifiche, modifiche e conversione costanti dei dati richiedono aggiornamenti istantanei. Per una rappresentazione accurata dei dati, i consumer di questi dati devono vedere queste modifiche riflesse immediatamente.

## <a name="standard-performance"></a>Prestazioni standard

Le prestazioni standard supportano diversi livelli di accesso per archiviare [i](storage-blob-storage-tiers.md) dati nel modo più conveniente. È ottimizzato per alta capacità e velocità effettiva su set di dati di grandi dimensioni.

- **Set di dati**di backup e ripristino di emergenza . L'archiviazione standard delle prestazioni offre livelli efficienti in termini di costi, il che lo rende un caso d'uso perfetto sia per i set di dati di ripristino di emergenza a breve e a lungo termine, i backup secondari e l'archiviazione dei dati di conformità.

- **Contenuto multimediale**. Le immagini e i video spesso sono accessibili di frequente quando vengono creati e archiviati per la prima volta, ma questo tipo di contenuto viene usato meno spesso man mano che invecchia. L'archiviazione standard delle prestazioni offre livelli adatti per le esigenze di contenuto multimediale. 

- **Elaborazione di dati in blocco**. Questi tipi di carichi di lavoro sono adatti per l'archiviazione standard perché richiedono un'archiviazione ad alta velocità effettiva conveniente anziché una bassa latenza costante. Set di dati non elaborati di grandi dimensioni vengono gestiti per l'elaborazione ed eventualmente eseguire la migrazione a livelli più freddi.

## <a name="migrate-from-standard-to-premium"></a>Migrazione da standard a premium

Non è possibile convertire un account di archiviazione delle prestazioni standard esistente in un account di archiviazione BLOB a blocchi con prestazioni premium. Per eseguire la migrazione a un account di archiviazione premium per le prestazioni, è necessario creare un account BlockBlobStorage ed eseguire la migrazione dei dati nel nuovo account. Per altre informazioni, vedere [Creare un account BlockBlobStorage.](storage-blob-create-account-block-blob.md)

Per copiare BLOB tra account di archiviazione, è possibile usare la versione più recente dello strumento da riga di comando [AzCopy.To](../common/storage-use-azcopy-blobs.md) copy blobs between storage accounts, you can use the latest version of the AzCopy command-line tool. Sono inoltre disponibili altri strumenti, ad esempio Azure Data Factory, per lo spostamento e la trasformazione dei dati.

## <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita dell'archiviazione BLOB offre criteri avanzati basati su regole:Blob storage lifecycle management offers a rich, rule-based policy:

- **Premium**: Scadenza dei dati alla fine del ciclo di vita.
- **Standard:** consente di eseguire la transizione dei dati al livello di accesso migliore e di scadenza dei dati alla fine del ciclo di vita.

Per altre informazioni, vedere Gestire il ciclo di [vita dell'archiviazione BLOB](storage-lifecycle-management-concepts.md)di Azure.To learn more, see Manage the Azure Blob storage lifecycle.

Non è possibile spostare i dati archiviati in un account di archiviazione BLOB con blocchi premium tra livelli di accesso a caldo, a scelta rapida e ad archivio. Tuttavia, è possibile copiare BLOB da un account di archiviazione BLOB in blocchi al livello di accesso a caldo in un account *diverso.* Per copiare i dati in un account diverso, utilizzare l'API [Put Block From URL](/rest/api/storageservices/put-block-from-url) o [AzCopy v10](../common/storage-use-azcopy-v10.md). L'API **Put Block From URL** copia in modo sincrono i dati sul server. La chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Valutare gli account di archiviazione Hot, cool e archive negli account di archiviazione GPv2 e Blob.Evaluate hot, cool, and archive in GPv2 and Blob storage accounts.

- [Informazioni sulla reidratazione dei dati BLOB dal livello di archiviazioneLearn about rehydrating blob data from the archive tier](storage-blob-rehydration.md)
- [Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md)
- [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
- [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
