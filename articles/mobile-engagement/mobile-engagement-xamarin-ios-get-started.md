<properties
	pageTitle="Introduzione ad Azure Mobile Engagement per Xamarin.iOS"
	description="Informazioni sull'uso di Azure Mobile Engagement con le funzionalità di analisi e notifiche push per le app Xamarin.iOS."
	services="mobile-engagement"
	documentationCenter="xamarin"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Introduzione ad Azure Mobile Engagement per app Xamarin.iOS

[AZURE.INCLUDE [Banner per la selezione del tipo di esercitazione](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati in un'applicazione Xamarin.iOS. In questa esercitazione si creerà un'app Xamarin.iOS vuota che raccoglie dati di base e riceve notifiche push tramite Apple Push Notification System (APNS).

Per completare questa esercitazione, è necessario disporre di:

+ [Xamarin Studio](http://xamarin.com/studio) 
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app iOS

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push.

Verrà creata un'app di base con Xamarin per illustrare l'integrazione.

###Creare un nuovo progetto Xamarin.iOS

1. Avviare Xamarin Studio. Passare a **File** -> **New** -> **Solution**. 

    ![][1]

2. Selezionare **Single View App**, assicurarsi che il linguaggio selezionato sia **C#** e quindi fare clic su **Next**.

    ![][2]

3. Specificare le informazioni richieste nei campi **App Name** e **Organization Identifier** e quindi fare clic su **Next**.

    ![][3]

	> [AZURE.IMPORTANT] Assicurarsi che il profilo di pubblicazione usato per la distribuzione dell'app iOS abbia un ID app che corrisponde esattamente all'identificatore del bundle riportato qui.

4. Aggiornare i campi **Project Name**, **Solution Name** e **Location**, se necessario, e fare clic su **Create**.

    ![][4]
 
Xamarin Studio creerà l'app demo in cui verrà integrato Mobile Engagement.

###Connettere l'app al back-end di Mobile Engagement

1. Fare clic sulla cartella **Packages** nelle finestre della soluzione e selezionare **Add Packages...**.

    ![][5]

2. Cercare **Microsoft Azure Mobile Engagement Xamarin SDK** e aggiungerlo alla soluzione.

    ![][6]
   
3. Aprire **AppDelegate.cs** e aggiungere l'istruzione using seguente:

		using Microsoft.Azure.Engagement.Xamarin;

4. Nel metodo **FinishedLaunching** aggiungere quanto segue per inizializzare la connessione con il back-end di Mobile Engagement. Assicurarsi di aggiungere **ConnectionString**. Questo codice usa anche un oggetto **NotificationIcon** fittizio che viene aggiunto da Mobile Engagement SDK e che è consigliabile sostituire.

		EngagementConfiguration config = new EngagementConfiguration {
		                ConnectionString = "YourConnectionStringFromAzurePortal",
		                NotificationIcon = UIImage.FromBundle("close")
		            };
	    EngagementAgent.Init (config);

##<a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata al back-end di Mobile Engagement.

1. Aprire **ViewController.cs** e aggiungere l'istruzione using seguente:

		using Microsoft.Azure.Engagement.Xamarin;

2. Sostituire la classe da cui `ViewController` eredita, modificando `UIViewController` in `EngagementViewController`.

##<a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto delle campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

### Modificare il delegato dell'applicazione

1. Aprire **AppDelegate.cs** e aggiungere l'istruzione using seguente:

		using System; 

2. A questo punto aggiungere il codice seguente all'interno del metodo `FinishedLaunching` per eseguire la registrazione per i messaggi push, dopo `EngagementAgent.init(...)`.

		if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Infine, aggiornare o aggiungere i metodi seguenti:

		public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. Nel file **Info.plist** della soluzione verificare che l'**identificatore del bundle** corrisponda all'**ID app** incluso nel profilo di provisioning nel centro per sviluppatori Apple.

	![][7]

5. Nello stesso file **Info.plist** assicurarsi di aver selezionato **Enable Background Modes** e **Remote Notifications**.

 	![][8]

6. Eseguire l'app nel dispositivo associato a questo profilo di pubblicazione.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png

<!---HONumber=AcomDC_0330_2016-->