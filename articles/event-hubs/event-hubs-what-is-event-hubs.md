---
title: Informazioni su Hub eventi di Azure e relativi vantaggi | Documentazione Microsoft
description: Panoramica e introduzione all'Hub eventi di Azure - Inserimento di telemetria su scala cloud da siti Web, app e dispositivi
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: sethm; babanisa
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1a6bf0a0352e6d9e3a22586ac825558d12e1307a
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="what-is-event-hubs"></a>Che cos'è Hub eventi?

Hub eventi di Azure è una piattaforma di streaming di dati estremamente scalabile e un servizio di inserimento degli eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Con la possibilità di fornire [funzionalità di pubblicazione-sottoscrizione](https://msdn.microsoft.com/library/aa560414.aspx) con una latenza bassa e su larga scala, Hub eventi funge da "rampa di ingresso" per i Big Data.

## <a name="why-use-event-hubs"></a>Vantaggi dell'uso di Hub eventi

Gli eventi e le funzionalità di gestione della telemetria di Hub eventi sono particolarmente utili per:

* Strumentazione dell'applicazione
* Esperienza dell'utente o l'elaborazione del flusso di lavoro
* Scenari Internet of Things (IoT)

Hub eventi consente, ad esempio, il rilevamento del comportamento nelle app per dispositivi mobili, il traffico di informazioni da Web farm, l'acquisizione di eventi nei giochi per console e la raccolta di dati di telemetria da computer industriali, veicoli connessi o altri dispositivi.

## <a name="azure-event-hubs-overview"></a>Panoramica di Hub eventi di Azure

Il ruolo comune svolto da Hub eventi nelle architetture delle soluzioni è la sua funzione di "porta principale" per una pipeline di eventi, spesso denominata *ingestor evento*. Un ingestor evento è un componente o servizio che si trova tra gli autori e i consumer di eventi per separare la produzione di un flusso di eventi dal consumo di tali eventi. Questa architettura è illustrata nella figura seguente:

![Hub eventi](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Hub eventi offre funzionalità di gestione del flusso di messaggi, ma presenta caratteristiche diverse da quelle dei servizi di messaggistica aziendale tradizionale. Le funzionalità di Hub eventi sono basate su scenari con velocità effettiva elevata ed elaborazione di eventi. Di conseguenza, Hub eventi è diverso dalla messaggistica del [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/) e non implementa alcune delle funzionalità disponibili per le entità di [messaggistica del bus di servizio](/azure/service-bus-messaging/), ad esempio gli argomenti.

## <a name="event-hubs-features"></a>Funzionalità di Hub eventi

Hub eventi contiene gli elementi chiave seguenti:

- [**Producer/autori di eventi**](event-hubs-features.md#event-publishers): qualsiasi entità che invia dati a un hub eventi. Un evento viene pubblicato tramite AMQP 1.0 o HTTPS.
- [**Acquisizione**](event-hubs-features.md#capture): consente di acquisire i dati di streaming di Hub eventi e archiviarli in un account di archiviazione BLOB di Azure.
- [**Partizioni**](event-hubs-features.md#partitions): consentono a ogni consumer di leggere solo uno specifico subset, o partizione, del flusso di eventi.
- [**Token di firma di accesso condiviso**](event-hubs-features.md#sas-tokens): consentono di identificare e autenticare l'autore di eventi.
- [**Consumer eventi**](event-hubs-features.md#event-consumers): qualsiasi entità che legge i dati dell'evento da un hub eventi. I consumer eventi si connettono tramite AMQP 1.0. 
- [**Gruppi di consumer**](event-hubs-features.md#consumer-groups): offrono a ogni singola applicazione una visualizzazione separata del flusso di eventi, consentendo a tali consumer di operare in modo indipendente.
- [**Unità elaborate**](event-hubs-features.md#capacity): unità di capacità pre-acquistate. Una singola partizione ha una scalabilità massima di 1 unità elaborata.

Per informazioni tecniche dettagliate su queste e altre funzionalità di Hub eventi, vedere la [panoramica sulle funzionalità di Hub eventi](event-hubs-features.md). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sui prezzi di Hub eventi, vedere [Hub eventi Prezzi](https://azure.microsoft.com/pricing/details/event-hubs/).

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

* Iniziare con un'[esercitazione di Hub eventi](event-hubs-dotnet-standard-getstarted-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 


