<properties urlDisplayName="Get Started with Push Notifications" pageTitle="Introduzione alle notifiche push (Xamarin.iOS) - Servizi mobili" metaKeywords="" description="Informazioni su come usare le notifiche push in app per Xamarin.iOS con Servizi mobili di Azure." metaCanonical="" disqusComments="0" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="yuaxu" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="Java" ms.topic="article" ms.date="10/20/2014" ms.author="yuaxu" />

# Aggiungere notifiche push all'app di Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push-xamarin](../includes/mobile-services-selector-get-started-push-xamarin.md)]

<p>Questo argomento illustra come usare Servizi mobili di Azure per inviare notifiche push a un'app per Xamarin.iOS 8. In questa esercitazione si userà il servizio di notifiche push di Apple (Apple Push Notification Service, APNS) per aggiungere notifiche push al progetto [Introduzione a Servizi mobili]. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.</p>

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Generare la richiesta di firma del certificato] 
2. [Registrare l'app e abilitare le notifiche push]
3. [Creare un profilo di provisioning per l'app]
4. [Configurare Servizi mobili]
5. [Configurare l'app di Xamarin.iOS]
6. [Aggiungere notifiche push all'app]
7. [Aggiornare gli script per l'invio di notifiche push]
8. [Inserire dati per la ricezione di notifiche]

Per completare questa esercitazione, è necessario disporre di:

+ Dispositivo iOS 8
+ Iscrizione a iOS Developer Program
+ [Xamarin.iOS Studio]
+ [Componente Servizi mobili di Azure]

   <div class="dev-callout"><b>Nota</b>
   <p>Considerati i requisiti di configurazione delle notifiche push, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.</p>
   </div>

Il servizio APNS usa i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service].

## <a name="certificates"></a>Generare la richiesta di firma del certificato

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene usato da Apple per la generazione di un certificato firmato.

1. Dalla cartella Utility eseguire lo **strumento Accesso Portachiavi**.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

    ![][5]

3. Immettere l'**Indirizzo e-mail utente**, digitare un valore per **Nome comune**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

    ![][6]

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

    ![][7]
  
    Ricordare il percorso scelto.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

## <a name="register"></a>Registrare l'app per le notifiche push

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire la registrazione per abilitare le notifiche push. 

1. Se l'app non è ancora stata registrata, accedere al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per creare un ID per l'app.
    
    ![][102]

2. Digitare un nome per l'app in **Description**, immettere un valore univoco per **Bundle Identifier** e ricordarlo, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continua**. In questo esempio viene usato l'ID **MobileServices.Quickstart**, tuttavia non è possibile riutilizzare lo stesso ID, in quanto gli ID app devono essere univoci per tutti gli utenti. È pertanto consigliabile aggiungere il proprio nome completo o le proprie iniziali dopo il nome dell'app. 

    ![][103]
   
    Verrà generato l'ID dell'app e all'utente verrà chiesto di **inviare** le informazioni. Fare clic su **Submit**.
   
    ![][104] 
   
    Dopo aver fatto clic su **Submit**, verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.
   
    ![][105]    

3. Individuare l'ID dell'app appena creato e fare clic sulla relativa riga. 

    ![][106]
   
    Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID. Fare clic sul pulsante **Settings**.
   
    ![][107] 
   
4. Scorrere fino alla fine della schermata e fare clic su **Create Certificate** nella sezione **Development Push SSL Certificate**.

    ![][108] 

    Verrà visualizzato l'assistente "Add iOS Certificate".
   
Nota: In questa esercitazione viene usato un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.

5. Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR in precedenza, quindi fare clic su **Generate**. 

    ![][110]
  
6. Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.
 
    ![][111]  

    Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download. 

    ![][9] 

Nota: Per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato <strong>aps_development.cer</strong>.

7. Fare doppio clic sul certificato push scaricato **aps_development.cer**.

    Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

    ![][10]

Nota: Il nome del certificato potrebbe essere diverso, ma verrà preceduto da <strong>Apple Development iOS Push Notification Services:</strong>.

Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 e verrà caricato in Servizi mobili per abilitare l'autenticazione con APNS.

## <a name="profile"></a>Creare un profilo di provisioning per l'app
 
1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisiong Profile**.

    ![][112]

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

3. Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**.

    ![][113]

4. Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**.

    ![][114]

5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**.
  
    ![][115]

6. Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**.

    ![][116]

    Verrà creato un nuovo profilo di provisioning.

    ![][117]

## <a name="configure-mobileServices"></a>Configurare Servizi mobili per l'invio di richieste push

Dopo aver registrato l'app con APNS e aver configurato il progetto, è necessario configurare il servizio mobile in modo che si integri con APNS.

1. In Accesso Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, scegliere **Esporta**, assegnare un nome al file, selezionare il formato **.p12**, quindi fare clic su **Salva**.

    ![][28]

    Annotare il nome del file e il percorso del certificato esportato.

2. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

    ![][18]

3. Fare clic sulla scheda **Push** e quindi su **Carica** in **Impostazioni di notifica push di Apple**.

    ![][19]

    Verrà visualizzata la finestra di dialogo Upload Certificate.

4. Fare clic su **File**, selezionare il file con estensione p12 del certificato esportato, immettere la password in **Password**, verificare che in **Modalità** sia selezionata la modalità corretta, fare clic sull'icona del segno di spunta e quindi su **Salva**.

    ![][20] 

Il servizio mobile è ora configurato per funzionare con APNS.

## <a name="configure-app"></a>Configurare l'applicazione di Xamarin.iOS

1. In Xamarin.Studio aprire **Info.plist** e aggiornare il valore in **Bundle Identifier** con l'ID creato in precedenza.

    ![][121]

2. Scorrere verso il basso fino a **Background Modes** e selezionare la casella **Enable Background Modes** e la casella **Remote notifications**. 

    ![][122]

3. Fare doppio clic sul progetto nel riquadro delle soluzioni per aprire le **opzioni del progetto**.

4.  Scegliere **iOS Bundle Signing** in **Build** e selezionare i valori corrispondenti per **Identity** e **Provisioning profile** appena configurati per questo progetto. 

    ![][120]

    Per effetto di questa operazione, il progetto Xamarin userà il nuovo profilo per la firma del codice. Per la documentazione ufficiale, vedere la pagina relativa al [provisioning del dispositivo Xamarin].

## <a name="add-push"></a>Aggiungere notifiche push all'app

1. In Xamarin.Studio aprire il file AppDelegate.cs e aggiungere la seguente proprietà:

        public string DeviceToken { get; set; }

2. Aprire la classe **TodoItem** e aggiungere la proprietà seguente:

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. In **QSTodoService** eseguire l'override della dichiarazione client esistente come segue:
        
        public MobileServiceClient client { get; private set; }

4. Aggiungere quindi il metodo seguente in modo da consentire ad **AppDelegate** di acquisire successivamente il client per la registrazione delle notifiche push:

        public MobileServiceClient GetClient {
            get{ 
                return client;
            }
        }

5. In **AppDelegate** eseguire l'override dell'evento **FinishedLaunching**: 

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

6. In **AppDelegate** eseguire l'override dell'evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. In **AppDelegate** eseguire l'override dell'evento **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. In **TodoListViewController** modificare l'azione **OnAdd** in modo da ottenere il token del dispositivo archiviato in **AppDelegeate** e archiviarlo nell'elemento **TodoItem** aggiunto.

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem() 
        {
            Text = itemText.Text, 
            Complete = false,
            DeviceToken = deviceToken
        };
L'app è ora aggiornata per il supporto delle notifiche push.

## <a name="update-scripts"></a>Aggiornare lo script insert registrato nel portale di gestione

1. Nel portale di gestione fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**. 

    ![][21]

2. In **todoitem** fare clic sulla scheda **Script** e selezionare **Inserisci**.
   
    ![][22]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Salva**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Verrà registrato un nuovo script insert, che usa l'[oggetto apns] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert. 

    <div class="dev-callout"><b>Nota</b>
   <p>Questo script ritarda l'invio della notifica per dare all'utente il tempo di chiudere l'app per ricevere una notifica di tipo avviso popup.</p>
   </div> 

## <a name="test"></a>Testare le notifiche push nell'app

1. Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

    ![][23]

    <div class="dev-callout"><b>Nota</b>
    <p>È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.</p>
    </div>

2. Digitare testo significativo nell'app, ad esempio _Nuova attività di Servizi mobili_, quindi fare clic sull'icona con il segno più (**+**).

    ![][24]

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

    ![][25]

4. Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente avviso popup.

    ![][26]

L'esercitazione è stata completata.

<!-- Anchors. -->
[Generare la richiesta di firma del certificato]: #certificates
[Registrare l'app e abilitare le notifiche push]: #register
[Creare un profilo di provisioning per l'app]: #profile
[Configurare Servizi mobili]: #configure-mobileServices
[Configurare l'app di Xamarin.iOS]: #configure-app
[Aggiornare gli script per l'invio di notifiche push]: #update-scripts
[Aggiungere notifiche push all'app]: #add-push
[Inserire dati per la ricezione di notifiche]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Installare Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portale di provisioning di iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services SDK per iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introduzione alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Inviare notifiche push agli utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Provisioning del dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[progetto di esempio completato]: http://go.microsoft.com/fwlink/p/?LinkId=331303
