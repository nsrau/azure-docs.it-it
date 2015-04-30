<properties
	pageTitle="Introduzione ad Hub di notifica per le app per Xamarin iOS"
	description="Informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin iOS."
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="yuaxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="donnam"/>

# Introduzione ad Hub di notifica

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Informazioni generali

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS.
In questa esercitazione si creerà un'app per Xamarin.iOS vuota che riceve notifiche push tramite Apple Push Notification Service (APN). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

Questa esercitazione illustra lo scenario di trasmissione semplice tramite hub di notifica. 

##Prerequisiti

Per completare questa esercitazione, è necessario disporre dei seguenti prerequisiti:

+ [XCode 6.0][Installare Xcode]
+ Dispositivo con iOS 7.0 o versione successiva
+ Iscrizione a iOS Developer Program
+ [Xamarin.iOS]
+ [Componente Servizi mobili di Azure]

   > [AZURE.NOTE] Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un simulatore.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Xamarin.iOS.

> [AZURE.IMPORTANT] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Il servizio APNS usa i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service].


<h2><a name="certificates"></a>Generare il file di richiesta di firma del certificato</h2>

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene usato da Apple per la generazione di un certificato firmato.

1. Dalla cartella Utility eseguire lo strumento Accesso Portachiavi.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

  	![][5]

3. Selezionare il proprio **Indirizzo e-mail utente**, digitare i valori di **Nome comune** e **Indirizzo e-mail CA**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

  	![][6]

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

  	![][7]

  	Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

Successivamente, registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

<h2><a name="register"></a>Registrare l'app per le notifiche push</h2>

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push.  

1. Se l'app non è stata ancora registrata, accedere al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> in Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identificatori**, quindi su **ID app** e infine fare clic sul segno **+** per registrare una nuova app.

   	![][105]

2. Digitare un nome per l'app in **Description**, inserire un valore per **Bundle Identifier**, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continue**.

   	![][106]

   	![][107]

   	![][108]


	Verrà generato l'ID dell'app e verrà chiesto di inviare le informazioni. Fare clic su **Invia**.

   	![][109]

	Dopo aver fatto clic su **Submit**, verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.

   	![][110]

	> [AZURE.NOTE] Se si sceglie di specificare un valore **Bundle Identifier** diverso da **MobileServices.Quickstart**, è necessario aggiornare il valore dell'identificatore bundle anche nel progetto Xcode.

3. Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

   	![][111]

	Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID:

   	![][112]

   	![][113]

4. Fare clic su **Edit**, scorrere fino alla fine della schermata e fare clic su **Create Certificate** nella sezione **Development Push SSL Certificate**.

   	![][114]

	Verrà visualizzato l'assistente "Add iOS Certificate".

   	![][115]

	> [AZURE.NOTE] In questa esercitazione viene usato un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.

5. Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

  	![][116]

6. Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

  	![][118]

  	![][119]  

   	Il certificato di firma verrà scaricato e salvato nel computer nella cartella **Download**.

  	![][9]

    > [AZURE.NOTE] Per impostazione predefinita, il nome del file scaricato di un certificato di sviluppo è **aps_development.cer**.

7. Fare doppio clic sul certificato push scaricato **aps_development.cer**.

	Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

   	![][10]

	> [AZURE.NOTE]
	> Il nome del certificato potrebbe essere diverso, ma verrà preceduto da <strong>Apple Development iOS Push Notification Services:</strong>.

	Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 e caricarlo nell'hub di notifica per abilitare le notifiche push tramite il servizio APN.

<h2><a name="profile"></a>Creare un profilo di provisioning per l'app</h2>

1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Profili di provisioning**, **Tutti** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisioning Profile**.

   	![][120]

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

   	![][121]

3. Selezionare quindi l'ID dell'app per Servizi mobili dall'elenco a discesa **ID app** e fare clic su **Continua**.

   	![][122]

4. Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**.

   	![][123]

5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**.

   	![][124]

6. Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**.

   	![][125]

   	![][126]

  	Verrà creato un nuovo profilo di provisioning.

7. In Xcode aprire l'Organizer e selezionare la visualizzazione Devices, selezionare **Provisioning Profiles** nella sezione **Library** nel riquadro a sinistra, quindi importare il profilo di provisioning appena creato.

8. A sinistra, selezionare il dispositivo e importare di nuovo il profilo di provisioning.

9. In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, digitare un nome per il certificato, selezionare il formato **.p12** , quindi fare clic su **Salva**.

   	![][18]

  	Annotare il nome del file e il percorso del certificato esportato.

Per effetto di questa operazione, il progetto Xcode userà il nuovo profilo per la firma del codice. A questo punto è necessario caricare il certificato nell'hub di notifica.

<h2><a name="configure-hub"></a>Configurare l'hub di notifica</h2>

1. Accedere al [portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub di notifica**, quindi **Creazione rapida**.

   	![][27]

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**.

   	![][28]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configura** nella parte superiore.

   	![][29]

5. Fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][210]

6. Selezionare la scheda **Configura** nella parte superiore, quindi fare clic su **Carica** per le impostazioni di notifica di Apple. Selezionare quindi il certificato **.p12** esportato in precedenza e la password per il certificato. Selezionare se si desidera usare il servizio push **Production** (per inviare notifiche push agli utenti che hanno acquistato l'app dallo Store) o **Sandbox** (durante lo sviluppo).

   	![][211]

7. Fare clic sulla scheda **Dashboard** nella parte superiore, quindi su **Informazioni di connessione**. Prendere nota delle due stringhe di connessione.

   	![][212]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Apple e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

<h2><a name="connecting-app"></a>Connettere l'app all'hub di notifica</h2>

### Creare un nuovo progetto

1. In Xamarin Studio, creare un nuovo progetto iOS e selezionare il modello **Unified API > Single View Application**.

   	![][31]

2. È necessario aggiungere prima di tutto un riferimento al componente di messaggistica di Azure. Nella visualizzazione Solution fare clic con il pulsante destro del mouse sulla cartella **Components** del progetto e scegliere **Get More Components**. Cercare il componente **Messaggistica di Azure** e aggiungerlo al progetto.

3. In **AppDelegate.cs** aggiungere la seguente istruzione using:

    using WindowsAzure.Messaging;

4. Dichiarare un'istanza di **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }

5. Creare una classe **Constants.cs** con le seguenti variabili:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. In **AppDelegate.cs** aggiornare **FinishedLaunching()** in base al seguente esempio:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. Eseguire l'override del metodo **RegisteredForRemoteNotifications()** in **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Eseguire l'ovverride del metodo **ReceivedRemoteNotification()** in **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Creare il seguente metodo **ProcessNotification()** in **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the json gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] È possibile scegliere di eseguire l'override di **FailedToRegisterForRemoteNotifications()**per gestire situazioni come l'assenza di una connessione di rete e così via.


10. Eseguire l'app sul dispositivo.

<h2><a name="send"></a>Inviare notifiche dal back-end</h2>

È possibile inviare notifiche usando gli hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1. Creare una nuova applicazione console in Visual C#:

   	![][213]

2. Aggiungere un riferimento a Service Bus SDK di Azure con il pacchetto <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet</a>. Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi su **Gestione pacchetti libreria** e infine su **Console di gestione pacchetti**. Quindi nella finestra della console digitare:

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Aprire il file Program.cs e aggiungere la seguente istruzione using:

        using Microsoft.ServiceBus.Notifications;

3. Nella classe  `Program`  aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Quindi aggiungere la riga seguente al metodo  `Main` :

         SendNotificationAsync();
		 Console.ReadLine();

5. Premere F5 per eseguire l'app. Si dovrebbe ricevere un avviso sul dispositivo. Se si usa una connessione Wi-Fi, accertarsi che la connessione sia funzionante.

È possibile trovare tutti i payload disponibili nella [guida alla programmazione di notifiche push e locali di Apple].

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili], quindi seguire questa procedura:

1. Accedere al [portale di gestione di Azure] e selezionare il servizio mobile.

2. Selezionare la scheda **Scheduler** nella parte superiore.

   	![][215]

3. Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **On demand**.

   	![][216]

4. Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5. Inserire il seguente script all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a  *DefaultFullSharedAccessSignature* ottenuta in precedenza. Fare clic su **Salva**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. Fare clic su **Esegui una volta** sulla barra inferiore. Si dovrebbe ricevere un avviso sul dispositivo.

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi iOS. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Uso di Hub di notifica per inviare notifiche push agli utenti], mentre, se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Uso di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere la [pagina relativa alle linee guida per gli hub di notifica] e quella relativa alle [procedure di Hub di notifica per Android].

<!-- Anchors. -->
[Generare la richiesta di firma del certificato]: #certificates
[Registrare l'app e abilitare le notifiche push]: #register
[Creare un profilo di provisioning per l'app]: #profile
[Configurare l'hub di notifica]: #configure-hub
[Connettere l'app all'hub di notifica]: #connecting-app
[Inviare notifiche dal back-end]: #send
[Passaggi successivi]:#next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[SDK di Servizi mobili per iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started-xamarin-ios
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[pagina relativa alle linee guida per gli hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedure di Hub di notifica per iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[procedure di Hub di notifica per Android]: http://msdn.microsoft.com/library/jj927168.aspx
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portale di provisioning per iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Uso di Hub di notifica per inviare notifiche push agli utenti]: /manage/services/notification-hubs/notify-users-aspnet
[Uso di Hub di notifica per inviare le ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet

[Guida alla programmazione di notifiche push e locali]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[guida alla programmazione di notifiche push e locali di Apple]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!--HONumber=49-->