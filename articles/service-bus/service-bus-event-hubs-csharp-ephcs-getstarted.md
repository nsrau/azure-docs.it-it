<properties
	pageTitle="Introduzione all'Hub eventi"
	description="Seguire questa esercitazione per iniziare a usare gli hub eventi di Azure con C# usando EventProcessorHost"
	services="service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="csharp"
	ms.devlang="csharp"
	ms.topic="hero-article"
	ms.date="04/13/2015"
	ms.author="sethm"/>

# Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduzione

Hub eventi è un servizio che consente di elaborare grandi quantità di dati eventi da applicazioni e dispositivi connessi. Dopo aver raccolto i dati in Hub eventi, è possibile archiviarli usando un cluster di archiviazione o trasformarli usando un provider di analisi in tempo reale. Questa funzionalità di elaborazione e di raccolta di eventi su vasta scala rappresenta un componente chiave delle moderne architetture di applicazioni, tra cui Internet delle cose.

Questa esercitazione illustra come usare il portale di gestione di Azure per creare un Hub di eventi. Illustra inoltre come inserire messaggi in Hub eventi usando un'applicazione console in C# e come recuperarli in parallelo usando la libreria dell'[Host processore di eventi] in C#.

Per completare questa esercitazione, sono necessari i seguenti elementi:

+ Microsoft Visual Studio 2013 o Microsoft Visual Studio Express 2013 per Windows.

+ Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

## Creare un hub eventi

1. Accedere al [portale di gestione di Azure] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, quindi **Hub eventi** e infine **Creazione rapida**.

   	![][1]

3. Digitare un nome per l'hub eventi, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub eventi**.

   	![][2]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub eventi*-ns**).

   	![][3]

5. Fare clic sulla scheda **Hub eventi** nella parte superiore della pagina, quindi selezionare l'hub eventi appena creato.

   	![][4]

6. Fare clic sulla scheda **Configura** nella parte superiore, aggiungere una regola denominata **SendRule** con diritti *Send*, aggiungere un'altra regol denominata **ReceiveRule** con diritti *Manage, Send, Listen* e quindi fare clic su **Salva**.

   	![][5]

7. Fare clic sulla scheda **Dashboard** nella parte superiore della pagina e quindi su **Informazioni di connessione**. Prendere nota di due stringhe di connessione o copiarle in un punto per usarle in un secondo momento in questa esercitazione.

   	![][6]

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	In Visual Studio eseguire il progetto **Receiver**, quindi attendere che il progetto avvii i ricevitori per tutte le partizioni.

   	![][21]

2.	Eseguire il progetto **Sender** e premere **INVIO** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.

   	![][22]

## Passaggi successivi

Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

- Un'[applicazione di esempio completa che usa Hub eventi].
- L'esempio per la [scalabilità orizzontale di eventi elaborati con Hub eventi].
- Una [soluzione di messaggistica accodata] usando le code di Bus di servizio.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Host processore di eventi]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: http://msdn.microsoft.com/library/azure/dn836025.aspx
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[scalabilità orizzontale di eventi elaborati con Hub eventi]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[soluzione di messaggistica accodata]: ../cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md

<!--HONumber=52--> 