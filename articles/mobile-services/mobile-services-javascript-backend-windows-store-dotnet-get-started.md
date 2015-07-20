<properties
	pageTitle="Introduzione a servizi mobili per applicazioni Windows Store | Mobile Dev Center"
	description="Seguire questa esercitazione per iniziare a usare Servizi mobili di Azure per lo sviluppo per Windows Store in C# o JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="glenga"/>

# <a name="getting-started"> </a>Introduzione a Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app universale di Windows mediante Servizi mobili di Azure. Le soluzioni per app universali di Windows includono progetti di app per Windows Store 8.1 e Windows Phone Store 8.1 e un progetto condiviso comune. Per altre informazioni, vedere [Creare app universali di Windows per Windows e Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Per il servizio mobile creato verrà usato JavaScript per la logica di business sul lato server. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server nei linguaggi .NET supportati tramite Visual Studio, vedere la versione per back-end .NET di questo argomento.

>[AZURE.NOTE]Questo argomento descrive come creare un nuovo progetto di servizio mobile e un'app universale di Windows usando il portale di gestione di Azure. Visual Studio 2013 permette di aggiungere un nuovo progetto di servizio mobile a una soluzione Visual Studio esistente. Per altre informazioni, vedere [Aggiungere Servizi mobili a un'app esistente](mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md).

>Per aggiungere un servizio mobile a un progetto app di Windows Phone 8.0 o Windows Phone Store 8.1, vedere [Aggiungere Servizi mobili a un'app esistente per Windows Phone](mobile-services-windows-phone-get-started-data.md).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express per Windows]

## Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Creare una nuova app universale di Windows

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app universale di Windows o per modificare un progetto app esistente per Windows Store o Windows Phone per connettersi al servizio mobile.

In questa sezione si creerà una nuova app universale di Windows connessa al servizio mobile.

1.  Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.


2. Nella scheda Avvio rapido fare clic su **Windows** in **Scegli piattaforma** ed espandere **Crea una nuova app di Windows Store**.

   	Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Windows Store connessa al servizio mobile.

  	![Passaggi della guida introduttiva a Servizi mobili](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Se necessario, scaricare e installare [Visual Studio 2013 Express per Windows] nel computer locale o nella macchina virtuale.

4. Fare clic su **Crea tabella TodoItem** per creare una tabella in cui archiviare i dati dell'app.

5. In **Scaricare ed eseguire l'applicazione** selezionare un linguaggio per l'app, quindi fare clic su **Download**.

  	Verrà scaricato il progetto per l'applicazione *To do list* di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Esecuzione dell'app per Windows

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file MainPage.xaml.cs.

## Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili:

* [Aggiungere Servizi mobili a un'app esistente][Get started with data] <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione alla sincronizzazione dei dati offline] <br/>Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Aggiungere l'autenticazione all'app di Servizi mobili][Get started with authentication] <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Aggiungere notifiche push all'app][Get started with push notifications] <br/>Informazioni sull'invio di una notifica push di base all'app.

Per altre informazioni sulle app Windows universali, vedere [Supporto di piattaforme per più dispositivi da un singolo servizio mobile](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[Get started with data]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md
[Get started with data]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Introduzione alla sincronizzazione dei dati offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Get started with authentication]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Visual Studio 2013 Express per Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Management Portal]: https://manage.windowsazure.com/
[Get started with data in Mobile Services using Visual Studio 2012]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md
 

<!---HONumber=July15_HO2-->