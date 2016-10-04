<properties
	pageTitle="Introduzione all'Hub eventi in C# | Microsoft Azure"
	description="Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure, inviare gli eventi in C# e riceverli in Java usando EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="jtaubensee"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/27/2016"
	ms.author="jotaub;sethm"/>

# Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduzione

Hub eventi è un servizio che consente di elaborare grandi quantità di dati di telemetria sugli eventi da applicazioni e dispositivi connessi. Dopo aver raccolto i dati in Hub eventi, è possibile archiviarli usando un cluster di archiviazione o trasformarli usando un provider di analisi in tempo reale. Questa funzionalità di elaborazione e raccolta di eventi su vasta scala rappresenta un componente chiave delle moderne architetture di applicazioni, tra cui Internet delle cose (IoT).

Questa esercitazione illustra come usare il portale di Azure classico per creare un hub eventi. Illustra anche come inserire messaggi in un hub eventi usando un'applicazione console scritta in C# e come recuperarli in parallelo usando la libreria dell'host processore di eventi Java.

Per completare l'esercitazione sono necessari gli elementi seguenti:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un account Azure attivo. <br/>Se non è disponibile un account, è possibile crearne uno gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	Eseguire il progetto **Receiver**.

	![][21]

2.	Eseguire il progetto **Sender**.

	![][22]

## Passaggi successivi

Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

- Un'[applicazione di esempio completa che usa Hub eventi][].
- Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][].
- Una [soluzione di messaggistica accodata][] che usa le code di Bus di servizio.
- [Panoramica di Hub eventi][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[soluzione di messaggistica accodata]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0928_2016-->