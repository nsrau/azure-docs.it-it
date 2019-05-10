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
ms.openlocfilehash: 4f721dc4fda5bef002c794d79dfd2f054f9eaf38
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511185"
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

*Cluster di hub eventi* offre distribuzioni a tenant singolo per i clienti con le esigenze più complesse di streaming. Questa offerta a tenant singolo ha un contratto di servizio del 99,99% garantite ed è disponibile solo nei nostri Dedicated piano tariffario. Un cluster di hub eventi può inserire milioni di eventi al secondo con una capacità garantita e latenza a frazioni di secondo. Gli spazi dei nomi e hub eventi creato all'interno del cluster dedicato includono tutte le funzionalità dell'offerta Standard e così via, ma senza limiti di ingresso. Contiene inoltre il famoso [acquisizione di hub eventi](event-hubs-capture-overview.md) funzionalità senza costi aggiuntivi, consentendo di automaticamente batch e log nei flussi di dati da archiviazione di Azure o Azure Data Lake. 

I cluster vengono effettuato il provisioning e addebitati dal **unità di capacità (CUs)**, una quantità di pre-allocata risorse di CPU e memoria. È possibile acquistare 1, 2, 4, 8, 12, 16 o 20 unità di capacità per ogni cluster. Quanto è possibile inserire e per unità di capacità di flusso dipende da vari fattori, ad esempio il numero di producer e consumer, forma, payload di dati in uscita frequenza (vedere i risultati benchmark sotto per altri dettagli). 

> [!NOTE]
> Tutti i cluster di hub eventi sono abilitati per Kafka per impostazione predefinita e supportano gli endpoint di Kafka che possono essere utilizzati da esistente applicazioni basate su Kafka. Con Kafka è abilitato nel cluster non influisce sui casi d'uso di non-Kafka; non vi è alcuna opzione o necessità di disabilitare il in un cluster Kafka.

## <a name="why-dedicated"></a>Il motivo per cui dedicato?

Dedicato di hub eventi offre tre vantaggi significativi per i clienti che necessitano di capacità a livello aziendale:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Singolo tenancy garanzie di capacità per migliorare le prestazioni

Un cluster dedicato garanzie di capacità su scala completa e in ingresso fino a gigabyte di dati in streaming con latenza di archiviazione e frazioni di secondo completamente durevole qualsiasi incrementarne l'utilizzo del traffico. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accesso alle funzionalità inclusivo ed esclusivo 
L'offerta dedicato comprende funzionalità come acquisire in alcun costo aggiuntivo, nonché accesso esclusivo a funzionalità presto disponibili, ad esempio BYOK. Il servizio gestisce inoltre il bilanciamento del carico, del sistema operativo degli aggiornamenti, patch di sicurezza e il partizionamento per il cliente, in modo che è possibile dedicare meno tempo nella manutenzione dell'infrastruttura e all'ora più sullo sviluppo di funzionalità lato client.  

#### <a name="cost-savings"></a>Risparmi sui costi
Con volumi di traffico in ingresso ad alta (> 100 unità elaborate), un cluster i costi significativamente inferiore all'ora rispetto all'acquisto di una quantità confrontabile di unità di velocità effettiva nell'offerta Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Dedicato di hub eventi, limiti e quote

L'offerta dedicato di hub eventi viene fatturato a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello dedicato offre tutte le funzionalità del piano Standard, ma con capacità su scala aziendale e i limiti per i clienti con carichi di lavoro intensi. 

| Funzionalità | Standard | Dedicato |
| --- |:---:|:---:|
| Larghezza di banda | 20 unità elaborate (fino a 40 unità elaborate) | 20 unità di capacità |
| Spazi dei nomi |  1 | 50 per ogni unità di capacità |
| Hub eventi |  10 | Nessun limite per hub eventi e gli argomenti |
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Dimensioni del messaggio | 1 milione di byte | 1 milione di byte |
| Partizioni | 40 per ogni spazio dei nomi | 2000 per unità di capacità |
| Gruppi di consumer | 20 per Hub eventi | Nessun limite per ogni unità di capacità, 1000 per hub eventi |
| Connessioni negoziate | 1.000 incluse | 100 K incluse |
| Conservazione messaggi | 7 giorni, incluso per ogni unità Elaborata 84 GB | 90 giorni, 10 TB, incluse per ogni unità di capacità |
| Acquisizione | Pagamento per ogni ora | Incluso |

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

Eseguire l'onboarding a hub eventi dedicato, contattare il [team di hub eventi](mailto:askeventhubs@microsoft.com). Il piano Dedicato è unico nel suo genere perché consente di usufruire di più onboarding pratico dal team di produzione di Hub eventi per ottenere la distribuzione flessibile desiderata. 

## <a name="faqs"></a>Domande frequenti

#### <a name="what-can-i-achieve-with-a-cluster"></a>Ciò che è possibile ottenere con un cluster?

Per un cluster di hub eventi, quanto più possibile di inserimento e flusso dipende vari fattori, ad esempio il producer, consumer, la frequenza con cui sono l'inserimento e l'elaborazione e molto altro ancora. 

La tabella seguente mostra i risultati dei benchmark ottenuti durante i test:

| Forma del payload | Destinatari | Larghezza di banda in ingresso| Messaggi in ingresso | Larghezza di banda in uscita | Messaggi in uscita | Unità elaborate totali | Unità elaborate per unità di capacità |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batch di 100 x 1 KB | 2 | 400 MB/sec | 400 k messaggi/sec | 800 MB/sec | 800 k messaggi/sec | 400 unità elaborate | 100 unità elaborate | 
| Batch di 10 x 10 KB | 2 | 666 MB/sec | 66.6 k messaggi/sec | 1,33 GB/sec | 133 k messaggi/sec | 666 unità elaborate | 166 unità elaborate |
| Batch di 6 x 32 KB | 1 | 1,05 GB/sec | i messaggi di 34 KB / sec | 1,05 GB/sec | 34 k messaggi/sec | 1000 unità elaborate | 250 unità elaborate |

Nei test sono stati usati i criteri seguenti:

- È stato usato un cluster di hub eventi dedicato a livelli con quattro unità di capacità (CUs). 
- L'hub eventi usato per l'inserimento aveva 200 partizioni. 
- I dati inseriti da tutte le partizioni vengono ricevuti da due applicazioni riceventi.

#### <a name="can-i-scale-down-my-cluster"></a>È possibile ridurre il cluster?

Dopo la creazione del cluster vengono fatturati per un minimo di 4 ore di utilizzo. Nella versione di anteprima dell'esperienza di Self-Service, è possibile inviare un [richiesta di supporto](https://ms.portal.azure.com/#create/Microsoft.Support) al team di hub eventi in *Technical > Quota > richiesta scalabilità verticale o ridimensionare un Cluster verso il basso dedicato*. Potrebbero occorrere fino a 7 giorni per completare la richiesta di ridimensionamento verso il basso il cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Come si integrano ripristino di emergenza geografico con il cluster?

È possibile associazione geografica uno spazio dei nomi in un cluster dedicato a livelli con un altro spazio dei nomi in un cluster dedicato a livelli. Ti consigliamo di non associazione uno spazio dei nomi di livello dedicato con uno spazio dei nomi nel nostro Standard offerta, poiché il limite di velocità effettiva sarà incompatibile che generano errori. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>È possibile eseguire la migrazione personali Standard degli spazi dei nomi che appartengono a un cluster dedicato a livelli?
Attualmente Supportiamo un processo automatizzato di migrazione per la migrazione dei dati di hub di eventi da uno spazio dei nomi Standard a un dedicata uno. Per eseguire la migrazione a un cluster dedicato a livelli, è consigliabile svuotamento delle eventuali sinistra dei messaggi di hub di eventi di livello Standard e sostituendo gli endpoint di connessione con quello dello spazio dei nomi dedicato.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni aggiuntive sul livello Dedicato di Hub eventi, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft. Per altre informazioni sui piani tariffari di Hub eventi, visitare i collegamenti seguenti:

- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi.
