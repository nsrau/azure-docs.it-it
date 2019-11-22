---
title: Livelli di prestazioni dell'archiviazione BLOB in blocchi-archiviazione di Azure
description: Viene illustrata la differenza tra i livelli di prestazioni Premium e standard per l'archiviazione BLOB in blocchi di Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270216"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Livelli di prestazioni per l'archiviazione BLOB in blocchi

Poiché le aziende distribuiscono applicazioni native del cloud sensibili alle prestazioni, è importante disporre di opzioni per l'archiviazione dei dati conveniente a livelli di prestazioni diversi.

Archiviazione BLOB in blocchi di Azure offre due livelli di prestazioni diversi:

- **Premium**: ottimizzato per frequenze di transazioni elevate e latenza di archiviazione coerente a un solo numero
- **Standard**: ottimizzato per capacità elevata e velocità effettiva elevata

Le considerazioni seguenti si applicano ai diversi livelli di prestazioni:

| Area |Prestazioni standard  |Prestazioni Premium  |
|---------|---------|---------|
|Aree di disponibilità     |   Tutte le aree      | In [aree selezionate](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|[Tipi di account di archiviazione](../common/storage-account-overview.md#types-of-storage-accounts) supportati     |     Utilizzo generico V2, BlobStorage, utilizzo generico V1    |    BlockBlobStorage     |
|Supporta [BLOB in blocchi a velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Sì, con dimensioni superiori a 4 MiB PutBlock o PutBlob     |    Sì, con dimensioni maggiori di 256 KiB PutBlock o PutBlob    |
|Ridondanza     |     Vedere [tipi di account di archiviazione](../common/storage-account-overview.md#types-of-storage-accounts)   |  Attualmente supporta solo archiviazione con ridondanza locale (con ridondanza locale) e archiviazione redudant (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> L'archiviazione con ridondanza della zona (ZRS) è disponibile nelle aree selezionate per gli account di archiviazione BLOB in blocchi di prestazioni Premium.</div>

Per quanto riguarda i costi, le prestazioni Premium offrono prezzi ottimizzati per le applicazioni con tariffe di transazione elevate, per [ridurre i costi di archiviazione totali](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) per questi carichi di lavoro.

## <a name="premium-performance"></a>Prestazioni Premium

Archiviazione BLOB in blocchi di prestazioni Premium rende disponibili i dati tramite hardware ad alte prestazioni. I dati vengono archiviati in unità SSD (Solid-State Drive) ottimizzate per la bassa latenza. Le SSD forniscono una velocità effettiva superiore rispetto ai dischi rigidi tradizionali.

L'archiviazione delle prestazioni Premium è ideale per i carichi di lavoro che richiedono tempi di risposta rapidi e coerenti. È ideale per i carichi di lavoro che eseguono molte transazioni di piccole dimensioni. I carichi di lavoro di esempio includono:

- **Carichi di lavoro interattivi**. Questi carichi di lavoro richiedono aggiornamenti istantanei e commenti degli utenti, ad esempio applicazioni di e-commerce e di mapping. In un'applicazione di e-commerce, ad esempio, gli elementi visualizzati con minore frequenza probabilmente non vengono memorizzati nella cache. Tuttavia, devono essere immediatamente visualizzati al cliente su richiesta.

- **Analisi**. In uno scenario molto più piccolo, le operazioni di scrittura più piccole potrebbero essere inviate al cloud ogni secondo. È possibile che vengano rilevate grandi quantità di dati, aggregati per finalità di analisi, e quindi eliminati quasi immediatamente. Le funzionalità di inserimento elevato dell'archiviazione BLOB in blocchi Premium lo rendono efficiente per questo tipo di carico di lavoro.

- **Intelligenza artificiale/Machine Learning (ai/ml)** . AI/ML gestisce il consumo e l'elaborazione di tipi di dati diversi, ad esempio oggetti visivi, sintesi vocale e testo. Questo tipo di calcolo a prestazioni elevate occupa una grande quantità di dati che richiedono una risposta rapida e tempi di inserimento efficienti per l'analisi dei dati.

- **Trasformazione dei dati**. I processi che richiedono modifiche costanti, modifiche e conversione dei dati richiedono aggiornamenti istantanei. Per una rappresentazione accurata dei dati, i consumer di questi dati devono visualizzare immediatamente le modifiche riflesse.

## <a name="standard-performance"></a>Prestazioni standard

Prestazioni standard supporta diversi [livelli di accesso](storage-blob-storage-tiers.md) per archiviare i dati in modo più conveniente. È ottimizzato per la capacità elevata e la velocità effettiva elevata su set di dati di grandi dimensioni.

- **Set di impostazioni di backup e ripristino di emergenza**. Archiviazione prestazioni standard offre livelli convenienti, che lo rendono un caso d'uso perfetto per set di dati di ripristino di emergenza a breve e a lungo termine, backup secondari e archiviazione dei dati di conformità.

- **Contenuto multimediale**. Spesso è possibile accedere a immagini e video quando vengono creati e archiviati per la prima volta, ma questo tipo di contenuto viene usato meno spesso quando si invecchia. Archiviazione prestazioni standard offre livelli appropriati per le esigenze di contenuti multimediali. 

- **Elaborazione di dati in blocco**. Questi tipi di carichi di lavoro sono adatti per l'archiviazione standard, perché richiedono un archivio con velocità effettiva elevata e conveniente anziché una bassa latenza coerente. I set di dati di grandi dimensioni e non elaborati vengono gestiti in modo temporaneo per l'elaborazione ed eventualmente migrati a livelli più interessanti.

## <a name="migrate-from-standard-to-premium"></a>Eseguire la migrazione da standard a Premium

Non è possibile convertire un account di archiviazione prestazioni standard esistente in un account di archiviazione BLOB in blocchi con prestazioni Premium. Per eseguire la migrazione a un account di archiviazione con prestazioni Premium, è necessario creare un account BlockBlobStorage ed eseguire la migrazione dei dati al nuovo account. Per altre informazioni, vedere [creare un account BlockBlobStorage](storage-blob-create-account-block-blob.md).

Per copiare i BLOB tra gli account di archiviazione, è possibile usare la versione più recente dello strumento da riga di comando [AzCopy](../common/storage-use-azcopy-blobs.md) . Per lo spostamento e la trasformazione dei dati sono inoltre disponibili altri strumenti, ad esempio Azure Data Factory.

## <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita dell'archiviazione BLOB offre criteri avanzati basati su regole:

- **Premium**: scadono i dati alla fine del ciclo di vita.
- **Standard**: transizione dei dati al livello di accesso migliore e scadenza dei dati alla fine del ciclo di vita.

Per altre informazioni, vedere [gestire il ciclo di vita dell'archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).

Non è possibile spostare i dati archiviati in un account di archiviazione BLOB in blocchi Premium tra livelli ad accesso frequente, ad accesso sporadico e archivio. Tuttavia, è possibile copiare i BLOB da un account di archiviazione BLOB in blocchi al livello di accesso frequente in un account *diverso* . Per copiare i dati in un account diverso, usare il [blocco put dall'API URL](/rest/api/storageservices/put-block-from-url) o [AzCopy V10](../common/storage-use-azcopy-v10.md). Il **blocco put dall'API URL** copia in modo sincrono i dati nel server. La chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Valutazione di accesso frequente, sporadico e archivio negli account di archiviazione BLOB e GPv2.

- [Informazioni sulla reidratazione dei dati BLOB dal livello archivio](storage-blob-rehydration.md)
- [Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md)
- [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
- [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
