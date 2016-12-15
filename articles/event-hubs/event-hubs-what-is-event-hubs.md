---
title: "Che cos&quot;è l&quot;hub di eventi di Azure? | Documentazione Microsoft"
description: Panoramica e descrizione di Hub eventi di Azure
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 4391d750-5bbe-456d-9091-b416127bc754
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 05ca343cfdfc602759eb3ea30a7186a0bb47bd74
ms.openlocfilehash: eddc3e7c4914936a8a83a042dc0f7d528b91f059


---
# <a name="what-is-azure-event-hubs"></a>Che cos'è l'hub di eventi di Azure?
Hub di eventi di Azure è un servizio di ingresso dati altamente scalabile che può inserire milioni di eventi al secondo in modo che è possibile elaborare e analizzare enormi quantità di dati generati per i dispositivi connessi e le applicazioni. Hub eventi funge da "porta principale" per una pipeline di eventi. Dopo aver raccolto i dati in un hub eventi, è possibile trasformarli e archiviarli tramite qualsiasi provider di analisi in tempo reale o adattatore di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione. Per altre informazioni e dettagli tecnici, vedere [Panoramica di Hub eventi](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Caratteristiche degli hub di eventi
Hub eventi di Azure è un servizio di elaborazione eventi che consente l'elaborazione di eventi e telemetria su grandissima scala, con bassa latenza e affidabilità elevata. Questo servizio è particolarmente utile per:

* Strumentazione dell'applicazione
* Esperienza dell'utente o l'elaborazione del flusso di lavoro
* Scenari Internet of Things (IoT)

Tra le altre funzionalità chiave di Hub eventi ci sono il rilevamento del comportamento nelle app per dispositivi mobili, il traffico di informazioni da Web farm, l'acquisizione di eventi nei giochi per console e la raccolta di dati di telemetria da computer industriali o veicoli connessi.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate su Hub eventi, vedere gli argomenti seguenti.

* [Panoramica di Hub eventi](event-hubs-overview.md)
* [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md)
* [Domande frequenti sulla disponibilità e il supporto di Hub eventi](event-hubs-availability-and-support-faq.md)
* Iniziare con un'[esercitazione di Hub eventi][Event Hubs tutorial]
* Un'[applicazione di esempio completa che usa Hub eventi][sample application that uses Event Hubs]

[Event Hubs tutorial]: event-hubs-csharp-ephcs-getstarted.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Dec16_HO1-->


