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
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708003"
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

*Cluster di hub eventi* offre distribuzioni a tenant singolo per i clienti con le esigenze più complesse di streaming. Questa offerta a tenant singolo ha un contratto di servizio del 99,99% garantite ed è disponibile solo nei nostri Dedicated piano tariffario. Un cluster di hub eventi può inserire milioni di eventi al secondo con una capacità garantita e latenza a frazioni di secondo. Gli spazi dei nomi e hub eventi creato all'interno del cluster dedicato includono tutte le funzionalità dell'offerta Standard e così via, ma senza limiti di ingresso. Contiene inoltre il famoso [acquisizione di hub eventi](event-hubs-capture-overview.md) funzionalità senza costi aggiuntivi, consentendo di automaticamente batch e log nei flussi di dati da archiviazione di Azure o Azure Data Lake. 

Provisioning e fatturazione basata sul cluster dedicati **unità di capacità (CUs)**, una quantità di pre-allocata risorse di CPU e memoria. È possibile acquistare 1, 2, 4, 8, 12, 16 o 20 unità di capacità per ogni cluster. Quanto è possibile inserire e per unità di capacità di flusso dipende da vari fattori, ad esempio il numero di producer e consumer, forma, payload di dati in uscita frequenza (vedere i risultati benchmark sotto per altri dettagli). 

## <a name="why-dedicated"></a>Il motivo per cui dedicato?

Dedicato di hub eventi offre tre vantaggi significativi per i clienti che necessitano di capacità a livello aziendale:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Singolo tenancy garanzie di capacità per migliorare le prestazioni

Un cluster dedicato garanzie di capacità su scala completa e in ingresso fino a gigabyte di dati in streaming con latenza di archiviazione e frazioni di secondo completamente durevole qualsiasi incrementarne l'utilizzo del traffico. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accesso alle funzionalità inclusivo ed esclusivo 
L'offerta dedicato comprende funzionalità come acquisire in alcun costo aggiuntivo, nonché accesso esclusivo a funzionalità presto disponibili, ad esempio BYOK. Il servizio gestisce inoltre il bilanciamento del carico, del sistema operativo degli aggiornamenti, patch di sicurezza e il partizionamento per il cliente, in modo che è possibile dedicare meno tempo nella manutenzione dell'infrastruttura e all'ora più sullo sviluppo di funzionalità lato client.  

#### <a name="cost-savings"></a>Risparmi sui costi
Con volumi di traffico in ingresso ad alta (> 100 unità elaborate), un cluster i costi significativamente inferiore all'ora rispetto all'acquisto di una quantità confrontabile di unità di velocità effettiva nell'offerta Standard.


## <a name="event-hubs-standard-vs-dedicated"></a>Visual Studio Standard di hub eventi. Dedicato

La tabella seguente confronta i livelli di servizio disponibili per Hub eventi. L'offerta dedicato di hub eventi viene fatturato in base a un prezzo mensile fisso, rispetto ai prezzi di utilizzo per la maggior parte delle funzionalità del livello Standard. Il livello Dedicato offre tutte le funzionalità del piano Standard, ma con capacità su scala aziendale per i clienti con carichi di lavoro intensi. 

| Funzionalità | Standard | Dedicato |
| --- |:---:|:---:|
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Unità di velocità effettiva (in entrata 1 MB/secondo, in uscita di 2 MB/secondo) | Pagamento per ogni ora | Incluso |
| Dimensioni del messaggio | 1 MB | 1 MB |
| Partitions | 40 per ogni spazio dei nomi | 2000 per unità di capacità |
| Gruppi di consumer | 20 per Hub eventi | 1000 per Hub eventi |
| Max. Larghezza di banda | 20 unità elaborate (fino a 40 unità elaborate)    | 20 unità di capacità |
| Connessioni negoziate | 1.000 incluse | 100 K incluse |
| Conservazione dei messaggi | 1 giorno incluso | Fino a 7 giorni inclusi |
| Acquisizione | Pagamento per ogni ora | Incluso |

## <a name="what-can-i-achieve-with-a-cluster"></a>Ciò che è possibile ottenere con un cluster?

Per un cluster di hub eventi, quanto più possibile di inserimento e flusso dipende vari fattori, ad esempio il producer, consumer, la frequenza con cui sono l'inserimento e l'elaborazione e molto altro ancora. 

La tabella seguente mostra i risultati dei benchmark ottenuti durante i test:

| Forma del payload | Destinatari | Larghezza di banda in ingresso| Messaggi in ingresso | Larghezza di banda in uscita | Messaggi in uscita | Unità elaborate totali | Unità elaborate per unità di capacità |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batch di 100 x 1 KB | 2 | 400 MB/sec | 400000 messaggi/sec | 800 MB/sec | 800000 messaggi/sec | 400 unità elaborate | 100 unità elaborate | 
| Batch di 10 x 10 KB | 2 | 666 MB/sec | 666000 messaggi/sec | 1,33 GB/sec | 133000 messaggi/sec | 666 unità elaborate | 166 unità elaborate |
| Batch di 6 x 32 KB | 1 | 1,05 GB/sec | 34000 messaggi/sec | 1,05 GB/sec | 34000 messaggi/sec | 1000 unità elaborate | 250 unità elaborate |

Nei test sono stati usati i criteri seguenti:

- È stato usato un cluster di hub eventi dedicato a livelli con quattro unità di capacità (CUs). 
- L'hub eventi usato per l'inserimento aveva 200 partizioni. 
- I dati inseriti da tutte le partizioni vengono ricevuti da due applicazioni riceventi.

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

Per eseguire l'onboarding di questo SKU, [contattare il supporto per la fatturazione](https://ms.portal.azure.com/#create/Microsoft.Support) o il rappresentante Microsoft. È possibile aumentare o ridurre la capacità nel corso del mese in base alle esigenze specifiche, aggiungendo o rimuovendo le unità di capacità. Il piano Dedicato è unico nel suo genere perché consente di usufruire di più onboarding pratico dal team di produzione di Hub eventi per ottenere la distribuzione flessibile desiderata. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive sul livello Dedicato di Hub eventi, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft. Per altre informazioni sui piani tariffari di Hub eventi, visitare i collegamenti seguenti:

- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi.
