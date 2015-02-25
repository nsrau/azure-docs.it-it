<properties pageTitle="Introduzione ai dati (iOS) | Mobile Dev Center" description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per iOS." services="mobile-services" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Questo argomento descrive come usare Servizi mobili di Azure per sfruttare i dati in un'app per iOS. In questa esercitazione si scaricherà un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile creato in questa esercitazione supporta il runtime .NET. Sarà quindi possibile usare linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la [versione per back-end JavaScript] di questo argomento.

>[AZURE.NOTE] In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app per iOS e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se questa è la prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].
</div>

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Download del progetto dell'app per iOS]
2. [Creazione del servizio mobile]
3. [Scaricare il servizio in locale]
4. [Testare il servizio mobile]
5. [Pubblicare il servizio mobile in Azure]
7. [Aggiornamento dell'app per l'uso di Servizi mobili]
8. [Testare l'app in Servizi mobili]

Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ [Mobile Services SDK per iOS] e [XCode 4.5][Installare Xcode] e iOS 5.0 o versioni successive.
+ Visual Studio 2013 ([Visual Studio Express per il Web](http://go.microsoft.com/p/?linkid=9832232) è disponibile gratuitamente).
+ Un account Microsoft Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'[app GetStartedWithData][GitHub], che è un'app per iOS. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili per iOS, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1. Scaricare l'[app di esempio][GitHub] GetStartedWithData.

2. In Xcode aprire il progetto scaricato ed esaminare il file TodoService.m.

   	Si noti che sono presenti otto commenti **// TODO** che specificano le procedure da eseguire per il funzionamento dell'app con il servizio mobile.

3. Scegliere il pulsante **Run** (o premere il tasto Comando+R) per ricompilare il progetto e avviare l'app.

4. Nell'app digitare un testo nella casella e quindi fare clic sul pulsante **+**.

   	![][0]  

   	Si noti che il testo salvato è visualizzato nell'elenco riportato di seguito.

##<a name="create-service"></a>Creare un nuovo servizio mobile nel portale di gestione

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Scaricare il servizio nel computer locale

[AZURE.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>Testare il servizio mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Pubblicare il servizio mobile in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Testare l'app nel nuovo servizio mobile

1. In Xcode selezionare un emulatore in cui effettuare la distribuzione (iPhone o iPad), scegliere il pulsante **Run** (o premere il tasto Comando+R) per ricompilare il progetto e avviare l'app.

   	Viene eseguito il client Servizi mobili di Azure, compilato con l'SDK per iOS, e viene eseguita una query sugli elementi dal servizio mobile.

2. Come in precedenza, digitare un testo nella casella e quindi fare clic sul pulsante **+**.

   	Un nuovo elemento verrà inviato come inserimento al servizio mobile. Ogni nuovo elemento TodoItem viene archiviato e aggiornato nel database SQL precedentemente configurato per il servizio mobile nel portale di gestione di Azure.

3. Arrestare e riavviare l'app per verificare che le modifiche siano state salvate in modo permanente nel database in Azure.

	È inoltre possibile esaminare il database usando il portale di gestione di Azure oppure il comando Esplora oggetti di SQL Server in Visual Studio. I due passaggi successivi illustrano come usare il [portale di gestione di Azure] per visualizzare le modifiche nel database.

4. Nel portale di gestione di Azure fare clic su Gestisci per il database associato al servizio mobile.

    ![][17]

5. Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app. La query da usare sarà simile alla seguente, ma anziché `todolist` conterrà il nome del database.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

L'esercitazione **Introduzione ai dati** è terminata.

##<a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app per iOS di usare dati in Servizi mobili.

In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Usare script per la convalida e la modifica di dati]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire le esercitazioni per iOS seguenti:

* [Introduzione all'autenticazione]
	<br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push]
  <br/>Informazioni sull'invio di una notifica push di base all'app con Servizi mobili.

<!-- Anchors. -->
[Download del progetto dell'app per iOS]: #download-app
[Creazione del servizio mobile]: #create-service
[Aggiunta di una tabella dati per l'archiviazione]: #add-table
[Aggiornamento dell'app per l'uso di Servizi mobili]: #update-app
[Testare l'app in Servizi mobili]: #test-app
[Passaggi successivi]:#next-steps
[Scaricare il servizio in locale]: #download-the-service-locally
[Testare il servizio mobile]: #test-the-service
[Pubblicare il servizio mobile in Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-ios
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
[Versione per back-end JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-data-ios

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Archivio GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784


<!--HONumber=42-->
