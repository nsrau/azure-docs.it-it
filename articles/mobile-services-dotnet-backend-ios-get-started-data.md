<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introduzione ai dati in Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/it-it/develop/mobile/tutorials/get-started-with-data-ios/"  title="JavaScript backend">Back-end JavaScript</a></div>

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app per iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile creato in questa esercitazione supporta il runtime .NET. Sarà quindi possibile usare linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript][versione per back-end JavaScript] di questo argomento.

<div class="dev-callout"><b>Nota</b>
<p>Per completare questa esercitazione, &egrave; necessario disporre di Visual Studio 2013.</p>
</div>

<div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione viene descritto come &egrave; possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per iOS e vengono pertanto riproposte molte delle procedure gi&agrave; completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, &egrave; consigliabile iniziare dall'esercitazione <a href="/it-it/develop/mobile/tutorials/get-started-ios">Introduzione a Servizi mobili</a>.</p>
</div>

In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Download del progetto dell'app per iOS][Download del progetto dell'app per iOS]
2. [Creazione del servizio mobile][Creazione del servizio mobile]
3. [Scaricare il servizio mobile in locale][Scaricare il servizio mobile in locale]
4. [Testare il servizio mobile][Testare il servizio mobile]
5. [Pubblicare il servizio mobile in Azure][Pubblicare il servizio mobile in Azure]
6. [Aggiornamento dell'app per l'utilizzo di Servizi mobili][Aggiornamento dell'app per l'utilizzo di Servizi mobili]
7. [Test dell'app in Servizi mobili][Test dell'app in Servizi mobili]

Per completare questa esercitazione, è necessario disporre di:

+ [Mobile Services SDK per iOS][Mobile Services SDK per iOS], di [XCode 4.5][XCode 4.5] e di iOS 5.0 o versioni successive.
+ Visual Studio 2013 ([Visual Studio Express per il Web][Visual Studio Express per il Web] è disponibile gratuitamente).
+ Un account di Microsoft Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

## <a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][app GetStartedWithData] che è un'app per iOS. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1. Scaricare l'[app di esempio][app GetStartedWithData] GetStartedWithData.

2. In Xcode aprire il progetto scaricato ed esaminare il file TodoService.m.

   	Si noti che sono presenti otto commenti **// TODO** in cui vengono specificate le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3. Premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

   	![][0]

   	Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

## <a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>Scaricare il servizio nel computer locale

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a>Testare il servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-mobile-service"></a>Pubblicare il servizio mobile in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Testare l'app nel nuovo servizio mobile

1. In Xcode selezionare un emulatore in cui effettuare la distribuzione (iPhone o iPad), premere il pulsante **Run** (o il tasto Comando+R) per ricompilare il progetto e avviare l'app.

   	Verrà eseguito il client di Servizi mobili di Azure, compilato con l'SDK per iOS, e verrà eseguita una query sugli elementi dal servizio mobile.

2. Come in precedenza, digitare un testo nella casella di testo e quindi fare clic sul pulsante **+**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile. Ogni nuovo elemento TodoItem viene archiviato e aggiornato nel database SQL precedentemente configurato per il servizio mobile nel portale di gestione di Azure.

3. Arrestare e riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure.

    È inoltre possibile esaminare il database usando il portale di gestione di Azure oppure il comando Esplora oggetti di SQL Server in Visual Studio. I due passaggi successivi illustrano come usare il [portale di gestione di Azure][portale di gestione di Azure] per visualizzare le modifiche nel database.

4. Nel portale di gestione di Azure fare clic su Manage per il database associato al servizio mobile.

    ![][1]

5. Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app. La query da usare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di usare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Utilizzo di script per la convalida e la modifica di dati][Utilizzo di script per la convalida e la modifica di dati] 
  <br/>Altre informazioni sull'utilizzo di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Utilizzo del paging per ridefinire le query][Utilizzo del paging per ridefinire le query]
  <br/>Altre informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per iOS seguenti:

* [Introduzione all'autenticazione][Introduzione all'autenticazione]
	<br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push][Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.


<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Windows Store C#]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Windows Store C#"
[Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Windows Store JavaScript"
[Windows Phone]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
[iOS]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
[Android]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
[Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ ".NET backend"
[Back-end JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-ios/ "JavaScript backend"
[versione per back-end JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Download del progetto dell'app per iOS]: #download-app
[Creazione del servizio mobile]: #create-service
[Scaricare il servizio mobile in locale]: #download-the-service-locally
[Testare il servizio mobile]: #test-the-service
[Pubblicare il servizio mobile in Azure]: #publish-mobile-service
[Aggiornamento dell'app per l'utilizzo di Servizi mobili]: #update-app
[Test dell'app in Servizi mobili]: #test-app
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Express per il Web]: http://go.microsoft.com/p/?linkid=9832232
[versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
[app GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
[mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
[mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
[mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
[mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
[Utilizzo di script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Utilizzo del paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
