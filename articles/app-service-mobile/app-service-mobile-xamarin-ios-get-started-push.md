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
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Aggiungere notifiche push all'app Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Overview

Questa esercitazione è basata sull'esercitazione della [guida introduttiva di Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) che deve essere completata per prima. In questa esercitazione si aggiungeranno notifiche push al progetto di creazione di un'app per Xamarin.iOS, in modo da attivare l'invio di una notifica push ogni volta che viene inserito un record. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Prerequisiti

* Completare l'esercitazione della [guida introduttiva di Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md).

* Un dispositivo iOS fisico. Le notifiche push non sono supportate dal simulatore iOS.

##Registrare l'app per le notifiche push nel portale per sviluppatori Apple

[AZURE.INCLUDE [Hub di notifica Xamarin - Abilita notifiche Push di Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##Configurare l'app per dispositivi mobili per l'invio di notifiche push

Per configurare l'app per l'invio di notifiche, creare un nuovo hub e configurarlo per i servizi di notifica della piattaforma che si intende usare.

1. Nel [Portale Azure](https://portal.azure.com/) fare clic su **Sfoglia** > **App per dispositivi mobili** > la propria app per dispositivi mobili > **Impostazioni** > **Dispositivi mobili** > **Push** > **Hub di notifica** > **+ Hub di notifica**, specificare un nome e uno spazio dei nomi per un nuovo hub di notifica e quindi fare clic sul pulsante **OK**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. Nel pannello per la creazione dell'hub di notifica, fare clic su **Crea**.

3. Fare clic su **Push** > **Apple (APN)** > **Carica certificato**. Caricare il file del certificato push (con estensione p12) esportato in precedenza. Assicurarsi di selezionare **Sandbox** se è stato creato un certificato push di sviluppo per le fasi di sviluppo e test. In caso contrario, scegliere **Produzione**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

Il servizio è ora configurato per l'uso con le notifiche push in iOS.

##Aggiornare il progetto server per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Configurare il progetto Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##Aggiungere notifiche push all'app

1. In **QSTodoService**, aggiungere la proprietà seguente in modo che l'oggetto **AppDelegate** possa acquisire il client per dispositivi mobili:

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
		using Newtonsoft.Json.Linq;

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

3. Nello stesso file, eseguire l'override dell'evento **RegisteredForRemoteNotifications**. In questo codice ci si sta registrando per una semplice notifica del modello che verrà inviata a tutte le piattaforme supportate dal server.

	Per ulteriori informazioni sui modelli con Hub di notifica, vedere [Modelli](../notification-hubs/notification-hubs-templates.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
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

	> [AZURE.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Nell'app digitare un'attività e fare clic sull'icona con il segno più (**+**).

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzata una notifica push.

L'esercitazione è stata completata.

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0824_2016-->