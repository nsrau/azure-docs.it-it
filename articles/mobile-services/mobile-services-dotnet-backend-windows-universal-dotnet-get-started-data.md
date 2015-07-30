<properties 
	pageTitle="Aggiungere Servizi mobili a un'app esistente (Windows Store) | Mobile Dev Center" 
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="wesmc"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Panoramica

Questo argomento illustra come usare Servizi mobili di Azure come origine dati di back-end per un'app di Windows Store. In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si visualizzeranno le modifiche apportate ai dati durante l'esecuzione dell'app.

Il servizio mobile che viene creato in questa esercitazione è un servizio mobile back-end .NET. Il back-end .NET consente di usare i linguaggi .NET e Visual Studio per la logica di business sul lato server nel servizio mobile, nonché di eseguire il servizio mobile ed effettuarne il debugging sul computer locale in uso. Per creare un servizio mobile che consenta di scrivere la logica di business sul lato server in JavaScript, vedere la versione per back-end JavaScript di questo argomento.

>[AZURE.NOTE]Questo argomento descrive come usare gli strumenti in Visual Studio Professional 2013 con aggiornamento 3 per connettere un nuovo servizio mobile a un'app di Windows universale. È possibile eseguire la stessa procedura per connettere un servizio mobile a un'app di Windows Store o Windows Phone Store 8.1. Per connettere un servizio mobile a un'app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, vedere [Introduzione ai dati per Windows Phone](mobile-services-dotnet-backend-windows-phone-get-started-data.md).

> Se non è possibile eseguire l'aggiornamento a Visual Studio Professional 2013 aggiornamento 3 o si preferisce aggiungere manualmente il progetto di servizio mobile a una soluzione app di Windows Store, vedere [questa versione](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md) dell'argomento.

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> Update 3 o versione successiva. 

##Scaricare il progetto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##Creare un nuovo servizio mobile da Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>In Esplora soluzioni, aprire il file di codice App.xaml.cs nella cartella di progetto GetStartedWithData.Shared e notare il nuovo campo statico aggiunto alla classe <strong>App</strong> al'interno di un blocco di compilazione condizionale di un'app di Windows Store, il cui aspetto ricorda quello illustrato nel seguente esempio:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Questo codice consente di accedere al nuovo servizio mobile nell'app tramite un'istanza della <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">classe MobileServiceClient</a>. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Questo campo static è disponibile in tutte le pagine dell'app.</p>
</li>
<li><p>Fare clic con il pulsante destro del mouse sul progetto di app di Windows Phone, selezionare <strong>Aggiungi</strong> e fare clic su <strong>Servizio connesso...</strong>, quindi selezionare il servizio mobile appena creato e fare clic su <strong>OK</strong>. </p>
<p>Il medesimo codice viene aggiunto al file App.xaml.cs condiviso, ma questa volta all'interno di un blocco di compilazione condizionale di un'app di Windows Phone.</p></li>
</ol>

A questo punto, l'app di Windows Store e l'app di Windows Phone Store sono entrambe connesse al nuovo sevizio mobile. Il passaggio successivo consiste nel testare il nuovo progetto di servizio mobile.


##Test del progetto di servizio mobile in locale

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##Aggiornare l'app per l'uso del servizio mobile

In questa sezione l'app per Windows universale verrà aggiornata per l'utilizzo del servizio mobile come servizio back-end per l'applicazione. È necessario apportare modifiche solo al file di progetto MainPage.xaml.cs nella cartella di progetto GetStartedWithData.Shared.

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##Pubblicazione del servizio mobile in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##Testare il servizio mobile ospitato in Azure

Ora è possibile testare entrambe le versioni dell'app di Windows universale con il servizio mobile ospitato in Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##Visualizzazione dei dati archiviati nel database SQL

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
L'esercitazione è terminata.

##Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app di Windows universale di usare dati in Servizi mobili. Successivamente, è consigliabile leggere uno di questi altri argomenti:

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Informazioni di riferimento sui concetti e sulle procedure di Servizi mobili con C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Altre informazioni sull'uso di Servizi mobili con .NET.


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introduzione all'autenticazione]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Introduzione alle notifiche push]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[sincronizzazione dei dati offline]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!---HONumber=July15_HO2-->