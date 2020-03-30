---
title: Panoramica di Hub eventi dedicato - Hub eventi di Azure | Microsoft Docs
description: Questo articolo offre una panoramica degli hub eventi di Azure dedicati, che offre distribuzioni a tenant singolo degli hub eventi.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516756"
---
# <a name="overview-of-event-hubs-dedicated"></a>Panoramica di Hub eventi dedicato

I cluster Hub eventi offrono distribuzioni single-tenant per i clienti con le esigenze di streaming più *esigenti.* Questa offerta per single-tenant ha un accordo di servizio garantito del 99,99% ed è disponibile solo nel nostro piano tariffario dedicato. Un cluster Hub eventi può in ingresso milioni di eventi al secondo con capacità garantita e latenza inferiore al secondo. Gli spazi dei nomi e gli hub eventi creati all'interno del cluster dedicato includono tutte le funzionalità dell'offerta Standard e altro ancora, ma senza limiti di ingresso. Include anche la popolare funzionalità event Hubs Capture senza costi aggiuntivi, consentendo di eseguire automaticamente il batch e il log dei flussi di dati in Archiviazione di Azure o Azure Data Lake.It also includes the popular [Event Hubs Capture](event-hubs-capture-overview.md) feature at no additional cost, allowing you to automatically batch and log data streams to Azure Storage or Azure Data Lake. 

Il provisioning dei cluster viene eseguito e fatturato da **Capacity Units (CU),** una quantità preallocata di risorse di CPU e memoria. È possibile acquistare 1, 2, 4, 8, 12, 16 o 20 unità di capacità per ogni cluster. Quanto è possibile ingerire e trasmettere per CU dipende da una varietà di fattori, ad esempio il numero di produttori e consumatori, la forma del carico utile, la frequenza di uscita (vedere i risultati di riferimento di seguito per ulteriori dettagli). 

> [!NOTE]
> Tutti i cluster Hub eventi sono abilitati per Kafka per impostazione predefinita e supportano gli endpoint Kafka che possono essere utilizzati dalle applicazioni basate su Kafka esistenti. L'abilitazione di Kafka nel cluster non influisce sui casi d'uso non Kafka; non è disponibile alcuna opzione o è necessario disabilitare Kafka in un cluster.

## <a name="why-dedicated"></a>Perché dedicato?

Hub eventi dedicati offre tre vantaggi interessanti per i clienti che necessitano di capacità di livello enterprise:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>La mono-tenancy garantisce una migliore performance

Un cluster dedicato garantisce capacità su larga scala e può immettere fino a gigabyte di dati in streaming con archiviazione completamente durevole e latenza inferiore al secondo per soddisfare qualsiasi burst nel traffico. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Accesso inclusivo ed esclusivo alle funzioni 
L'offerta dedicata include funzionalità come Capture senza costi aggiuntivi, così come l'accesso esclusivo alle prossime funzionalità come Bring Your Own Key (BYOK). Il servizio gestisce anche il bilanciamento del carico, gli aggiornamenti del sistema operativo, le patch di sicurezza e il partizionamento per il cliente, in modo da poter dedicare meno tempo alla manutenzione dell'infrastruttura e più tempo alla creazione di funzionalità lato client.  

#### <a name="cost-savings"></a>Risparmio sui costi
Con volumi in ingresso elevati (>100 TUs), un cluster costa molto meno all'ora rispetto all'acquisto di una quantità comparabile di unità di velocità effettiva nell'offerta Standard.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Quote e limiti dedicati agli hub eventi

L'offerta Event Hubs Dedicated viene fatturata a un prezzo mensile fisso, con un minimo di 4 ore di utilizzo. Il livello Dedicato offre tutte le funzionalità del piano Standard, ma con capacità di scalabilità aziendale e limiti per i clienti con carichi di lavoro più impegnativi. 

| Funzionalità | Standard | Dedicated |
| --- |:---:|:---:|
| Larghezza di banda | 20 CU (fino a 40 CU) | 20 CU |
| Spazi dei nomi |  1 | 50 per CU |
| Hub eventi |  10 per spazio dei nomi | 1000 per spazio dei nomi |
| Eventi in ingresso | Pagamento per ogni milione di eventi | Incluso |
| Dimensioni del messaggio | 1 milione di byte | 1 milione di byte |
| Partizioni | 32 per hub eventi | 1024 per hub eventi |
| Gruppi di consumer | 20 per hub eventi | Nessun limite per CU, 1000 per hub eventi |
| Connessioni negoziate | 1.000 inclusi, 5.000 max | 100 K inclusi e max |
| Conservazione dei messaggi | 7 giorni, 84 GB inclusi per TU | 90 giorni, 10 TB inclusi per CU |
| Acquisizione | Pagamento per ogni ora | Incluso |

## <a name="how-to-onboard"></a>Modalità di esecuzione dell'onboarding

L'esperienza self-service per [creare un cluster di hub eventi](event-hubs-dedicated-cluster-create-portal.md) tramite il portale di [Azure](https://aka.ms/eventhubsclusterquickstart) è ora in anteprima. Se hai domande o hai bisogno di aiuto per l'onboarding negli hub eventi dedicati, contatta il [team degli Hub eventi.](mailto:askeventhubs@microsoft.com)

## <a name="faqs"></a>Domande frequenti

#### <a name="what-can-i-achieve-with-a-cluster"></a>Cosa posso ottenere con un cluster?

Per un cluster Hub eventi, quanto è possibile eseguire l'inserimento e lo streaming dipende da vari fattori, ad esempio i produttori, i consumer, la velocità con cui si sta ingerendo e l'elaborazione e molto altro ancora. 

La tabella seguente mostra i risultati dei benchmark ottenuti durante i test:

| Forma del payload | Destinatari | Larghezza di banda in ingresso| Messaggi in ingresso | Larghezza di banda in uscita | Messaggi in uscita | Unità elaborate totali | Unità elaborate per unità di capacità |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batch di 100 x 1 KB | 2 | 400 MB/sec | 400k messaggi/sec | 800 MB/sec | 800k messaggi/sec | 400 unità elaborate | 100 unità elaborate | 
| Batch di 10 x 10 KB | 2 | 666 MB/sec | 66,6k messaggi/sec | 1,33 GB/sec | 133k messaggi/sec | 666 unità elaborate | 166 unità elaborate |
| Batch di 6 x 32 KB | 1 | 1,05 GB/sec | Messaggi 34k/ secondi | 1,05 GB/sec | 34k messaggi/sec | 1000 unità elaborate | 250 unità elaborate |

Nei test sono stati usati i criteri seguenti:

- È stato utilizzato un cluster Hub eventi a livello dedicato con quattro unità di capacità (CU). 
- L'hub eventi usato per l'inserimento aveva 200 partizioni. 
- I dati inseriti da tutte le partizioni vengono ricevuti da due applicazioni riceventi.

#### <a name="can-i-scale-updown-my-cluster"></a>È possibile aumentare o ridurre il cluster?

Dopo la creazione, i cluster vengono fatturati per un minimo di 4 ore di utilizzo. Nella versione di anteprima dell'esperienza self-service, è possibile inviare una richiesta di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) al team Hub eventi in > tecnico > richiesta di *scalabilità verticale o verticale* del cluster per aumentare o ridurre il cluster. Potrebbero essere completati fino a 7 giorni per completare la richiesta di ridimensionamento del cluster. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Come funzionerà Geo-DR con il mio cluster?

È possibile eseguire l'associazione geografica di uno spazio dei nomi in un cluster a livello dedicato a un altro spazio dei nomi in un cluster a livello dedicato. Non è consigliabile associare uno spazio dei nomi del livello dedicato a uno spazio dei nomi nell'offerta Standard, poiché il limite di velocità effettiva sarà incompatibile e genererà errori. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>È possibile eseguire la migrazione degli spazi dei nomi Standard in modo che appartengano a un cluster a livello dedicato?
Attualmente non è supportato un processo di migrazione automatica per la migrazione dei dati degli hub eventi da uno spazio dei nomi Standard a uno dedicato. 

## <a name="next-steps"></a>Passaggi successivi

Contattare il rappresentante di vendita Microsoft o il supporto tecnico Microsoft per ulteriori informazioni sugli hub eventi dedicati. È anche possibile creare un cluster o ottenere ulteriori informazioni sui livelli dei prezzi di Hub eventi visitando i collegamenti seguenti:

- [Creare un cluster Hub eventi tramite il portale di AzureCreate an Event Hubs cluster through the Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Tariffe di Hub eventi dedicato](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni aggiuntive sulla capacità di Hub eventi dedicato, contattare il rappresentante di vendita Microsoft o il Supporto tecnico Microsoft.
- Le [Domande frequenti si Hub eventi](event-hubs-faq.md) contengono informazioni sui prezzi e risposte ad alcune domande frequenti sugli Hub eventi.
