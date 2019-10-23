---
title: Confrontare i servizi di messaggistica di Azure
description: Descrive i tre servizi di messaggistica di Azure - Griglia di eventi di Azure, Hub eventi di Azure e bus di servizio di Azure. Consiglia il servizio da usare a seconda dello scenario.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 10/22/2019
ms.author: spelluru
ms.custom: seodec18
ms.openlocfilehash: b68bd69c6d00ac177466f8fdaabd89649c910287
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790297"
---
# <a name="choose-between-azure-messaging-services---event-grid-event-hubs-and-service-bus"></a>Scegliere tra i servizi di messaggistica di Azure - Griglia di eventi, Hub eventi, bus di servizio

Azure offre tre servizi che agevolano il recapito di messaggi di eventi tramite una soluzione. Questi servizi sono:

* [Griglia di eventi](/azure/event-grid/)
* [Hub eventi](/azure/event-hubs/)
* [Bus di servizio](/azure/service-bus-messaging/)

Nonostante alcune analogie, ogni servizio è progettato per determinati scenari. Questo articolo descrive le differenze tra i servizi e consente di scegliere quello più adatto all'applicazione. In molti casi, i servizi di messaggistica sono complementari e possono essere usati insieme.

## <a name="event-vs-message-services"></a>Servizi di eventi e di messaggi

È importante tenere presente la differenza tra servizi che recapitano un evento e servizi che recapitano un messaggio.

### <a name="event"></a>Event

Un evento è una notifica leggera di una condizione o di una modifica di stato. L'autore dell'evento non ha aspettative su come viene gestito l'evento. Il consumer dell'evento decide come procedere con la notifica. Gli eventi possono essere unità separate o fare parte di una serie.

Gli eventi separati segnalano una modifica dello stato ed possibile intervenire. Per eseguire il passaggio successivo, il consumer deve solo sapere che è accaduto qualcosa. I dati dell'evento contengono informazioni su ciò che è accaduto, ma non includono i dati che hanno attivato l'evento. Un evento ad esempio notifica ai consumer che è stato creato un file. Può contenere informazioni generali sul file, ma non il file stesso. Gli eventi separati sono ideali per le soluzioni [senza server](https://azure.com/serverless) che devono essere ridimensionate.

Gli eventi di una serie segnalano una condizione e sono analizzabili. Gli eventi sono ordinati nel tempo e intercorrelati. Il consumer ha bisogno della serie sequenziata di eventi per analizzare che cosa è accaduto.

### <a name="message"></a>Message

I messaggi sono dati non elaborati generati da un servizio da utilizzare o archiviare altrove. Il messaggio contiene i dati che hanno attivato la pipeline del messaggio. L'autore del messaggio ha un'aspettativa su come il consumer gestisce il messaggio. Esiste un contratto tra le due parti. L'autore, ad esempio, invia un messaggio con i dati non elaborati e prevede che il consumer crei un file da tali dati e al termine invii una risposta.

## <a name="comparison-of-services"></a>Confronto dei servizi

| Service | Scopo | Type | Quando usare le autorizzazioni |
| ------- | ------- | ---- | ----------- |
| Griglia di eventi | Programmazione reattiva | Distribuzione di eventi (separati) | Reazione alle modifiche di stato |
| Hub eventi | Pipeline di Big Data | Streaming di eventi (serie) | Flusso dei dati di telemetria e distribuiti |
| Bus di servizio | Messaggistica aziendale di alto valore | Message | Elaborazione di ordini e transazioni finanziarie |

### <a name="event-grid"></a>Griglia di eventi

Griglia di eventi è un backplane eventi che abilita la programmazione reattiva basata su eventi. Usa un modello di pubblicazione-sottoscrizione. Gli autori emettono gli eventi, ma non hanno aspettative sugli eventi che vengono gestiti. I sottoscrittori decidono quali eventi vogliono gestire.

Griglia di eventi è strettamente integrato con i servizi di Azure e può essere integrato con i servizi di terze parti. Semplifica l'utilizzo degli eventi e riduce i costi eliminando la necessità di polling costante. Griglia di eventi instrada in modo efficiente e affidabile gli eventi dalle risorse di Azure e non di Azure. Distribuisce gli eventi agli endpoint dei sottoscrittori registrati. Il messaggio dell'evento contiene le informazioni necessarie per reagire alle modifiche apportate ai servizi e alle applicazioni. Griglia di eventi non è una pipeline di dati e non recapita l'oggetto effettivo aggiornato.

Griglia di eventi supporta l'Inserimento degli eventi che non vengono recapitati a un endpoint nella coda dei messaggi non recapitabili.

Presenta le caratteristiche seguenti:

* scalabile dinamicamente
* basso costo
* senza server
* recapito at-least-once

### <a name="event-hubs"></a>Hub eventi

Hub eventi di Azure è una pipeline di Big Data. Semplifica l'acquisizione, la conservazione e la riproduzione dei dati dei flussi di eventi e di telemetria. I dati possono provenire da molte origini simultanee. Hub eventi consente di rendere disponibili i dati di telemetria e degli eventi per svariate infrastrutture di elaborazione di flussi e servizi di analisi. È disponibile come flusso dei dati o batch di eventi in bundle. Questo servizio offre un'unica soluzione che consente di recuperare rapidamente i dati per l'elaborazione in tempo reale oltre che per la riproduzione ripetuta dei dati non elaborati archiviati. Consente di acquisire i dati di streaming in un file per l'elaborazione e l'analisi.

Presenta le caratteristiche seguenti:

* bassa latenza
* possibilità di ricevere ed elaborare milioni di eventi al secondo
* recapito at-least-once

### <a name="service-bus"></a>Bus di servizio

Il bus di servizio è adatto alle tradizionali applicazioni aziendali. Queste applicazioni aziendali richiedono transazioni, ordinamento, rilevamento duplicati e coerenza immediata. Il bus di servizio consente alle applicazioni [native del cloud](https://azure.microsoft.com/overview/cloudnative/) di offrire una gestione delle transizioni di stato affidabile per i processi aziendali. Quando si gestiscono messaggi di alto valore che non devono andare persi o essere duplicati, usare il bus di servizio di Azure. Il bus di servizio agevola anche la comunicazione a sicurezza elevata tra soluzioni di cloud ibrido e può connettere i sistemi locali esistenti alle soluzioni cloud.

Il bus di servizio è un sistema di messaggistica negoziata. Archivia i messaggi in un "broker", ad esempio una coda, fino a quando il consumer non è pronto a riceverli.

Presenta le caratteristiche seguenti:

* recapito dei messaggi asincrono affidabile (messaggistica aziendale come servizio) che richiede il polling
* funzionalità di messaggistica avanzate, ad esempio FIFO, invio in batch/sessioni, transazioni, messaggi non recapitabili, controllo temporale, routing e filtri e rilevamento duplicati
* recapito at-least-once
* recapito in ordine facoltativo

## <a name="use-the-services-together"></a>Usare i servizi insieme

In alcuni casi, è possibile usare i servizi affiancati per ricoprire ruoli distinti. Un sito di e-commerce ad esempio può usare il bus di servizio per elaborare l'ordine, Hub eventi per acquisire i dati di telemetria del sito e Griglia di eventi per rispondere a eventi come la spedizione di un articolo.

In altri casi, è possibile collegarli per formare una pipeline di eventi e dati. Si usa Griglia di eventi per rispondere agli eventi negli altri servizi. Per un esempio di uso di Griglia di eventi con Hub eventi per eseguire la migrazione dei dati a una data warehouse, vedere [Trasmettere Big Data a una data warehouse](event-grid-event-hubs-integration.md). L'immagine seguente illustra il flusso di lavoro per lo streaming dei dati.

![Panoramica dei dati di flusso](./media/compare-messaging-services/overview.png)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Events, Data Points, and Messages - Choosing the right Azure messaging service for your data](https://azure.microsoft.com/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/) (Eventi, punti dati e messaggi: scelta del servizio di messaggistica di Azure appropriato per i dati)
- [Analogie e differenze tra le code di archiviazione e le code del bus di servizio](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- Per iniziare a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
- Per iniziare a usare Hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md).
- Per iniziare a usare il bus di servizio, vedere [Creare uno spazio dei nomi del bus di servizio usando il portale di Azure](../service-bus-messaging/service-bus-create-namespace-portal.md).
