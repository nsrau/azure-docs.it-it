<properties
	pageTitle="Introduzione all'Hub eventi in C# | Microsoft Azure"
	description="Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure con C# e iniziare a usare EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/12/2016"
	ms.author="sethm"/>

# Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introduzione

Hub eventi è un servizio che consente di elaborare grandi quantità di dati di telemetria sugli eventi da applicazioni e dispositivi connessi. Dopo aver raccolto i dati in Hub eventi, è possibile archiviarli usando un cluster di archiviazione o trasformarli usando un provider di analisi in tempo reale. Questa funzionalità di elaborazione e di raccolta di eventi su vasta scala rappresenta un componente chiave delle moderne architetture di applicazioni, tra cui l’Internet delle cose (IoT).

Questa esercitazione illustra come usare il portale di Azure classico per creare un hub eventi. Illustra inoltre come inserire messaggi in Hub eventi usando un'applicazione console scritta in C# e come recuperarli in parallelo usando la [libreria dell'Host processore di eventi][] in C#.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Microsoft Visual Studio 2013 o versione successiva oppure Microsoft Visual Studio Express per Windows. Gli esempi in questo articolo usano Visual Studio 2015.

+ Un account Azure attivo. <br/>Se non è disponibile un account, è possibile crearne uno gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank").

## Creare un hub eventi

1. Accedere al [portale di Azure classico][] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, **Bus di servizio**, **Hub eventi** e infine **Creazione rapida**.

	![][1]

3. Digitare un nome per l'hub eventi, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub eventi**.

	![][2]

4. Se in una determinata area non è stato selezionato in modo esplicito uno spazio dei nomi esistente il portale ne crea uno, in genere nel formato ***nome hub eventi*-ns**. Fare clic su tale spazio dei nomi, in questo esempio **eventhub-ns**.

	![][3]

5. Fare clic sulla scheda **Hub eventi** nella parte superiore della pagina, quindi selezionare l'hub eventi appena creato.

	![][4]

6. Fare clic sulla scheda **Configura** nella parte superiore, aggiungere una regola denominata **SendRule** con diritti di invio, aggiungere un'altra regola denominata **ReceiveRule** con diritti di *gestione*, *invio* e **ascolto** e quindi fare clic su *Salva*.

	![][5]

7. Fare clic sulla scheda **Dashboard** nella parte superiore della pagina e quindi su **Informazioni di connessione**. Copiare le due stringhe di connessione in un percorso temporaneo, perché verranno usate più avanti in questa esercitazione.

	![][6]

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. In Visual Studio aprire il progetto **Receiver** creato in precedenza.

2. Fare clic con il pulsante destro del mouse sulla soluzione **Receiver**, scegliere **Aggiungi** e quindi fare clic su **Progetto esistente**.
 
3. Trovare il file Sender.csproj esistente e fare doppio clic su di esso per aggiungerlo alla soluzione.
 
4. Fare nuovamente clic con il pulsante destro del mouse sulla soluzione **Receiver** e quindi scegliere **Proprietà**. Viene visualizzata la pagina della proprietà **Receiver**.

5. Fare clic su **Progetto di avvio** e quindi fare clic sul pulsante **Progetti di avvio multipli**. Impostare la casella **Azione** per i progetti **Receiver** e **Sender** su **Avvio**.

	![][19]

6. Fare clic su **Dipendenze progetto**. Nella casella **Progetti** fare clic su **Sender**. Nella casella **Dipendente da** assicurarsi che sia selezionato **Receiver**.

	![][20]

7. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà**.

1.	Premere F5 per eseguire il progetto **Receiver** in Visual Studio e quindi attendere che il progetto avvii i ricevitori per tutte le partizioni.

	![][21]

2.	Il progetto **Sender** verrà eseguito automaticamente. Premere **INVIO** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.

	![][22]

Premere **CTRL+C** nella finestra di dialogo **Sender** per terminare l'applicazione Sender, quindi premere **INVIO** nella finestra di dialogo Receiver per arrestare la relativa applicazione.

## Passaggi successivi

Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

- Un'[applicazione di esempio completa che usa Hub eventi][].
- Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][].
- Una [soluzione di messaggistica accodata][] che usa le code di Bus di servizio.
- [Panoramica di Hub eventi][]

<!-- Images. -->
[1]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[portale di Azure classico]: https://manage.windowsazure.com/
[libreria dell'Host processore di eventi]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[soluzione di messaggistica accodata]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0413_2016-->