<properties
	pageTitle="Aggiungere notifiche push all'app per iOS con le app per dispositivi mobili di Azure"
	description="Informazioni su come usare le app per dispositivi mobili di Azure per inviare notifiche push all'app per iOS."
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
	ms.date="06/28/2016"
	ms.author="krisragh"/>


# Aggiungere notifiche push all'app iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Panoramica
In questa esercitazione si aggiungeranno notifiche push al progetto di [avvio rapido di iOS] per attivare l'invio di una notifica push ogni volta che viene inserito un record. Questa esercitazione è basata sull'esercitazione relativa all'[avvio rapido di iOS], che deve essere completata per prima. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Il [simulatore iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), quindi per questa esercitazione è necessario un dispositivo iOS fisico e l'[iscrizione all'Apple Developer Program](https://developer.apple.com/programs/ios/).

##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>Registrare l'app per le notifiche push

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## Configurare Azure per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Aggiungere notifiche push all'app

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Sottoporre a test le notifiche push nell'app

[AZURE.INCLUDE [Testare le notifiche push nell'app](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Altro

* I modelli offrono flessibilità per inviare notifiche push multipiattaforma e push localizzati. [Come usare la libreria client iOS per le app per dispositivi mobili di Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) illustra come registrare modelli.
* I tag consentono di orientarsi a clienti segmentati con notifiche push. [Lavorare con l’SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) illustra come aggiungere tag all’installazione di un dispositivo.

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

<!-- URLs. -->
[avvio rapido di iOS]: app-service-mobile-ios-get-started.md

<!---HONumber=AcomDC_0706_2016-->