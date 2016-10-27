<properties
    pageTitle="Introduzione all'Hub eventi in C# | Microsoft Azure"
    description="Eseguire questa esercitazione per iniziare a usare gli hub eventi di Azure con C# e iniziare a usare EventProcessorHost."
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
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>


# <a name="get-started-with-event-hubs"></a>Introduzione all'Hub eventi

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduzione

Hub eventi è un servizio che consente di elaborare grandi quantità di dati di telemetria sugli eventi da applicazioni e dispositivi connessi. Dopo aver raccolto i dati in Hub eventi, è possibile archiviarli usando un cluster di archiviazione o trasformarli usando un provider di analisi in tempo reale. Questa funzionalità di elaborazione e raccolta di eventi su vasta scala rappresenta un componente chiave delle moderne architetture di applicazioni, tra cui Internet delle cose (IoT).

Questa esercitazione illustra come usare il portale di Azure classico per creare un hub eventi. Illustra inoltre come inserire messaggi in Hub eventi usando un'applicazione console scritta in C# e come recuperarli in parallelo usando la [libreria dell'Host processore di eventi][] in C#.

Per completare l'esercitazione sono necessari gli elementi seguenti:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Un account Azure attivo. Se non si ha un account, è possibile crearne uno gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1. In Visual Studio aprire il progetto **Receiver** creato in precedenza.

2. Fare clic con il pulsante destro del mouse sulla soluzione **Receiver**, scegliere **Aggiungi** e quindi fare clic su **Progetto esistente**.
 
3. Trovare il file Sender.csproj esistente e fare doppio clic su di esso per aggiungerlo alla soluzione.
 
4. Fare nuovamente clic con il pulsante destro del mouse sulla soluzione **Receiver** e quindi scegliere **Proprietà**. Viene visualizzata la pagina della proprietà **Receiver** .

5. Fare clic su **Progetto di avvio** e quindi fare clic sul pulsante **Progetti di avvio multipli**. Impostare la casella **Azione** per i progetti **Receiver** e **Sender** su **Avvio**.

    ![][19]

6. Fare clic su **Dipendenze progetto**. Nella casella **Progetti** fare clic su **Sender**. Nella casella **Dipendente da** verificare che sia selezionato **Receiver**.

    ![][20]

7. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà**.

1.  Premere F5 per eseguire il progetto **Receiver** in Visual Studio e quindi attendere che il progetto avvii i ricevitori per tutte le partizioni.

    ![][21]

2.  Il progetto **Sender** verrà eseguito automaticamente. Premere **INVIO** nella finestra della console. Gli eventi verranno visualizzati nella finestra del ricevitore.

    ![][22]

Premere **CTRL+C** nella finestra di dialogo **Sender** per terminare l'applicazione Sender, quindi premere **INVIO** nella finestra di dialogo Receiver per arrestare la relativa applicazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata creata un'applicazione che crea un Hub eventi e invia e riceve i dati, è possibile passare ai seguenti scenari:

- Un' [applicazione di esempio completa che usa Hub eventi][].
- Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][] .
- [Panoramica di Hub eventi][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[portale di Azure classico]: https://manage.windowsazure.com/
[Host processore di eventi]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[soluzione di messaggistica accodata]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 



<!--HONumber=Oct16_HO2-->


