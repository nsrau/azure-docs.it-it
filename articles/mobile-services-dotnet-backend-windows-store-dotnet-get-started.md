<properties pageTitle="Introduzione a servizi mobili per applicazioni Windows Store | Mobile Developer Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/18/2014" ms.author="glenga" />


# <a name="getting-started"> </a>Introduzione a Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app di Windows universale mediante Servizi mobili di Azure. Le soluzioni per app di Windows universali includono progetti di app di Windows Store 8.1 e Windows Phone Store 8.1 e un progetto condiviso comune. Per altre informazioni, vedere [Creare app di Windows universali per Windows e Windows Phone](http://msdn.microsoft.com/it-it/library/windows/apps/xaml/dn609832.aspx).

In questa esercitazione si creeranno sia un nuovo servizio mobile che una semplice app *To do list* che archivia i dati dell'app nel nuovo servizio mobile. Il servizio mobile che verrà creato usa i linguaggi .NET supportati tramite Visual Studio per la logica di business sul lato server e per la gestione del servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la versione per back-end JavaScript di questo argomento.

>[WACOM.NOTE]Questo argomento illustra come creare un nuovo progetto di servizio mobile e un'app di Windows universale usando il portale di gestione di Azure. Visual Studio 2013 Update 3 consente di aggiungere un nuovo progetto di servizio mobile a una soluzione Visual Studio esistente. Per altre informazioni, vedere [Guida introduttiva: aggiunta di un servizio mobile (back-end .NET)](http://msdn.microsoft.com/it-it/library/windows/apps/dn629482.aspx).

>Per aggiungere un servizio mobile a un progetto app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, vedere [Introduzione ai dati per Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. È disponibile una versione di valutazione gratuita.

## Creare un nuovo servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creare una nuova app di Windows universale

Dopo aver creato il servizio mobile, è possibile seguire una facile guida introduttiva nel portale di gestione per creare una nuova app o modificare un'app esistente per connettersi al servizio mobile. 

In questa sezione si creerà una nuova app di Windows universale connessa al servizio mobile.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.
   
2. Nella scheda Avvio rapido fare clic su **Windows** in **Scegli una piattaforma** ed espandere **Crea una nuova app di Windows Store**.

   	![][6]

   	Di seguito sono visualizzati i tre semplici passaggi per creare un'app di Windows Store connessa al servizio mobile.

  	![][7]

3. Se necessario, scaricare e installare <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> nel computer locale o nella macchina virtuale.

4. In **Scarica ed esegui l'app e il servizio localmente** selezionare un linguaggio per l'app di Windows Store, quindi fare clic su **Download**. 

  	Verrà scaricata una soluzione che contiene progetti sia per il servizio mobile, sia per l'applicazione _To do list_ di esempio connessa al servizio mobile. Salvare il file del progetto compresso nel computer locale e prendere nota del percorso.

## Testare l'app nel servizio mobile locale

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[WACOM.NOTE]È possibile esaminare il codice che accede al servizio mobile per eseguire una query e inserire i dati, disponibile nel file MainPage.xaml.cs.


## Pubblicare il servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>Nel progetto di codice condiviso aprire il file App.xaml.cs, individuare il codice che crea un'istanza di <a href="http://msdn.microsoft.com/it-it/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, impostare come commento il codice per la creazione del client usando <em>localhost</em>, quindi rimuovere il commento dal codice per la creazione del client usando l'URL del servizio mobile, simile al seguente:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>Il client accederà al servizio mobile pubblicato in Azure.</p></li>
</ol>

## Testare l'app nel servizio mobile ospitato in Azure

Una volta pubblicato il servizio mobile e aver connesso il client al servizio mobile remoto ospitato in Azure, è possibile eseguire l'app usando Azure per l'archiviazione dell'elemento.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]


## Passaggi successivi
Dopo aver completato la guida introduttiva, è possibile eseguire altre importanti attività in Servizi mobili: 

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione alla sincronizzazione dei dati offline]
  <br/>Informazioni su come usare la sincronizzazione dei dati offline per rendere l'app più affidabile e veloce nelle risposte.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Risolvere i problemi relativi a un back-end .NET di Servizi mobili]
  <br/> Informazioni su come diagnosticare e correggere i problemi che possono verificarsi con un back-end .NET di Servizi mobili. 

Per altre informazioni sulle app di Windows universali, vedere [Supporto di piattaforme per più dispositivi da un singolo servizio mobile](/it-it/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Introduzione a Servizi mobili]:#getting-started
[Creare un nuovo servizio mobile]:#create-new-service
[Definire l'istanza del servizio mobile]:#define-mobile-service-instance
[Passaggi successivi]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-startup.png



<!-- URLs. -->
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Introduzione alla sincronizzazione dei dati offline]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: mobile-services-win8-javascript/
[Portale di gestione]: https://manage.windowsazure.com/
[Versione di back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-get-started
[Introduzione ai dati in Servizi mobili tramite Visual Studio 2012]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Risolvere i problemi relativi a un back-end .NET di Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
