<properties 
	pageTitle="Aggiungere Servizi mobili a un'app di Windows universale | Servizi mobili di Azure" 
	description="Informazioni su come connettere un'app di Windows universale a Servizi mobili di Azure." 
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
	ms.date="07/22/2015" 
	ms.author="glenga"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Panoramica

Questo argomento illustra come usare Servizi mobili di Azure per sfruttare i dati in un'app di Windows universale. Le soluzioni per app di Windows universali includono progetti di app di Windows Store 8.1 e Windows Phone Store 8.1 e un progetto condiviso comune. Per altre informazioni, vedere [Creare app di Windows universali per Windows e Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

In questa esercitazione si scaricherà un progetto di Visual Studio 2013 per un'app di Windows universale che archivia dati in memoria, si creerà un nuovo servizio mobile e lo si integrerà con l'app e quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate ai dati durante l'esecuzione dell'app.

>[AZURE.NOTE]Questo argomento descrive come usare gli strumenti in Visual Studio Professional 2013 con aggiornamento 3 per connettere un nuovo servizio mobile a un'app di Windows universale. È possibile eseguire la stessa procedura per connettere un servizio mobile a un'app di Windows Store o Windows Phone Store 8.1. Per connettere un servizio mobile a un'app di Windows Phone 8.0 o Windows Phone Silverlight 8.1, vedere [Introduzione ai dati per Windows Phone](mobile-services-windows-phone-get-started-data.md).

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F).
* [Visual Studio Express 2013 per Windows](https://go.microsoft.com/fwLink/p/?LinkID=257546) con aggiornamento 2 o versione successiva 

##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Creare un nuovo servizio mobile da Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

&nbsp;&nbsp;7. In Esplora soluzioni, aprire il file di codice App.xaml.cs nella cartella di progetto GetStartedWithData.Shared e notare il nuovo campo statico aggiunto alla classe **App** al'interno di un blocco di compilazione condizionale di un'app di Windows Store, il cui aspetto ricorda quello illustrato nel seguente esempio:

	public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
	    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
	        "https://todolist.azure-mobile.net/",
	        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

&nbsp;&nbsp;Questo codice consente di accedere al nuovo servizio mobile nell'app tramite un'istanza della classe [MobileServiceClient]. Per creare il client, è necessario specificare l'URI e la chiave dell'applicazione del nuovo servizio mobile. Questo campo static è disponibile in tutte le pagine dell'app.

&nbsp;&nbsp;8. Fare clic con il pulsante destro del mouse sul progetto di app di Windows Phone, selezionare **Aggiungi** e fare clic su **Servizio connesso...**, quindi selezionare il servizio mobile appena creato e fare clic su **OK**. Il medesimo codice viene aggiunto al file App.xaml.cs condiviso, ma questa volta all'interno di un blocco di compilazione condizionale di un'app di Windows Phone.

A questo punto, l'app di Windows Store e l'app di Windows Phone Store sono entrambe connesse al nuovo sevizio mobile. Il passaggio successivo consiste nella creazione di una nuova tabella TodoItem nel servizio mobile.

##<a name="add-table"></a>Aggiungere una nuova tabella al servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Testare il servizio mobile ospitato in Azure

Ora è possibile testare entrambe le versioni dell'app di Windows universale con il servizio mobile ospitato in Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

&nbsp;&nbsp;4. Nel [portale di gestione di Azure] fare clic su **Servizi mobili** e quindi sul servizio mobile in questione.

&nbsp;&nbsp;5. Fare clic su **Dati** > **Sfoglia** e osservare che la tabella **TodoItem** ora contiene dati, con valori ID generati da Servizi mobili, e che alla tabella sono state aggiunte automaticamente colonne per garantire la corrispondenza con la classe TodoItem nell'app.
     
L'esercitazione è terminata.

## <a name="next-steps"> </a>Passaggi successivi

Questa esercitazione ha illustrato le nozioni di base per consentire a un progetto di app di Windows universale di usare dati in Servizi mobili. Successivamente, è consigliabile leggere uno di questi altri argomenti:

* [Introduzione all'autenticazione] <br/>Informazioni sull'autenticazione degli utenti dell'app.

* [Introduzione alle notifiche push] <br/>Informazioni sull'invio di una notifica push di base all'app.

* [Informazioni di riferimento sui concetti e sulle procedure di Servizi mobili con C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Altre informazioni sull'uso di Servizi mobili con .NET.
  
<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use the mobile service]: #update-app
[Test the app against Mobile Services]: #test-app
[View uploaded data in the Azure Management Portal]: #view-data
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Validate and modify data with scripts]: ../mobile-services-windows-store-dotnet-validate-modify-data-server-scripts.md
[Refine queries with paging]: ../mobile-services-windows-store-dotnet-add-paging-data.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Introduzione all'autenticazione]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Introduzione alle notifiche push]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

[portale di gestione di Azure]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!---HONumber=August15_HO6-->