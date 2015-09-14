<properties 
	pageTitle="Aggiungere notifiche push all'app Xamarin.iOS tramite il servizio app di Azure"
	description="Informazioni su come usare il servizio app di Azure per inviare notifiche push all'app Xamarin.iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="yuaxu"/>

# Aggiungere notifiche push all'app Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Panoramica

In questa esercitazione si aggiungeranno notifiche push al progetto della [guida introduttiva per Xamarin.iOS] per attivare l'invio di una notifica push ogni volta che viene inserito un record. Questa esercitazione è basata sull'esercitazione della [guida introduttiva per Xamarin.iOS], che deve essere completata per prima. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Il [simulatore di iOS non supporta le notifiche push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), quindi è necessario utilizzare un dispositivo iOS fisico. È necessario iscriversi al programma [Apple Developer Program](https://developer.apple.com/programs/ios/).

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi alla versione di valutazione gratuita di Azure e ottenere fino a un massimo di 10 app per dispositivi mobili gratuite. Sarà possibile continuare a usarle anche dopo il termine del periodo di valutazione. Vedere [Versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

* Un computer Mac in cui sono stati installati [Xamarin Studio] e [Xcode] v4.4 o versione successiva. Si noti che l'esecuzione dell'app per Xamarin.iOS in Xamarin Studio risulta più facile in un Mac. È anche possibile eseguire l'app per Xamarin.iOS in Visual Studio nel computer Windows, ma la procedura è più complessa perché è necessario connettersi a un Mac in rete. Per informazioni su questa procedura, vedere [Installazione di Xamarin.iOS in Windows].

* Un dispositivo iOS fisico.

* Completare l'[esercitazione della guida introduttiva](../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md).

## <a id="register"></a>Registrare l'app per le notifiche push

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## Configurare Azure per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>Distribuire il progetto server in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Configurare il progetto Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push"></a>Aggiungere notifiche push all'app

1. In **QSTodoService** aggiungere la proprietà seguente in modo che l'oggetto **AppDelegate** possa acquisire il client per dispositivi mobili:
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Aggiungere l'istruzione `using` seguente all'inizio del file **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;

2. In **AppDelegate** eseguire l'override dell'evento **FinishedLaunching**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. Nello stesso file eseguire l'override dell'evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. Sovrascrivere quindi l'evento **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

L'app è ora aggiornata per il supporto delle notifiche push.

## <a name="test"></a>Testare le notifiche push nell'app

1. Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.
	
	> [AZURE.NOTE]È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzata una notifica push.

L'esercitazione è stata completata.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[guida introduttiva per Xamarin.iOS]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step5.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step6.png
[7]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step7.png

[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step9.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-02.png
[103]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-03.png
[104]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-04.png
[105]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-05.png
[106]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-06.png
[107]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-07.png
[108]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-08.png

[110]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-10.png
[111]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-11.png
[112]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-12.png
[113]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-13.png
[114]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-14.png
[115]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-15.png
[116]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-16.png
[117]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-17.png

[120]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-20.png
[121]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-21.png
[122]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-22.png
[123]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-23.png
[124]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-24.png

[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /it-IT/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /it-IT/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /it-IT/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /it-IT/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /it-IT/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /it-IT/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
[Installazione di Xamarin.iOS in Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303

 

<!---HONumber=September15_HO1-->