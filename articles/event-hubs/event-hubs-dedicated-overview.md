---
title: Panoramica di Hub eventi dedicato - Hub eventi di Azure | Microsoft Docs
description: Questo articolo offre una panoramica di hub eventi Azure dedicato, che offre distribuzioni a tenant singolo di hub eventi.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138669"
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

I cluster di Azure hub eventi offrono distribuzioni a tenant singolo per i clienti con le esigenze più complesse di streaming. Questa offerta a tenant singolo ha un contratto di servizio del 99,99% garantite. È disponibile solo in Dedicated piano tariffario.

Un cluster di hub eventi può inserire milioni di eventi al secondo con una capacità garantita e latenza a frazioni di secondo. Gli spazi dei nomi e hub eventi creato all'interno del cluster dedicato includono tutte le funzionalità dell'offerta Standard e così via, ma senza limiti di ingresso. Include anche il [acquisizione di hub eventi](event-hubs-capture-overview.md) funzionalità senza costi aggiuntivi. È possibile usarlo automaticamente batch e log nei flussi di dati da archiviazione di Azure o Azure Data Lake.

Cluster dedicati vengono effettuato il provisioning e addebitato dall'unità di capacità (CUs). Unità di capacità sono una quantità di risorse di CPU e memoria preallocata. È possibile acquistare 1, 2, 4, 8, 12, 16 o 20 unità di capacità per ogni cluster. Quanto è in grado di inserire e flusso per unità di capacità dipende da fattori quali il numero di producer e consumer, la forma di payload e la frequenza in uscita.

Per altre informazioni, vedere la tabella con i risultati benchmark.

## <a name="why-use-event-hubs-dedicated"></a>Perché usare hub eventi dedicato?

Hub eventi dedicato offre tre vantaggi per i clienti che necessitano di capacità a livello aziendale.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Singolo tenancy garanzie di capacità per migliorare le prestazioni

Un cluster dedicato garanzie di capacità su scala completa. Può inserire un massimo di gigabyte di dati in streaming con latenza di archiviazione e le frazioni di secondo completamente durevole per adattarlo a picchi di traffico.

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accesso alle funzionalità inclusivo ed esclusivo 
L'offerta dedicata include funzionalità quali l'acquisizione senza costi aggiuntivi. Offre anche accesso esclusivo a funzionalità presto disponibili, ad esempio BYOK. Il servizio gestisce inoltre il bilanciamento del carico del sistema operativo degli aggiornamenti, patch di sicurezza e partizionamento. Con queste funzionalità, è possibile dedicare meno tempo nella manutenzione dell'infrastruttura e all'ora più sullo sviluppo di funzionalità lato client.

#### <a name="cost-savings"></a>Risparmi sui costi
Con volumi di traffico in ingresso elevata, un cluster i costi significativamente inferiore all'ora rispetto ai casi si acquistare una quantità confrontabile di unità elaborate (tu predeterminate) nell'offerta Standard. È un volume elevato > 100 unità elaborate.


## <a name="event-hubs-standard-vs-dedicated"></a>Visual Studio Standard di hub eventi. Dedicato

La tabella seguente confronta i livelli di servizio disponibili per Hub eventi. L'offerta dedicato di hub eventi viene fatturato in base a un prezzo mensile fisso, rispetto ai prezzi di utilizzo per la maggior parte delle funzionalità del livello Standard. Il livello dedicato offre tutte le funzionalità del piano Standard, ma con capacità su scala aziendale per i clienti con carichi di lavoro intensi.

| Funzionalità | Standard | Dedicato |
| --- |:---:|:---:|
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Unità di velocità effettiva (in entrata 1 MB/secondo, in uscita di 2 MB/secondo) | Pagamento per ogni ora | Incluso |
| Dimensioni dei messaggi | 1 MB | 1 MB |
| Partitions | 40 per ogni spazio dei nomi | 2.000 unità di capacità |
| Gruppi di consumer | 20 per hub eventi | 1.000 per hub eventi |
| Larghezza di banda massima | 20 unità elaborate (fino a 40 unità elaborate) | 20 unità di capacità |
| Connessioni negoziate | 1.000 incluse | 100.000 incluse |
| Conservazione di messaggi | Un giorno incluso | Fino a sette giorni incluso |
| Acquisizione | Pagamento per ogni ora | Incluso |

## <a name="what-can-i-achieve-with-a-cluster"></a>Ciò che è possibile ottenere con un cluster?

Per un cluster di hub eventi, quanto più possibile di inserimento e flusso dipende di Producer, gli utenti, la frequenza in corrispondenza del quale inserire ed elaborare e molto altro ancora.

Nella tabella seguente mostra i risultati benchmark che sono stati ottenuti durante il test.

| Forma del payload | Destinatari | Larghezza di banda in ingresso| Messaggi in ingresso | Larghezza di banda in uscita | Messaggi in uscita | Unità elaborate totali | Unità elaborate per unità di capacità |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batch di 100 x 1 KB | 2 | 400 MB/sec | 400.000 messaggi/sec | 800 MB/sec | 800.000 messaggi/sec | 400 unità elaborate | 100 unità elaborate | 
| Batch di 10 x 10 KB | 2 | 666 MB/sec | 66,600 messaggi/sec | 1,33 GB/sec | 133,000 messaggi/sec | 666 unità elaborate | 166 unità elaborate |
| Batch di 6 x 32 KB | 1 | 1,05 GB/sec | 34.000 messaggi/sec | 1,05 GB/sec | 34.000 messaggi/sec | 1.000 unità elaborate | 250 unità elaborate |

Il test, sono stati usati i criteri seguenti:

- È stato usato un cluster di hub eventi dedicato a livelli con quattro unità di capacità.
- L'hub eventi usato per l'inserimento aveva 200 partizioni.
- Due applicazioni destinatario ha ricevuti i dati che è stati inseriti. I dati vengono ricevuti da tutte le partizioni.

## <a name="use-event-hubs-dedicated"></a>Usare hub eventi dedicato

Usare hub eventi dedicato, [contattare il supporto per la fatturazione](https://ms.portal.azure.com/#create/Microsoft.Support) o il rappresentante Microsoft. È possibile aumentare o ridurre la capacità nel corso del mese in base alle esigenze specifiche, aggiungendo o rimuovendo le unità di capacità. Il team del prodotto di hub eventi consente di ottenere la distribuzione flessibile ottimale.

## <a name="next-steps"></a>Passaggi successivi

Contattare il rappresentante di vendita Microsoft o il supporto tecnico Microsoft per ottenere ulteriori informazioni sulla capacità di hub eventi dedicato. Per altre informazioni sui livelli di prezzi di hub eventi, usare i collegamenti seguenti:

- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). È anche possibile contattare il rappresentante di vendita Microsoft o il supporto tecnico Microsoft per ottenere ulteriori informazioni sulla capacità di hub eventi dedicato.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi.
