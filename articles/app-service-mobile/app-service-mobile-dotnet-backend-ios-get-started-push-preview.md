<properties
	pageTitle="Aggiungere notifiche push all'app iOS tramite Azure App Service"
	description="Informazioni su come usare il servizio app di Azure per inviare notifiche push all'app iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/01/2015"
	ms.author="krisragh"/>


# Aggiungere notifiche push all'app per iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

In questo argomento viene illustrato come aggiungere notifiche push per il [progetto di guida introduttiva](app-service-mobile-dotnet-backend-ios-get-started-preview.md), in modo che il servizio mobile sia in grado di inviare una notifica push ogni volta che viene inserito un record. È necessario completare prima l'esercitazione [Introduzione ad App per dispositivi mobili].

> [AZURE.NOTE]Il [simulatore di iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), quindi è necessario utilizzare un dispositivo iOS fisico. È inoltre necessario iscriversi all'[Apple Developer Program](https://developer.apple.com/programs/ios/), una membership a pagamento.

##<a name="review"></a>Verificare la configurazione del progetto server (facoltativo)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-push-preview](../../includes/app-service-mobile-dotnet-backend-enable-push-preview.md)]

[AZURE.INCLUDE [Abilitare le notifiche push per Apple](../../includes/enable-apple-push-notifications.md)]

## Configurare Azure per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Aggiornare il codice back-end per l'invio di notifiche push

* Scaricare il progetto di Visual Studio per il codice back-end. Nel portale fare clic su **Sfoglia** > nome app > **Aggiungi client** > **iOS** (Objective-C o Swift) > **Scaricare ed eseguire il progetto del server**. Aprire **Controller** > TodoItemController.cs e aggiungere le istruzioni using seguenti:

```
			using Microsoft.Azure.Mobile.Server.Config;
			using Microsoft.Azure.NotificationHubs;
```

* Aggiungere il codice seguente a `PostTodoItem` dopo la chiamata di `InsertAsync`. Quando viene inserito un elemento, questo codice invia una notifica push con il testo dell'elemento.

```
        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.SendAppleNativeNotificationAsync(appleNotificationPayload);
```

## <a name="publish-the-service"></a>Pubblicare il servizio mobile in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

[AZURE.INCLUDE [Aggiungere notifiche push all'app](../../includes/app-service-add-push-notifications-to-app.md)]

[AZURE.INCLUDE [Sottoporre a test le notifiche push nell'app](../../includes/test-push-notifications-in-app.md)]

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Azure Mobile App iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=529823
[Azure Notification Hubs Nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-ios-get-started.md
[Introduzione ad App per dispositivi mobili]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
 

<!---HONumber=August15_HO6-->