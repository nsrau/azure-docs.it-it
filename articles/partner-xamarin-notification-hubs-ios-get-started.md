<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="" />

Introduzione ad Hub di notifica
===============================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/it-it/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/it-it/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/it-it/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/it-it/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS. In questa esercitazione si creerà un'app per iOS vuota che riceve notifiche push tramite il Servizio di notifica Push di Apple. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica. Il codice compilato è disponibile nell'esempio di [app NotificationHubs](http://go.microsoft.com/fwlink/p/?LinkId=331329).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Generazione della richiesta di firma del certificato](#certificates)
2.  [Registrazione dell'app e abilitazione delle notifiche push](#register)
3.  [Creazione di un profilo di provisioning per l'app](#profile)
4.  [Configurazione dell'hub di notifica](#configure-hub)
5.  [Connessione dell'app all'hub di notifica](#connecting-app)
6.  [Invio di notifiche dal back-end](#send)

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

-   [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Dispositivo con iOS 5.0 o versione successiva
-   Iscrizione a iOS Developer Program
-   [Xamarin.iOS]
-   [Componente Servizi mobili di Azure](http://components.xamarin.com/view/azure-mobile-services/)

    **Nota**

    Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app per iOS.

**Nota**

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F).

Il servizio APNS utilizza i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Generazione del file della richiesta di firma del certificatoGenerazione del file della richiesta di firma del certificato
--------------------------------------------------------------------------------------------------------------------------

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene utilizzato da Apple per la generazione di un certificato firmato.

1.  Dalla cartella Utility eseguire lo strumento Accesso Portachiavi.

2.  Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

1.  Fare clic su **Indirizzo e-mail utente**, digitare i valori di **Nome comune** e **Indirizzo e-mail CA**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

1.  Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png) 

	Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

Successivamente, registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

Registrazione dell'appRegistrazione dell'app per le notifiche push
------------------------------------------------------------------

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire un'ulteriore registrazione per abilitare le notifiche push.

1.  Se l'app non è ancora stata registrata, accedere al [portale di provisioning iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per registrare una nuova app.

    ![][105] 

2.  Digitare un nome per l'app in **Description**, inserire un valore per **Bundle Identifier**, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continue**.

    ![][106]

    ![][107] 

    ![][108]

    Verrà generato l'ID dell'app e verrà chiesto di inviare le informazioni. Fare clic su **Submit**.

    ![][109] 

    Dopo aver fatto clic su **Submit** verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.

    ![][110]

    **Nota**

    Se si sceglie di specificare un valore per **Bundle Identifier** diverso da **MobileServices.Quickstart**, è necessario aggiornare anche il valore dell'identificatore del bundle nel progetto Xcode.

3.  Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

    ![][111]

    Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID:

    ![][112] 
           
    ![][113]

4.  Fare clic su **Edit**, scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

    ![][114] 

    Verrà visualizzato l'assistente "Add iOS Certificate".

    ![][115] 

    **Nota**

    In questa esercitazione viene utilizzato un certificato di sviluppo. La stessa procedura viene utilizzata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.

5.  Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png)

1.  Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png) 

	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png)

 	Il certificato di firma verrà scaricato e salvato nel computer nella cartella **Download**. 

	![](./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png)

    <div class="dev-callout"><b>Nota</b>
    <p>Per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato <strong>aps_development.cer</strong>.</p>
    </div>

1.  Fare doppio clic sul certificato push scaricato **aps\_development.cer**.

    Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

    ![][10]

    > [WACOM.NOTE] Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Notification Services:**.

    Questo certificato verrà utilizzato in un secondo momento per generare un file con estensione p12 e caricarlo nell'hub di notifica per abilitare le notifiche push tramite APNS.

Provisioning dell'appCreazione di un profilo di provisioning per l'app
----------------------------------------------------------------------

1.  Nel [portale di provisioning iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisioning Profile**.

    ![][120]

2.  Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

    ![][121]

3.  Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**.

    ![][122]

4.  Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**.

    ![][123]

5.  In **Devices** selezionare i dispositivi da utilizzare per il test e fare clic su **Continue**.

    ![][124]

6.  Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**.

    ![][125]
           
    ![][126]

	Verrà creato un nuovo profilo di provisioning.

1.  In Xcode aprire l'Organizer e selezionare la visualizzazione Devices, selezionare **Provisioning Profiles** nella sezione **Library** nel riquadro a sinistra, quindi importare il profilo di provisioning appena creato.

2.  A sinistra, selezionare il dispositivo e importare di nuovo il profilo di provisioning.

3.  In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, digitare un nome per il certificato, selezionare il formato **.p12**, quindi fare clic su **Salva**.

    ![][18]

	Annotare il nome del file e il percorso del certificato esportato.

Per effetto di questa operazione, il progetto Xcode utilizzerà il nuovo profilo per la firma del codice. A questo punto è necessario caricare il certificato nell'hub di notifica.

Configurazione dell'hub di notificaConfigurazione dell'hub di notifica
----------------------------------------------------------------------

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic su **+NEW** nella parte inferiore della schermata.

2.  Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

    ![][27]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

    ![][28]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configure** nella parte superiore.

    ![][29]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

    ![][210]

6.  Selezionare la scheda **Configure** nella parte superiore e quindi fare clic su **Upload** per le impostazioni di notifica di Apple. Selezionare quindi il certificato **.p12** esportato in precedenza e la password per il certificato. Selezionare se si desidera utilizzare il servizio push **Production** (per inviare notifiche push agli utenti che hanno acquistato l'app dallo Store) o **Sandbox** (durante lo sviluppo).

    ![][211]

7.  Fare clic sulla scheda **Dashboard** nella parte superiore e quindi su **Connection Information**. Prendere nota delle due stringhe di connessione.

    ![][212]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Apple e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

Connessione dell'appConnessione dell'app all'hub di notifica
------------------------------------------------------------

1.  In Xamarin Studio creare un nuovo progetto iOS e selezionare il modello **Single View Application**.

    ![][31]

2.  A questo punto è necessario aggiungere un riferimento al componente Servizi mobili di Azure. Nella visualizzazione Solution fare clic con il pulsante destro del mouse sulla cartella **Components** del progetto e scegliere **Get More Components**. Cercare il componente **Servizi mobili di Azure** e aggiungerlo al progetto.

3.  In **AppDelegate.cs** aggiungere l'istruzione using seguente:

         using WindowsAzure.Messaging;

4.  Dichiarare un'istanza di **SBNotificationHub**:

         private SBNotificationHub Hub { get; set; }

5.  Creare una classe **Constants.cs** con le variabili seguenti:

         // Azure app specific connection string and hub path
         public const string ConnectionString = "<Azure connection string>";
         public const string NotificationHubPath = "<Azure hub path>";

6.  In **AppDelegate.cs** aggiornare **FinishedLaunching()** in base all'esempio seguente:

         public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
         {
             // NOTE: Don't call the base implementation on a Model class
             // see http://docs.xamarin.com/guides/ios/application_fundamentals/delegates,_protocols,_and_events 

             UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                 UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
             UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

             return true;
         }

7.  In **AppDelegate.cs** eseguire l'override dell'evento **RegisteredForRemoteNotifications()**:

         public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
         {
             // NOTE: Don't call the base implementation on a Model class
             // see http://docs.xamarin.com/guides/ios/application_fundamentals/delegates,_protocols,_and_events 

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

8.  In **AppDelegate.cs** eseguire l'override dell'evento **ReceivedRemoteNotification()**:

         public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
         {
             ProcessNotification(userInfo, false);
         }

9.  Creare il metodo **ProcessNotification()** seguente in **AppDelegate.cs**:

         void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
         {
             // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
             if (null != options && options.ContainsKey(new NSString("aps")))
             {
                 //Get the aps dictionary
                 NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                 string alert = string.Empty;

                 //Extract the alert text
                 //NOTE: If you're using the simple alert by just specifying "  aps:{alert:"alert msg here"}  "
                 //this will work fine.  But if you're using a complex alert with Localization keys, etc., 
                 //your "alert" object from the aps dictionary will be another NSDictionary... Basically the 
                 //json gets dumped right into a NSDictionary, so keep that in mind
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

    **Nota**

    È possibile scegliere di eseguire l'override di **FailedToRegisterForRemoteNotifications()** per gestire situazioni quali l'assenza di una connessione di rete e così via.

10. Eseguire l'app sul dispositivo.

Invio di notificheInvio di notifiche dal back-end
-------------------------------------------------

È possibile utilizzare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST](http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx). In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1.  Creare una nuova applicazione console in Visual C\#:

    ![][213]

2.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

         Install-Package WindowsAzure.ServiceBus e premere INVIO.

3.  Aprire il file Program.cs e aggiungere l'istruzione using seguente:

         using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

5.  Aggiungere quindi la riga seguente nel metodo `Main`:

          SendNotificationAsync();
          Console.ReadLine();

6.  Premere F5 per eseguire l'app. Si dovrebbe ricevere un avviso sul dispositivo. Se si utilizza una connessione Wi-Fi, accertarsi che la connessione sia funzionante.

È possibile trovare tutti i payload disponibili nella [guida alla programmazione di notifiche push e locali](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1) di Apple.

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-ios), quindi eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e selezionare il servizio mobile.

2.  Selezionare la scheda **Scheduler** nella parte superiore.

    ![][215]

3.  Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **On demand**.

    ![][216]

4.  Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5.  Inserire lo script seguente all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a *DefaultFullSharedAccessSignature* ottenuti nel passaggio precedente. Fare clic su **Save**.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
         notificationHubService.apns.send(
            null,

    	{"aps":

                 {
                "alert": "Hello from Mobile Services!"
                }

    	}, function (error) {

                 if (!error) {

    	console.warn("Notification successful");

                 }

     	    }

         );

6.  Fare clic su **Run Once** sulla barra inferiore. Si dovrebbe ricevere un avviso sul dispositivo.

Passaggi successivi
-------------------

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi iOS. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet), mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Per ulteriori informazioni sull'utilizzo di Hub di notifica, vedere la pagina relativa alle [linee guida per gli hub di notifica](http://msdn.microsoft.com/it-it/library/jj927170.aspx) e quella relativa alle [procedure di Hub di notifica per iOS](http://msdn.microsoft.com/it-it/library/jj927168.aspx).


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

