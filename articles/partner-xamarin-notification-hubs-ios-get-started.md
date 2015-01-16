<properties urlDisplayName="Get Started" pageTitle="Introduzione ad Hub di notifica per le app per Xamarin iOS" metaKeywords="" description="Informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Xamarin iOS." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS. 
In questa esercitazione si creerà un'app per Xamarin.iOS vuota che riceve notifiche push tramite il servizio di notifica push di Apple (APN, Apple Push Notification). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][GitHub].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Generare la richiesta di firma del certificato] 
2. [Registrare l'app e abilitare le notifiche push]
3. [Creare un profilo di provisioning per l'app]
4. [Configurare l'hub di notifica]
5. [Connettere l'app all'hub di notifica]
6. [Inviare notifiche dal back-end]

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica. Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

+ [XCode 5.0][Installare Xcode] 
+ Dispositivo con iOS 5.0 o versione successiva
+ Iscrizione a iOS Developer Program
+ [Xamarin.iOS]
+ [Componente Servizi mobili di Azure]

   <div class="dev-callout"><b>Nota</b>
   <p>Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un simulatore.</p>
   </div>

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Xamarin.iOS. 

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

Il servizio APNS usa i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service].



<h2><a name="certificates"></a>Generare la richiesta di firma del certificato</h2>

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene usato da Apple per la generazione di un certificato firmato.

1. Dalla cartella Utility eseguire lo strumento Accesso Portachiavi.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

  	![][5]

3. Fare clic su **Indirizzo e-mail utente**, digitare i valori di **Nome comune** e **Indirizzo e-mail CA**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

  	![][6]

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

  	![][7]
  
  	Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

Successivamente, registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

<h2><a name="register"></a>Registrare l'app per le notifiche push</h2>

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push.  

1. Se l'app non è ancora stata registrata, accedere al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per registrare una nuova app.

   	![][105] 

2. Type a name for your app in **Description**, and a value for **Bundle Identifier**, check the "Push Notifications" option in the "App Services" section, and then click **Continue**.

   	![][106]

   	![][107] 

   	![][108]
   

	Verrà generato l'ID dell'app e verrà chiesto di inviare le informazioni. Fare clic su **Submit**.
   
   	![][109] 
   
	Once you click **Submit**, you will see the **Registration complete** screen, as shown below. Click **Done**.
   
   	![][110]

	<div class="dev-callout"><b>Nota</b>
	<p>Se si sceglie di specificare per <strong>Bundle Identifier</strong> un valore diverso da <b>MobileServices.Quickstart</b>, è necessario aggiornare anche il valore dell'identificatore del bundle nel progetto Xcode.</p>
	 </div>

3. Individuare l'ID dell'app appena creato e fare clic sulla relativa riga. 

   	![][111]
   
	Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID:
   
   	![][112] 
   
   	![][113]

4. Fare clic su **Edit**, scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

   	![][114] 

	This displays the "Add iOS Certificate" assistant.
   
   	![][115] 

	<div class="dev-callout"><b>Note</b>
	<p>This tutorial uses a development certificate. The same process is used when registering a production certificate. Just make sure that you set the same certificate type when you upload the certificate to Mobile Services.</p>
	</div>

5. Click **Choose File**, browse to the location in which you saved the CSR file that you created in the first task, and then click **Generate**. 

  	![][116]
  
6. Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.
 
  	![][118]

  	![][119]  
  
   	This downloads the signing certificate and saves it to your computer in your **Downloads** folder. 

  	![][9] 

    <div class="dev-callout"><b>Note</b>
	<p>By default, the downloaded file a development certificate is named <strong>aps_development.cer</strong>.</p>
    </div>

7. Double-click the downloaded push certificate **aps_development.cer**.

	This installs the new certificate in the Keychain, as shown below:

   	![][10]

	> [WACOM.NOTE]
	> Il nome del certificato potrebbe essere diverso, ma verrà preceduto da <strong>Apple Development iOS Push Notification Services:</strong>.

	Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 e caricarlo nell'hub di notifica per abilitare le notifiche push tramite APNS.

<h2><a name="profile"></a>Creare un profilo di provisioning per l'app</h2>
 
1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisioning Profile**.

   	![][120]

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

   	![][121]

3. Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**.

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

9. In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, digitare un nome per il certificato, selezionare il formato **.p12**, quindi fare clic su **Salva**.

   	![][18]

  	Annotare il nome del file e il percorso del certificato esportato.

Per effetto di questa operazione, il progetto Xcode userà il nuovo profilo per la firma del codice. A questo punto è necessario caricare il certificato nell'hub di notifica.

<h2><a name="configure-hub"></a>Configurare l'hub di notifica</h2>

1. Accedere al [portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, quindi **Hub di notifica** e infine **Creazione rapida**.

   	![][27]

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**.

   	![][28]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configura** nella parte superiore.

   	![][29]

5. Fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][210]

6. Selezionare la scheda **Configura** nella parte superiore e quindi fare clic su **Carica** per le impostazioni di notifica di Apple. Selezionare quindi il certificato **.p12** esportato in precedenza e la password per il certificato. Selezionare se si desidera usare il servizio push **Produzione** (per inviare notifiche push agli utenti che hanno acquistato l'app dallo Store) o **Sandbox** (durante lo sviluppo).

   	![][211]

7. Fare clic sulla scheda **Dashboard** nella parte superiore e quindi su **Informazioni di connessione**. Prendere nota delle due stringhe di connessione.

   	![][212]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Apple e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

<h2><a name="connecting-app"></a>Connettere l'app all'hub di notifica</h2>

### Scaricare la libreria WindowsAzure.Messaging

Questo assembly offre un modo semplice per effettuare la registrazione con Hub di notifica di Azure. È disponibile per il download tramite le istruzioni seguenti oppure nel [download di esempio][GitHub].

1. Scaricare l'origine per [WindowsAzure.Messaging] da GitHub.

2. Compilare il progetto e individuare l'assembly di output **WindowsAzure.Messaging.dll**, che sarà necessario durante la configurazione dell'applicazione Xamarin.iOS, più avanti.


### Creare un nuovo progetto

1. In Xamarin Studio creare un nuovo progetto iOS e selezionare il modello **Single View Application**.

   	![][31]

2. È necessario aggiungere prima di tutto un riferimento al componente Servizi mobili di Azure. Nella visualizzazione Solution fare clic con il pulsante destro del mouse sulla cartella **Components** del progetto e scegliere **Get More Components**. Cercare il componente **Servizi mobili di Azure** e aggiungerlo al progetto.

3. Aggiungere quindi un riferimento alla libreria WindowsAzure.Messaging scaricata in precedenza. Fare clic con il pulsante destro del mouse sulla cartella **References** del progetto e scegliere **Edit References**. Nella scheda **.Net Assembly** individuare **WindowsAzure.Messaging.dll**.

4. In **AppDelegate.cs** aggiungere le istruzioni using seguenti:

		using Microsoft.WindowsAzure.MobileServices;
    using WindowsAzure.Messaging;

5. Dichiarare un'istanza di **SBNotificationHub**:

		private SBNotificationHub Hub { get; set; }
		
6. Creare una classe **Constants.cs** con le variabili seguenti:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
	
	
7. In **AppDelegate.cs** aggiornare **FinishedLaunching()** in base all'esempio seguente:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8. Eseguire l'override del metodo **RegisteredForRemoteNotifications()** in **AppDelegate.cs**:

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

9. Eseguire l'override del metodo **ReceivedRemoteNotification()** in **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
        
10. Creare il metodo **ProcessNotification()** seguente in **AppDelegate.cs**:

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

    <div class="dev-callout"><b>Nota</b>
	<p>È possibile scegliere di eseguire l'override di **FailedToRegisterForRemoteNotifications()** per gestire situazioni quali l'assenza di una connessione di rete e così via.</p>
    </div>

	
11. Eseguire l'app sul dispositivo.

<h2><a name="send"></a>Inviare notifiche dal back-end</h2>

È possibile usare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1. Creare una nuova applicazione console in Visual C#: 

   	![][213]

2. Aggiungere un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto NuGet WindowsAzure.ServiceBus</a>. Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Aprire il file Program.cs e aggiungere l'istruzione using seguente:

        using Microsoft.ServiceBus.Notifications;

3. Nella classe `Program` aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Aggiungere quindi la riga seguente nel metodo `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

5. Premere F5 per eseguire l'app. Si dovrebbe ricevere un avviso sul dispositivo. Se si usa una connessione Wi-Fi, accertarsi che la connessione sia funzionante.

È possibile trovare tutti i payload disponibili nella [guida alla programmazione di notifiche push e locali] di Apple.

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili], quindi eseguire le operazioni seguenti:

1. Accedere al [portale di gestione di Azure] e selezionare il servizio mobile.

2. Selezionare la scheda **Utilità di pianificazione** nella parte superiore.

   	![][215]

3. Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **Su richiesta**.

   	![][216]

4. Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5. Inserire lo script seguente all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a *DefaultFullSharedAccessSignature* ottenuti nel passaggio precedente. Fare clic su **Salva**.

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


6. Fare clic su **Esegui una volta** sulla barra inferiore. Si riceverà un avviso sul dispositivo.

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi iOS. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti], mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Usare Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'utilizzo di Hub di notifica, vedere la pagina relativa alle [linee guida per gli hub di notifica] e quella relativa alle [procedure di Hub di notifica per iOS].

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
[Mobile Services SDK per iOS]: http://go.microsoft.com/fwLink/?LinkID=266533
[Pagina per l'invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Linee guida su Hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
[Procedure di Hub di notifica per iOS]: http://msdn.microsoft.com/it-it/library/jj927168.aspx
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portale di provisioning di iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Usare Hub di notifica per inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
[Usare Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet

[Guida alla programmazione di notifiche push e locali]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Servizio notifiche push Apple (APN)]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
