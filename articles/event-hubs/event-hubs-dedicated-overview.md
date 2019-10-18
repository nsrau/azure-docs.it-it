---
title: Panoramica di Hub eventi dedicato - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica dell'hub eventi di Azure dedicato, che offre distribuzioni a tenant singolo di hub eventi.
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
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516756"
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

I *cluster di hub eventi* offrono distribuzioni a tenant singolo per i clienti con le esigenze di streaming più complesse. Questa offerta a tenant singolo ha un contratto di servizio garantito del 99,99% ed è disponibile solo per il piano tariffario Dedicato. Un cluster di hub eventi può trasferire milioni di eventi al secondo con capacità garantita e latenza di sottosecondo. Gli spazi dei nomi e gli hub eventi creati nel cluster dedicato includono tutte le funzionalità dell'offerta standard e altro ancora, ma senza limiti di ingresso. Include anche la nota funzionalità di [acquisizione di hub eventi](event-hubs-capture-overview.md) senza costi aggiuntivi, che consente di raggruppare e registrare automaticamente i flussi di dati in archiviazione di Azure o in Azure Data Lake. 

Il provisioning dei cluster viene effettuato e fatturato da **unità di capacità**, una quantità preallocata di risorse di CPU e memoria. È possibile acquistare 1, 2, 4, 8, 12, 16 o 20 unità di capacità per ogni cluster. La quantità di dati che è possibile inserire e trasmettere in streaming per CU dipende da diversi fattori, ad esempio il numero di Producer e consumer, la forma del payload e la velocità in uscita. per altri dettagli, vedere i risultati del benchmark. 

> [!NOTE]
> Per impostazione predefinita, tutti i cluster di hub eventi sono abilitati per Kafka e supportano gli endpoint Kafka che possono essere usati dalle applicazioni esistenti basate su Kafka. L'abilitazione di Kafka nel cluster non influisce sui casi d'uso non Kafka; non è possibile o non è necessario disabilitare Kafka in un cluster.

## <a name="why-dedicated"></a>Perché dedicate?

Hub eventi dedicati offre tre vantaggi interessanti per i clienti che necessitano di capacità a livello aziendale:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-tenant garantisce la capacità per ottenere prestazioni migliori

Un cluster dedicato garantisce una capacità su vasta scala e può trasferire fino a gigabyte di dati in streaming con archiviazione completamente durevole e latenza di sottosecondo per supportare eventuali picchi di traffico. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accesso inclusivo ed esclusivo alle funzionalità 
L'offerta dedicata include funzionalità come l'acquisizione senza costi aggiuntivi, nonché l'accesso esclusivo alle funzionalità future come Bring Your Own Key (BYOK). Il servizio gestisce anche il bilanciamento del carico, gli aggiornamenti del sistema operativo, le patch di sicurezza e il partizionamento per il cliente, in modo da poter dedicare meno tempo alla manutenzione dell'infrastruttura e più tempo alla creazione di funzionalità lato client.  

#### <a name="cost-savings"></a>Risparmio sui costi
In volumi in ingresso elevati (> 100 TUs), un cluster costi significativamente inferiore all'ora rispetto all'acquisto di una quantità comparabile di unità di velocità effettiva nell'offerta standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Quote e limiti Hub eventi Dedicato

L'offerta Hub eventi Dedicato viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello dedicato offre tutte le funzionalità del piano standard, ma con capacità e limiti di scalabilità aziendale per i clienti con carichi di lavoro complessi. 

| Funzionalità | Standard | Dedicata |
| --- |:---:|:---:|
| Larghezza di banda | 20 TUs (fino a 40 TUs) | 20 CUs |
| Spazi dei nomi |  1 | 50 per CU |
| Hub eventi |  10 per spazio dei nomi | 1000 per spazio dei nomi |
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Dimensione messaggi | 1 milione byte | 1 milione byte |
| Partitions | 32 per hub eventi | 1024 per hub eventi |
| Gruppi consumer | 20 per hub eventi | Nessun limite per CU, 1000 per hub eventi |
| Connessioni negoziate | 1\.000 incluso, 5.000 max | 100 K inclusi e Max |
| Conservazione di messaggi | 7 giorni, 84 GB inclusi per TU | 90 giorni, 10 TB inclusi per CU |
| Acquisizione | Pagamento per ogni ora | Incluso |

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

L'esperienza self-service per [creare un cluster di hub eventi](event-hubs-dedicated-cluster-create-portal.md) tramite il [portale di Azure](https://aka.ms/eventhubsclusterquickstart) è ora disponibile in anteprima. Per eventuali domande o richieste di supporto per l'onboarding in Hub eventi Dedicato, contattare il [team di hub eventi](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Domande frequenti

#### <a name="what-can-i-achieve-with-a-cluster"></a>Cosa si può ottenere con un cluster?

Per un cluster di hub eventi, quanto è possibile inserire e trasmettere in streaming dipende da diversi fattori, ad esempio produttori, consumer, velocità di inserimento ed elaborazione e molto altro ancora. 

La tabella seguente mostra i risultati dei benchmark ottenuti durante i test:

| Forma del payload | Destinatari | Larghezza di banda in ingresso| Messaggi in ingresso | Larghezza di banda in uscita | Messaggi in uscita | Unità elaborate totali | Unità elaborate per unità di capacità |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batch di 100 x 1 KB | 2 | 400 MB/sec | messaggi 400.000/sec | 800 MB/sec | messaggi 800K/sec | 400 unità elaborate | 100 unità elaborate | 
| Batch di 10 x 10 KB | 2 | 666 MB/sec | messaggi 66.6/sec | 1,33 GB/sec | messaggi 133K/sec | 666 unità elaborate | 166 unità elaborate |
| Batch di 6 x 32 KB | 1 | 1,05 GB/sec | messaggi 34K/sec | 1,05 GB/sec | messaggi 34K/sec | 1000 unità elaborate | 250 unità elaborate |

Nei test sono stati usati i criteri seguenti:

- È stato usato un cluster di hub eventi di livello dedicato con quattro unità di capacità. 
- L'hub eventi usato per l'inserimento aveva 200 partizioni. 
- I dati inseriti da tutte le partizioni vengono ricevuti da due applicazioni riceventi.

#### <a name="can-i-scale-updown-my-cluster"></a>È possibile aumentare/ridurre il cluster?

Al termine della creazione, i cluster vengono fatturati per almeno 4 ore di utilizzo. Nella versione di anteprima dell'esperienza self-service è possibile inviare una richiesta di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) al team di hub eventi in *Technical > quota > richiesta di scalabilità verticale o verticale del cluster dedicato* per la scalabilità del cluster. Potrebbero essere richiesti fino a 7 giorni per completare la richiesta di ridimensionamento del cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Come funzionerà il ripristino di emergenza geografico con il cluster?

È possibile associare uno spazio dei nomi in un cluster a livello dedicato con un altro spazio dei nomi in un cluster di livello dedicato. Non è consigliabile associare uno spazio dei nomi a livello dedicato con uno spazio dei nomi nell'offerta standard, perché il limite di velocità effettiva sarà incompatibile, che genererà errori. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>È possibile eseguire la migrazione degli spazi dei nomi standard in modo che appartengano a un cluster di livello dedicato?
Attualmente non è supportato un processo di migrazione automatizzato per la migrazione dei dati di hub eventi da uno spazio dei nomi standard a uno dedicato. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui Hub eventi Dedicato, contattare il rappresentante di vendita Microsoft o supporto tecnico Microsoft. È anche possibile creare un cluster o altre informazioni sui piani tariffari di hub eventi visitando i collegamenti seguenti:

- [Creare un cluster di hub eventi tramite il portale di Azure](https://aka.ms/eventhubsclusterquickstart) 
- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi.
