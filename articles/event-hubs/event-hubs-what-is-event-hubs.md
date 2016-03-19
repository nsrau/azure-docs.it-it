<properties
	pageTitle="Che cos'è Hub eventi di Azure? | Microsoft Azure"
	description="Panoramica di Hub eventi di Azure"
	services="event-hubs"
	documentationCenter=".net"
	authors="nberdy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016"
	ms.author="sethm"/>

# Che cos'è l'hub di eventi di Azure?

Hub di eventi di Azure è un servizio di ingresso dati altamente scalabile che può inserire milioni di eventi al secondo in modo che è possibile elaborare e analizzare enormi quantità di dati generati per i dispositivi connessi e le applicazioni. Gli hub di eventi fungono da "porta principale" per una pipeline di eventi e una volta che i dati vengono raccolti in un hub di eventi, possono essere trasformati e archiviati con qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione.

## Caratteristiche degli hub di eventi

Hub eventi di Azure è un servizio di elaborazione eventi che fornisce un punto di ingresso di eventi e telemetria nel cloud su grandissima scala, con bassa latenza e affidabilità elevata. Questo servizio è particolarmente utile per:

* Strumentazione dell'applicazione
* Esperienza dell'utente o l'elaborazione del flusso di lavoro
* Scenari Internet of Things (IoT)

Esempi di tali scenari sono il rilevamento del comportamento in app per dispositivi mobili, informazioni sul traffico da web farm, l’acquisizione di eventi in gioco nei giochi di console o i dati di telemetria raccolti da computer industriali o veicoli connessi.

A differenza di[le code del Bus di servizio e gli argomenti](../service-bus/service-bus-messaging-overview.md)gli hub di eventi è incentrato sulla fornitura di messaggistica la gestione del flusso a livello di scalabilità. Pertanto, le funzionalità di Hub eventi di Azure differiscono dagli argomenti di Bus di servizio in quando sono fortemente orientati agli scenari con velocità effettiva elevata ed elaborazione di eventi. Di conseguenza, gli Hub eventi non implementano alcune delle funzionalità di messaggistica disponibili per gli [argomenti](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics). Se tali funzionalità sono necessarie, gli argomenti rimangano la scelta ottimale.

## Passaggi successivi

Per informazioni dettagliate sugli hub di eventi, vedere gli argomenti seguenti.

- [Panoramica di Hub eventi](event-hubs-overview.md)
- [Guida alla programmazione di Hub eventi](event-hubs-programming-guide.md)
- [Domande frequenti sulla disponibilità e il supporto di Hub eventi](event-hubs-availability-and-support-faq.md)
- Iniziare con un’[esercitazione di Hub eventi][].
- Un'[applicazione di esempio completa che usa Hub eventi][]

[esercitazione di Hub eventi]: event-hubs-csharp-ephcs-getstarted.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0218_2016-->