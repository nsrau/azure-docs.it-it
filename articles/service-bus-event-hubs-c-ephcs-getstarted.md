<properties pageTitle="Introduzione all'Hub eventi" metaKeywords="Azure Service Bus, Event Hub, getting started Event Hubs" description="Seguire questa esercitazione per iniziare a usare gli hub eventi di Azure inviando gli eventi con C e ricevendoli in C# usando EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="c" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Introduzione all'Hub eventi

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di accettare milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta nell'Hub eventi, i dati possono essere trasformati e archiviati tramite qualsiasi provider di analisi in tempo reale o qualsiasi cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi].

Questa esercitazione illustra come inserire messaggi in Hub eventi usando un'applicazione console in C e come recuperarli in parallelo usando la libreria dell'[Host processore di eventi] in C#.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

+ Ambiente di sviluppo in C. Per questa esercitazione si presuppone l'uso di uno stack gcc in una [VM Linux in Azure](http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-linux-tutorial/) con Ubuntu 14.04. Le istruzioni per gli altri ambienti saranno fornite nei collegamenti esterni.

+ Microsoft Visual Studio Express 2013 per Windows

+ Un account Azure attivo. <br/>Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

## Creare un hub eventi

1. Accedere al [portale di gestione di Azure] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub eventi**, quindi **Creazione rapida**.

   	![][1]

3. Digitare un nome per l'hub eventi selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub eventi**.

   	![][2]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***event hub name*-ns**).

   	![][3]

5. Fare clic sulla scheda **Hub eventi** nella parte superiore della pagina, quindi selezionare l'hub eventi appena creato.

   	![][4]

6. Fare clic sulla scheda **Configura** nella parte superiore della pagina, aggiungere una regola denominata **SendRule** con diritti di *Invio*, aggiungere un'altra regola denominata **ReceiveRule** con diritti di *Gestione, Invio, Ascolto*, quindi fare clic su **Salva**.

   	![][5]

7. Nella stessa pagina prendere nota delle chiavi generate per **SendRule**.

   	![][6b]

8. Fare clic sulla scheda **Dashboard** nella parte superiore della pagina e quindi su **Informazioni di connessione**. Prendere nota delle due stringhe di connessione.

   	![][6]

L'hub eventi è stato creato e sono disponibili le stringhe di connessione necessarie per inviare e ricevere eventi.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Eseguire le applicazioni

A questo punto è possibile eseguire le applicazioni.

1.	Eseguire il progetto **Receiver** da Visual Studio, quindi attendere che il progetto avvii i ricevitori per tutte le partizioni.

   	![][21]

2.	Eseguire il programma **sender**. Gli eventi verranno visualizzati nella finestra del ricevitore.

   	![][24]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/service-bus-event-hubs-getstarted/receive-eph-c.png

<!-- Links -->
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Host processore di eventi]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Panoramica di Hub eventi]: http://msdn.microsoft.com/it-it/library/azure/dn836025.aspx
