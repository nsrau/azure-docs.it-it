<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento descrive come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione iOS.
In questa esercitazione verrà creata un'app per Xamarin.iOS vuota che riceve notifiche push tramite il servizio di notifica push di Apple (APN, Apple Push Notification). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica. Il codice compilato è disponibile nell'esempio di [app NotificationHubs][app NotificationHubs].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Generazione della richiesta di firma del certificato][Generazione della richiesta di firma del certificato]
2.  [Registrazione dell'app e abilitazione delle notifiche push][Registrazione dell'app e abilitazione delle notifiche push]
3.  [Creazione di un profilo di provisioning per l'app][Creazione di un profilo di provisioning per l'app]
4.  [Configurazione dell'hub di notifica][Configurazione dell'hub di notifica]
5.  [Connessione dell'app all'hub di notifica][Connessione dell'app all'hub di notifica]
6.  [Invio di notifiche dal back-end][Invio di notifiche dal back-end]

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Per completare questa esercitazione, è necessario disporre dei prerequisiti seguenti:

-   [XCode 5.0][XCode 5.0]
-   Dispositivo con iOS 5.0 o versione successiva
-   Iscrizione a iOS Developer Program
-   [Xamarin.iOS][1]
-   [Componente Servizi mobili di Azure][Componente Servizi mobili di Azure]

   <div class="dev-callout"><b>Nota</b>
   <p>Considerati i requisiti di configurazione delle notifiche push, &egrave; necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anzich&eacute; su un simulatore.</p>
   </div>

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Xamarin.iOS.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario disporre di un account Azure attivo. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

Il servizio APNS utilizza i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service][Apple Push Notification Service].

## <a name="certificates"></a><span class="short-header">Generazione del file della richiesta di firma del certificato</span>Generazione del file della richiesta di firma del certificato

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene usato da Apple per la generazione di un certificato firmato.

1.  Dalla cartella Utility eseguire lo strumento Accesso Portachiavi.

2.  Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione...**.

    ![][]

3.  Fare clic su **Indirizzo e-mail utente**, digitare i valori di **Nome comune** e **Indirizzo e-mail CA**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

    ![][2]

4.  Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

    ![][3]

    Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

Successivamente, registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

## <a name="register"></a><span class="short-header">Registrazione dell'app</span>Registrazione dell'app per le notifiche push

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire un'ulteriore registrazione per abilitare le notifiche push.

1.  Se l'app non è ancora stata registrata, accedere al [portale di provisioning iOS][portale di provisioning iOS] su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per registrare una nuova app.

    ![][4]

2.  Digitare un nome per l'app in **Description**, inserire un valore per **Bundle Identifier**, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continue**.

    ![][5]

    ![][6]

    ![][7]

    Verrà generato l'ID dell'app e verrà chiesto di inviare le informazioni. Fare clic su **Submit**.

    ![][8]

    Dopo aver fatto clic su **Submit** verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.

    ![][9]

    <div class="dev-callout"><b>Nota</b>
<p>Se si sceglie di specificare un valore per <strong>Bundle Identifier</strong> diverso da <b>MobileServices.Quickstart</b>, &egrave; necessario aggiornare anche il valore dell'identificatore del bundle nel progetto Xcode.</p>
 </div>

3.  Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

    ![][10]

    Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID:

    ![][11]

    ![][12]

4.  Fare clic su **Edit**, scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

    ![][13]

    Verrà visualizzato l'assistente "Add iOS Certificate".

    ![][14]

    <div class="dev-callout"><b>Nota</b>
<p>In questa esercitazione viene usato un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.</p>
</div>

5.  Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

    ![][15]

6.  Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

    ![][16]

    ![][17]

    Il certificato di firma verrà scaricato e salvato nel computer nella cartella **Download**.

    ![][18]

    <div class="dev-callout"><b>Nota</b>
<p>Per impostazione predefinita, il file scaricato di un certificato di sviluppo &egrave; denominato <strong>aps_development.cer</strong>.</p>
</div>

7.  Fare doppio clic sul certificato push scaricato **aps\_development.cer**.

    Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

    ![][19]

    > [WACOM.NOTE]
    > Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Notification Services:**.

    Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 e caricarlo nell'hub di notifica per abilitare le notifiche push tramite APNS.

## <a name="profile"></a><span class="short-header">Provisioning dell'app</span>Creazione di un profilo di provisioning per l'app

1.  Nel [portale di provisioning iOS][portale di provisioning iOS] selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisioning Profile**.

    ![][20]

2.  Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

    ![][21]

3.  Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**.

    ![][22]

4.  Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**.

    ![][23]

5.  In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**.

    ![][24]

6.  Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**.

    ![][25]

    ![][26]

    Verrà creato un nuovo profilo di provisioning.

7.  In Xcode aprire l'Organizer e selezionare la visualizzazione Devices, selezionare **Provisioning Profiles** nella sezione **Library** nel riquadro a sinistra, quindi importare il profilo di provisioning appena creato.

8.  A sinistra, selezionare il dispositivo e importare di nuovo il profilo di provisioning.

9.  In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, digitare un nome per il certificato, selezionare il formato **.p12**, quindi fare clic su **Salva**.

    ![][27]

    Annotare il nome del file e il percorso del certificato esportato.

Per effetto di questa operazione, il progetto Xcode utilizzerà il nuovo profilo per la firma del codice. A questo punto è necessario caricare il certificato nell'hub di notifica.

## <a name="configure-hub"></a><span class="short-header">Configurazione dell'hub di notifica</span>Configurazione dell'hub di notifica

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

2.  Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

    ![][28]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

    ![][29]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configure** nella parte superiore.

    ![][30]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

    ![][31]

6.  Selezionare la scheda **Configure** nella parte superiore e quindi fare clic su **Upload** per le impostazioni di notifica di Apple. Selezionare quindi il certificato **.p12** esportato in precedenza e la password per il certificato. Selezionare se si desidera usare il servizio push **Production** (per inviare notifiche push agli utenti che hanno acquistato l'app dallo Store) o **Sandbox** (durante lo sviluppo).

    ![][32]

7.  Fare clic sulla scheda **Dashboard** nella parte superiore e quindi su **Connection Information**. Prendere nota delle due stringhe di connessione.

    ![][33]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Apple e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

## <a name="connecting-app"></a><span class="short-header">Connessione dell'app</span>Connessione dell'app all'hub di notifica

### Scaricare la libreria WindowsAzure.Messaging

Questo assembly offre un modo semplice per effettuare la registrazione con Hub di notifica di Azure. È disponibile per il download tramite le istruzioni seguenti oppure nel [download di esempio][app NotificationHubs].

1.  Scaricare l'origine per [WindowsAzure.Messaging][WindowsAzure.Messaging] da GitHub.

2.  Compilare il progetto e individuare l'assembly di output **WindowsAzure.Messaging.dll**, che sarà necessario durante la configurazione dell'applicazione Xamarin.iOS, più avanti.

### Creare un nuovo progetto

1.  In Xamarin Studio creare un nuovo progetto iOS e selezionare il modello **Single View Application**.

    ![][34]

2.  È necessario aggiungere prima di tutto un riferimento al componente Servizi mobili di Azure. Nella visualizzazione Solution fare clic con il pulsante destro del mouse sulla cartella **Components** del progetto e scegliere **Get More Components**. Cercare il componente **Servizi mobili di Azure** e aggiungerlo al progetto.

3.  Aggiungere quindi un riferimento alla libreria WindowsAzure.Messaging scaricata in precedenza. Fare clic con il pulsante destro del mouse sulla cartella **References...** del progetto e scegliere **Edit References**. Nella scheda **.Net Assembly** individuare **WindowsAzure.Messaging.dll** e

4.  In **AppDelegate.cs** aggiungere le istruzioni using seguenti:

        using Microsoft.WindowsAzure.MobileServices;

    using WindowsAzure.Messaging;

5.  Dichiarare un'istanza di **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

6.  Creare una classe **Constants.cs** con le variabili seguenti:

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";

7.  In **AppDelegate.cs** aggiornare **FinishedLaunching()** in base all'esempio seguente:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8.  In **AppDelegate.cs** eseguire l'override dell'evento **RegisteredForRemoteNotifications()**:

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

9.  In **AppDelegate.cs** eseguire l'override dell'evento **ReceivedRemoteNotification()**:

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
<p>&Egrave; possibile scegliere di eseguire l'override di **FailedToRegisterForRemoteNotifications()** per gestire situazioni quali l'assenza di una connessione di rete e cos&igrave; via.</p>
</div>

11. Eseguire l'app sul dispositivo.

## <a name="send"></a><span class="short-header">Invio di notifiche</span>Invio di notifiche dal back-end

È possibile usare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST][interfaccia REST]. In questa esercitazione verranno inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1.  Creare una nuova applicazione console in Visual C#:

    ![][35]

2.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus][pacchetto NuGet WindowsAzure.ServiceBus]. Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

        Install-Package WindowsAzure.ServiceBus and press Enter.

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

6.  Premere F5 per eseguire l'app. Si dovrebbe ricevere un avviso sul dispositivo. Se si usa una connessione Wi-Fi, accertarsi che la connessione sia funzionante.

È possibile trovare tutti i payload disponibili nella [guida alla programmazione di notifiche push e locali][guida alla programmazione di notifiche push e locali] di Apple.

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili][Introduzione a Servizi mobili], quindi eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] e selezionare il servizio mobile.

2.  Selezionare la scheda **Scheduler** nella parte superiore.

    ![][36]

3.  Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **On demand**.

    ![][37]

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

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi iOS. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti][Utilizzo di Hub di notifica per inviare notifiche push agli utenti], mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie][Utilizzo di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'utilizzo di Hub di notifica, vedere la pagina relativa alle [linee guida per gli hub di notifica][linee guida per gli hub di notifica] e quella relativa alle [procedure di Hub di notifica per iOS][procedure di Hub di notifica per iOS].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Universal]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /it-it/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /it-it/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /it-it/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /it-it/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [app NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Generazione della richiesta di firma del certificato]: #certificates
  [Registrazione dell'app e abilitazione delle notifiche push]: #register
  [Creazione di un profilo di provisioning per l'app]: #profile
  [Configurazione dell'hub di notifica]: #configure-hub
  [Connessione dell'app all'hub di notifica]: #connecting-app
  [Invio di notifiche dal back-end]: #send
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [1]: http://xamarin.com/download
  [Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F
  [Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
  [2]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
  [3]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png
  [portale di provisioning iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [4]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
  [5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
  [6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
  [7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
  [8]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
  [9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
  [10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
  [11]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
  [12]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
  [13]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
  [14]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
  [15]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png
  [16]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
  [17]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png
  [18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
  [19]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
  [20]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
  [21]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
  [22]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
  [23]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
  [24]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
  [25]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
  [26]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png
  [27]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [30]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
  [34]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [interfaccia REST]: http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx
  [35]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
  [pacchetto NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [guida alla programmazione di notifiche push e locali]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios
  [36]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
  [37]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
  [Utilizzo di Hub di notifica per inviare notifiche push agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
  [Utilizzo di Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet
  [linee guida per gli hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
  [procedure di Hub di notifica per iOS]: http://msdn.microsoft.com/it-it/library/jj927168.aspx
