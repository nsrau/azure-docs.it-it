<properties
	pageTitle="Aggiungere Servizi mobili a un'app esistente in iOS"
	description="Informazioni su come iniziare a usare Servizi mobili per sfruttare i dati nell'app per iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# Aggiungere Servizi mobili a un'app esistente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

Questa esercitazione consente di scaricare un'app esistente che archivia i dati in memoria e di modificarla per l'uso con un servizio mobile di Azure.

La [Guida introduttiva] costituisce un prerequisito di questa esercitazione, si riutilizzerà infatti il servizio mobile creato nella Guida introduttiva.


##<a name="download-app"></a>Scaricare il progetto GetStartedWithData

Questa esercitazione è basata sull'app per iOS [GetStartedWithData]. L'app è identica a quella della [Guida introduttiva], ad eccezione del fatto che gli elementi aggiunti vengono archiviati in memoria.

Scaricare l'app per iOS [GetStartedWithData]. In Xcode aprire il progetto ed esaminare il file **TodoService.m**. Sono presenti otto commenti **// TODO** che specificano i passaggi utili al funzionamento dell'app.

##<a name="update-app"></a>Aggiornare l'app per l'uso del servizio mobile per l'accesso ai dati

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Testare l'app

1. In Xcode fare clic su **Run** per avviare l'app. Aggiungere elementi all'elenco todo digitando un testo e facendo clic su **+**.

2. Verificare che le modifiche siano state salvate in modo permanente nel database in Azure. Esaminare il database usando il portale di gestione di Azure oppure il comando Esplora oggetti di SQL Server in Visual Studio.

3. Per esaminare il database tramite il portale, nella pagina Dashboard del servizio mobile fare clic sul nome del database, fare clic su 'Gestisci' per gestire il database e l'accesso. Eseguire la query seguente, ma usare il nome del servizio mobile anziché `todolist`.

```
        SELECT * FROM [todolist].[todoitems]
```

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Publish the mobile service to Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->

[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[JavaScript backend version]: /develop/mobile/tutorials/get-started-with-data-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784

[Guida introduttiva]: mobile-services-dotnet-backend-ios-get-started.md
[GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!---HONumber=July15_HO2-->