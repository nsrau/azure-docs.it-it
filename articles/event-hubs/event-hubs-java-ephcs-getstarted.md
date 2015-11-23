<properties
	pageTitle="Introduzione all'Hub eventi in Java | Microsoft Azure"
	description="Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure, inviare gli eventi in Java e riceverli in C# mediante EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="sethm"/>

# Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduzione

Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][].

Questa esercitazione illustra come inserire messaggi in Hub eventi usando un'applicazione console in Java e come recuperarli in parallelo usando la libreria dell'[Host processore di eventi][] in C#.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Ambiente di sviluppo in Java. Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/).

+ Microsoft Visual Studio Express per Windows

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

## Creare un hub eventi

1. Accedere al [portale di Azure] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, **Bus di servizio**, **Hub eventi** e infine **Creazione rapida**.

	![][1]

3. Digitare un nome per l'hub eventi, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub eventi**.

	![][2]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub eventi*-ns**).

	![][3]

5. Fare clic sulla scheda **Hub eventi** nella parte superiore della pagina, quindi selezionare l'hub eventi appena creato.

	![][4]

6. Fare clic sulla scheda **Configura** nella parte superiore della pagina, aggiungere una regola denominata **SendRule** con diritti *Send*, aggiungere un'altra regola denominata **ReceiveRule** con diritti *Manage, Send, Listen* e quindi fare clic su **Salva**.

	![][5]

7. Nella stessa pagina prendere nota delle chiavi generate per **SendRule**.

	![][6b]

8. Fare clic sulla scheda **Dashboard** nella parte superiore della pagina e quindi su **Informazioni di connessione**. Prendere nota delle due stringhe di connessione.

	![][6]

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	Eseguire il progetto **Receiver** da Visual Studio, quindi attendere che il progetto avvii i ricevitori per tutte le partizioni.

	![][21]

2.	Eseguire il progetto **Sender** e scegliere **Invio** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.

	![][22]

## Passaggi successivi

Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

- Un'[applicazione di esempio completa che usa Hub eventi][].
- Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][].
- Una [soluzione di messaggistica accodata][] che usa le code di Bus di servizio.

Per altre informazioni, vedere il [Centro per sviluppatori di Java](/develop/java/).

<!-- Images. -->
[1]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6.png
[6b]: ./media/event-hubs-java-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[portale di Azure]: https://manage.windowsazure.com/
[Host processore di eventi]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[soluzione di messaggistica accodata]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=Nov15_HO3-->