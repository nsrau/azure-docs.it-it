<properties 
	pageTitle="Introduzione ai dati (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="wesmc"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Questo argomento descrive come usare Servizi mobili di Azure come origine dati di back-end per un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile che verrà creato in questa esercitazione è un servizio mobile back-end di .NET. Il Back-end .NET consente di usare i linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile; inoltre è possibile eseguire il servizio mobile ed effettuarne il debugging sul computer locale in uso. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la versione per back-end JavaScript di questo argomento.

>[AZURE.NOTE] Questo argomento descrive come usare gli strumenti in Visual Studio Professional 2013 con aggiornamento 3 per connettere un nuovo servizio mobile a un'app di Windows universale. È possibile eseguire la stessa procedura per connettere un servizio mobile a un'app di Windows Store o Windows Phone Store 8.1. Per connettere un servizio mobile a un'app per Windows Phone 8.0 o Windows Phone Silverlight 8.1, vedere [Introduzione ai dati per Windows Phone](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

> Se non è possibile eseguire l'aggiornamento a Visual Studio Professional 2013 aggiornamento 3 o si preferisce aggiungere manualmente il progetto di servizio mobile a una soluzione app di Windows Store, vedere [questa versione](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data) dell'argomento.

Questa esercitazione spiega come eseguire le operazioni di base seguenti:

1. [Scaricare il progetto dell'app di Windows Store]
2. [Creare un nuovo servizio mobile da Visual Studio]
3. [Testare il progetto di servizio mobile localmente]
4. [Aggiornare l'app per l'uso del servizio mobile]
5. [Pubblicare il servizio mobile in Azure]
6. [Testare l'app nel servizio ospitato in Azure]
7. [Visualizzare i dati archiviati nel database SQL]

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (aggiornamento 3 o versione successiva). <br/>È disponibile una versione di valutazione gratuita. 

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Creare un nuovo servizio mobile da Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>In Esplora soluzioni, aprire il file di codice App.xaml.cs nella cartella di progetto GetStartedWithData.Shared e notare il nuovo campo statico aggiunto alla classe <strong>App</strong> all'interno di un blocco di compilazione condizionale di un'app di Windows Store, che presenta un aspetto simile all'esempio seguente:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Questo codice consente di accedere al nuovo servizio mobile nell'app tramite un'istanza della <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient class</a>. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Questo campo static è disponibile in tutte le pagine dell'app.</p>
</li>
<li><p>Fare clic con il pulsante destro del mouse sul progetto di app per Windows Phone, selezionare <strong>Aggiungi</strong> e fare clic su <strong>Servizio connesso</strong>, quindi selezionare il servizio mobile appena creato e fare clic su <strong>OK</strong>. </p>
<p>Lo stesso codice viene aggiunto al file App.xaml.cs condiviso, ma questa volta all'interno di un blocco di compilazione condizionale di un'app per Windows Phone.</p></li>
</ol>

A questo punto, l'app di Windows Store e l'app di Windows Phone Store sono entrambe connesse al nuovo sevizio mobile. Il passaggio successivo consiste nel testare il nuovo progetto di servizio mobile.


##<a name="test-the-service-locally"></a>Testare il progetto di servizio mobile localmente

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile

In questa sezione l'app per Windows universale verrà aggiornata per l'uso del servizio mobile come servizio back-end per l'applicazione. È necessario apportare modifiche solo al file di progetto MainPage.xaml.cs nella cartella di progetto GetStartedWithData.Shared. 

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


##<a name="publish-mobile-service"></a>Pubblicare il servizio mobile in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Testare il servizio mobile ospitato in Azure

Ora è possibile testare entrambe le versioni dell'app di Windows universale con il servizio mobile ospitato in Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>Visualizzare i dati archiviati nel database SQL

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
L'esercitazione **Introduzione ai dati** è terminata.

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione illustra le nozioni di base per consentire a un'app di Windows universale di usare dati in Servizi mobili. In seguito, provare a eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

* [Usare script per la convalida e la modifica di dati]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

Una volta completata la serie relativa ai dati, provare a eseguire una delle esercitazioni seguenti:

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] 
  <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Riferimento per i concetti e le procedure di .NET per Servizi mobili]
  <br/>Altre informazioni su come usare Servizi mobili con .NET.
  
<!-- Anchors. -->

[Scaricare il progetto dell'app di Windows Store]: #download-app
[Creare un nuovo servizio mobile da Visual Studio]: #create-service
[Testare il progetto di servizio mobile localmente]: #test-the-service-locally
[Aggiornare l'app per l'uso del servizio mobile]: #update-app
[Testare l'app nel servizio ospitato localmente]: #test-locally-hosted
[Pubblicare il servizio mobile in Azure]: #publish-mobile-service
[Testare l'app nel servizio ospitato in Azure]: #test-azure-hosted
[Visualizzare i dati archiviati nel database SQL]: #view-stored-data
[Passaggi successivi]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Usare script per la convalida e la modifica di dati]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Introduzione alla sincronizzazione dei dati offline]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Portale di gestione]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sito degli esempi di codice di Developer Network]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!--HONumber=42-->
