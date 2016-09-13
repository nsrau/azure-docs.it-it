<properties
	pageTitle="Che cos'è Hub eventi di Azure? | Microsoft Azure"
	description="Panoramica e descrizione di Hub eventi di Azure"
	services="event-hubs"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/17/2016"
	ms.author="sethm"/>

# Che cos'è l'hub di eventi di Azure?

Hub di eventi di Azure è un servizio di ingresso dati altamente scalabile che può inserire milioni di eventi al secondo in modo che è possibile elaborare e analizzare enormi quantità di dati generati per i dispositivi connessi e le applicazioni. Hub eventi funge da "porta principale" per una pipeline di eventi. Dopo aver raccolto i dati in un hub eventi, è possibile trasformarli e archiviarli tramite qualsiasi provider di analisi in tempo reale o adattatore di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione. Per altre informazioni e dettagli tecnici, vedere [Panoramica di Hub eventi](event-hubs-overview.md).

## Caratteristiche degli hub di eventi

Hub eventi di Azure è un servizio di elaborazione eventi che consente l'elaborazione di eventi e telemetria su grandissima scala, con bassa latenza e affidabilità elevata. Questo servizio è particolarmente utile per:

- Strumentazione dell'applicazione
- Esperienza dell'utente o l'elaborazione del flusso di lavoro
- Scenari Internet of Things (IoT)

Tra le altre funzionalità chiave di Hub eventi ci sono il rilevamento del comportamento nelle app per dispositivi mobili, il traffico di informazioni da Web farm, l'acquisizione di eventi nei giochi per console e la raccolta di dati di telemetria da computer industriali o veicoli connessi.

## Passaggi successivi

Per informazioni dettagliate su Hub eventi, vedere gli argomenti seguenti.

- [Panoramica di Hub eventi](event-hubs-overview.md)
- [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md)
- [Domande frequenti sulla disponibilità e il supporto di Hub eventi](event-hubs-availability-and-support-faq.md)
- Iniziare con un’[esercitazione di Hub eventi][].
- Un'[applicazione di esempio completa che usa Hub eventi][]

[esercitazione di Hub eventi]: event-hubs-csharp-ephcs-getstarted.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0907_2016-->