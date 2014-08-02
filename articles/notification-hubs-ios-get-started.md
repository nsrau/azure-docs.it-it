<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Introduzione ad Hub di notifica
===============================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un'applicazione per iOS. In questa esercitazione si creerà un'app per iOS vuota che riceve notifiche push tramite il Servizio di notifica Push di Apple. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Generazione della richiesta di firma del certificato](#certificates)
2.  [Registrazione dell'app e abilitazione delle notifiche push](#register)
3.  [Creazione di un profilo di provisioning per l'app](#profile)
4.  [Configurazione dell'hub di notifica](#configure-hub)
5.  [Connessione dell'app all'hub di notifica](#connecting-app)
6.  [Invio di notifiche dal back-end](#send)

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a utilizzare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

-   [Mobile Services SDK per iOS](http://go.microsoft.com/fwLink/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Dispositivo con iOS 5.0 o versione successiva
-   Iscrizione a iOS Developer Program

    **Nota**

    Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app per iOS.

**Nota**

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Il servizio APNS utilizza i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service].

Generazione del file della richiesta di firma del certificatoGenerazione del file della richiesta di firma del certificato
--------------------------------------------------------------------------------------------------------------------------

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene utilizzato da Apple per la generazione di un certificato firmato.

1.  Dalla cartella Utility eseguire lo strumento Accesso Portachiavi.

2.  Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

1.  Fare clic su **Indirizzo e-mail utente**, selezionare un valore per **Nome comune**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**. Lasciare vuoto il campo **Indirizzo e-mail CA**, in quanto non è obbligatorio.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

1.  Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png)

Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

Successivamente, registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

Registrazione dell'appRegistrazione dell'app per le notifiche push
------------------------------------------------------------------

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire un'ulteriore registrazione per abilitare le notifiche push.

1.  Se l'app non è ancora stata registrata, accedere al [portale di provisioning iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per registrare una nuova app.

	![][B102] 

2.  Digitare un nome per l'app in **Description**, inserire il valore *MobileServices.Quickstart* in **Bundle Identifier**, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continue**. In questo esempio viene utilizzato l'ID **MobileServices.Quickstart**, tuttavia non è possibile riutilizzare lo stesso ID, in quanto gli ID app devono essere univoci per tutti gli utenti. È pertanto consigliabile aggiungere il proprio nome completo o le proprie iniziali dopo il nome dell'app.

	![][B103]
           
	Verrà generato l'ID dell'app e all'utente verrà chiesto di inviare le informazioni. Fare clic su **Submit**
           
	![][B104] 
           
	Dopo aver fatto clic su **Submit** verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.
           
	![][B105]

    > [WACOM.NOTE] Se si sceglie di specificare un valore per **Bundle Identifier** diverso da *MobileServices.Quickstart*, è necessario aggiornare anche il valore dell'identificatore del bundle nel progetto Xcode.

3.  Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

	![][B106]
           
	Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID. Fare clic sul pulsante **Settings**.
           
        ![][B107] 

4.  Scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

	![][B108] 

	Verrà visualizzato l'assistente "Add iOS Certificate".
           
	![][B108] 

    > [WACOM.NOTE] In questa esercitazione viene utilizzato un certificato di sviluppo. La stessa procedura viene utilizzata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.

5.  Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png)

1.  Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png)
 	Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download. ![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] Per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato **aps_development.cer**.

1.  Fare doppio clic sul certificato push scaricato **aps\_development.cer**.

	Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

	![][B10]

    > [WACOM.NOTE] Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Notification Services:**.

Questo certificato verrà utilizzato in un secondo momento per generare un file con estensione p12 e verrà caricato in Servizi mobili per abilitare l'autenticazione con APNS.

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

7.  In Xcode aprire l'Organizer e selezionare la visualizzazione Devices, selezionare **Provisioning Profiles** nella sezione **Library** nel riquadro a sinistra, quindi importare il profilo di provisioning appena creato.

8.  A sinistra, selezionare il dispositivo e importare di nuovo il profilo di provisioning.

9.  In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, digitare un nome per il certificato, selezionare il formato **.p12**, quindi fare clic su **Salva**.

	![][18]

    Annotare il nome del file e il percorso del certificato esportato.

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

1.  In XCode creare un nuovo progetto iOS e selezionare il modello **Single View Application**.

	![][31]

2.  In **Targets** fare clic sul nome del progetto, espandere **Code Signing Identity**, quindi in **Debug** selezionare il profilo di identità di firma del codice. Se si utilizza una versione più recente di XCode, cambiare l'impostazione di **Levels** da **Basic** a **All** e impostare la voce **Provisioning Profile** sul profilo di provisioning.

	![][32]

3.  Download di Azure Mobile SDK per iOS Aprire il file ZIP e trascinare la cartella WindowsAzureMessaging.framework nella cartella Framework nel progetto XCode. Selezionare **Copy items in destination group's folder**, quindi fare clic su **OK**.

	![][33]

4.  Nel file AppDelegate.h aggiungere la direttiva import seguente:

          #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  Nel file AppDelegate.m aggiungere il codice seguente nel metodo `didFinishLaunchingWithOptions`:

          [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  Nello stesso file aggiungere il metodo seguente:

         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
             SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                       @"<connection string>" notificationHubPath:@"mynh"];
                
             [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                 if (error != nil) {
                     NSLog(@"Error registering for notifications: %@", error);
                 }
            }];
         }

7.  *(Facoltativo)* Sempre nello stesso file, aggiungere il metodo seguente per visualizzare un avviso **UIAlert** se si riceve una notifica mentre l'app è attiva:

         - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
             NSLog(@"%@", userInfo);
             UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
             [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
             @"OK" otherButtonTitles:nil, nil];
             [alert show];
         }

8.  Eseguire l'app sul dispositivo.

Invio di notificheInvio di notifiche dal back-end
-------------------------------------------------

È possibile utilizzare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx). In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1.  Creare una nuova applicazione console in Visual C\#:

	![](./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png)

2.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

         Install-Package WindowsAzure.ServiceBus

	e premere INVIO.

3.  Aprire il file Program.cs e aggiungere l'istruzione using seguente:

         using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente. Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale, ad esempio **mynotificationhub2** nell'esempio precedente:

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{ private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);? }?quot;aps private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);? }?quot;:{ private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);? }?quot;alert private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);? }?quot;: private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);? }?quot;Hello from .NET! private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);? }?quot;}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }

5.  Aggiungere quindi la riga seguente nel metodo `Main`:

          SendNotificationAsync();
          Console.ReadLine();

6.  Premere F5 per eseguire l'app. Si dovrebbe ricevere un avviso sul dispositivo. Se si utilizza una connessione Wi-Fi, accertarsi che la connessione sia funzionante.

È possibile trovare tutti i payload disponibili nella [guida alla programmazione di notifiche push e locali](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1) di Apple.

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-ios), quindi eseguire le operazioni seguenti:

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
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);

6.  Fare clic su **Run Once** sulla barra inferiore. Si dovrebbe ricevere un avviso sul dispositivo.

Passaggi successivi
-------------------

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi iOS. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet), mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Per ulteriori informazioni sull'utilizzo di Hub di notifica, vedere la pagina relativa alle [linee guida per gli hub di notifica](http://msdn.microsoft.com/en-us/library/jj927170.aspx) e quella relativa alle [procedure di Hub di notifica per iOS](http://msdn.microsoft.com/en-us/library/jj927168.aspx).

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/en-us/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/en-us/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1