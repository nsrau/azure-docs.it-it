 <properties
	pageTitle="Aggiungere notifiche push all'app di Servizi mobili (Xamarin.Forms) - Servizi mobili"
	description="Informazioni su come usare le notifiche push in app per Xamarin.Forms con Servizi mobili di Azure."
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="03/18/2016"
	ms.author="wesmc"/>

# Aggiungere notifiche push all'app Xamarin.Forms

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Aggiungere notifiche push all'app Xamarin.Forms](../app-service-mobile/app-service-mobile-xamarin-forms-get-started-push.md).

##Panoramica

Questa esercitazione illustra come usare Servizi mobili di Azure per inviare notifiche push all'app per iOS, Android e Windows Phone della soluzione Xamarin.Forms. Creare prima di tutto un servizio mobile. Scaricare quindi un esempio introduttivo, effettuare la registrazione nei servizi di notifiche push appropriati e infine aggiungere codice alla soluzione per ricevere notifiche da tali servizi.

Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che un utente aggiunge un'attività in una delle app. L'esempio completo è disponibile nella pagina relativa all'[esempio completo di notifiche push di Azure per Xamarin.Forms].

Per completare questa esercitazione, è necessario disporre di:

+ Dispositivo iOS 8 (non è possibile testare le notifiche push nel simulatore iOS)
+ Iscrizione a iOS Developer Program
+ [Xamarin.iOS Studio]
+ [Componente Servizi mobili di Azure]
+ Un account Google attivo
+ [Componente client di Google Cloud Messaging] Questo componente verrà aggiunto durante l'esercitazione.

Contenuto dell'argomento:

1. [Creare un nuovo servizio mobile](#create-service)
2. [Scaricare e configurare il progetto introduttivo](#download-starter-sample)
4. [Aggiungere notifiche push all'app Xamarin.Forms.iOS](#iOS)
5. [Aggiungere notifiche push all'app Xamarin.Forms.Android](#Android)
6. [Aggiungere notifiche push all'app Xamarin.Forms.Windows](#Windows)
7. [Aggiornare lo script di inserimento tabelle di Azure per inviare notifiche push a tutte le app](#all-apps)

## <a name="create-service"> </a>Creare un nuovo servizio mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella.

1. Nel portale di Azure classico fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Fare clic sulla scheda **Dati** e quindi su **+Crea**.

    ![][123]

   	Verrà visualizzata la finestra di dialogo **Crea nuova tabella**.

3. In **Nome tabella** digitare _TodoItem_, quindi fare clic sul segno di spunta.

    ![][124]

  	Verrà creata una nuova tabella di archiviazione **TodoItem** con le autorizzazioni predefinite impostate. Questo significa che qualsiasi utente dell'app potrà accedere alla tabella e modificare i dati.

    > [AZURE.NOTE] Nella Guida introduttiva di Servizi mobili viene usato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili utilizzano lo stesso database.

4. Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

5. Fare clic sulla scheda **Colonne** e verificare che sia presente una sola colonna **id**, che viene creata automaticamente.

  	Questo è il requisito minimo per le tabelle in Servizi mobili.

    > [AZURE.NOTE] Se nel servizio mobile è abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.

È ora possibile usare il nuovo servizio mobile come archivio dati per l'app.

## <a name="download-starter-sample"></a>Scaricare e configurare il progetto introduttivo
Le notifiche push verranno aggiunte a un esempio esistente.

1. Scaricare l'[esempio introduttivo sulle notifiche push di Azure per Xamarin.Forms].

2. Nel [portale di Azure classico] fare clic su **Servizi mobili** e quindi sul sevizio mobile. Fare clic sulla scheda **Dashboard** e prendere nota del valore in **URL sito**. Fare clic su **Gestisci chiavi** e prendere nota del valore in **Chiave applicazione**. Questi valori saranno necessari per accedere al servizio mobile dal codice dell'app.

3. Nel progetto **ToDoAzure(Portable)** della soluzione aprire il file **Constants.cs**, sostituire `ApplicationURL` e `ApplicationKey` con l'URL del sito e la chiave dell'applicazione ottenuti nel passaggio precedente.

## <a name="iOS"></a>Aggiungere notifiche push all'app Xamarin.Forms.iOS

Le notifiche push verranno aggiunte all'app iOS tramite il servizio Apple Push Notification. Saranno necessari un account Google attivo e il [componente client di Google Cloud Messaging].

>[AZURE.IMPORTANT] Considerati i requisiti del servizio Apple Push Notification, è necessario distribuire e testare le notifiche push su un dispositivo con iOS (iPhone o iPad) anziché su un emulatore.

Il servizio APN usa i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APN, vedere [Apple Push Notification Service].

### <a name="certificates"></a>Generare il file della richiesta di firma del certificato

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene usato da Apple per la generazione di un certificato firmato.

1. Dalla cartella Utility eseguire lo **strumento Accesso Portachiavi**.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

    ![][5]

3. Immettere un valore in **Indirizzo e-mail utente**, digitare un valore in **Nome comune**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**.

    ![][6]

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

    ![][7]

    Ricordare il percorso scelto.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

### <a name="register"></a>Registrare l'app per le notifiche push

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire la registrazione per abilitare le notifiche push.

1. Se l'app non è ancora stata registrata, accedere al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning di iOS</a> in Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per creare un ID per l'app.

    ![][102]

2. Digitare un nome per l'app in **Description**, immettere un valore univoco per **Bundle Identifier** e ricordarlo, selezionare l'opzione "Push Notifications" nella sezione "App Services" e quindi fare clic su **Continue**. In questo esempio viene usato l'ID **MobileServices.Quickstart**, tuttavia non è possibile riutilizzare lo stesso ID, in quanto gli ID app devono essere univoci per tutti gli utenti. È pertanto consigliabile aggiungere il proprio nome completo o le proprie iniziali dopo il nome dell'app.

    ![][103]

    Verrà generato l'ID dell'app e all'utente verrà chiesto di **inviare** le informazioni. Fare clic su **Submit**.

    ![][104]

    Dopo aver fatto clic su **Submit** verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.

    ![][105]

3. Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

    ![][106]

    Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID dell'app. Fare clic sul pulsante **Settings**.

    ![][107]

4. Scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

    ![][108]

    Verrà visualizzato l'assistente "Add iOS Certificate".

    Nota: in questa esercitazione viene usato un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.

5. Fare clic su **Choose File**, selezionare il percorso in cui è stato salvato il file CSR in precedenza, quindi fare clic su **Generate**.

    ![][110]

6. Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

    ![][111]

    Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download.

    ![][9]

    Nota: per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato <strong>aps\_development.cer</strong>.

7. Fare doppio clic sul certificato push scaricato **aps\_development.cer**.

    Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

    ![][10]

    Nota: il nome del certificato può essere diverso, ma sarà preceduto dalla dicitura <strong>Apple Development iOS Push Notification Services:</strong>.

Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 e verrà caricato in Servizi mobili per abilitare l'autenticazione con il servizio APN.

### <a name="profile"></a>Creare un profilo di provisioning per l'app

1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisiong Profile**.

    ![][112]

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

3. Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**.

    ![][113]

4. Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**.

    ![][114]

5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**.

    ![][115]

6. Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**

    ![][116]

    Verrà creato un nuovo profilo di provisioning.

    ![][117]

7. In Xcode aprire Organizer e selezionare la visualizzazione Devices, selezionare **Provisioning Profiles** nella sezione **Library** nel riquadro a sinistra, quindi fare clic su **Refresh** nella parte inferiore del riquadro centrale.

### <a name="configure-mobileServices"></a>Configurare Servizi mobili per l'invio di richieste push

Dopo aver registrato l'app con il servizio APN e aver configurato il progetto, è necessario configurare il servizio mobile in modo che si integri con APN.

1. In Access Portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato, fare clic su **Esporta**, assegnare un nome al file, selezionare il formato **.p12**, quindi fare clic su **Salva**.

    ![][28]

    Annotare il nome del file e il percorso del certificato esportato.

2. Accedere al [portale di Azure classico], fare clic su **Servizi mobili** e quindi sull'app.

    ![][18]

3. Fare clic sulla scheda **Push** e quindi su **Upload** in **apple push notification settings**.

    ![][19]

    Verrà visualizzata la finestra di dialogo Upload Certificate.

4. Fare clic su **File**, selezionare il file con estensione p12 del certificato esportato, immettere un valore in **Password**, verificare che in **Mode** sia selezionata l'opzione corretta, fare clic sull'icona con il segno di spunta, quindi fare clic su **Save**.

    ![][20]

Il servizio mobile è ora configurato per funzionare con il servizio APN.

### <a name="configure-app"></a>Configurare l'applicazione per Xamarin.iOS

1. In Xamarin.Studio o Visual Studio aprire **Info.plist** e aggiornare il valore in **Bundle Identifier** con l'ID creato in precedenza.

    ![][121]

2. Scorrere verso il basso fino a **Background Modes** e selezionare le caselle **Enable Background Modes** e **Remote notifications**.

    ![][122]

3. Fare doppio clic sul progetto nel riquadro delle soluzioni per aprire **Project Options**.

4.  Scegliere **iOS Bundle Signing** in **Build** e selezionare i valori corrispondenti per **Identity** e **Provisioning profile** appena configurati per questo progetto.

    ![][120]

    Per effetto di questa operazione, il progetto Xamarin userà il nuovo profilo per la firma del codice. Per la documentazione ufficiale, vedere la pagina relativa al [provisioning del dispositivo Xamarin].

### <a name="add-push"></a>Aggiungere notifiche push all'app

1. In Xamarin.Studio o Visual Studio espandere il progetto **ToDoAzure.iOS**, aprire la classe **AppDelegate**, quindi sostituire l'evento **FinishedLaunching** con il codice seguente:

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();

            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. In **AppDelegate** eseguire l'override dell'evento **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient();

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };

            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("it-IT"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag);
        }

7. In **AppDelegate** eseguire l'override dell'evento **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

L'app è ora aggiornata per il supporto delle notifiche push.

### <a name="update-scripts"></a>Aggiornare lo script insert registrato nel portale di Azure classico

1. Nel portale di Azure classico fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

    ![][21]

2. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

    ![][22]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }

    Verrà registrato un nuovo script insert, che invia una notifica push al dispositivo specificato nella richiesta di inserimento.

   >[AZURE.NOTE] Questo script ritarda l'invio della notifica per dare all'utente il tempo di chiudere l'app per ricevere una notifica di tipo avviso popup.

### <a name="test"></a>Testare le notifiche push nell'app

1. Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi fare clic su **OK** per accettare le notifiche push.

   >[AZURE.NOTE] È necessario accettare le notifiche push in modo esplicito dall'app. Questa richiesta viene visualizzata solo la prima volta che si esegue l'app.

2. Nell'app fare clic sul pulsante **Aggiungi**, aggiungere un titolo di attività e quindi fare clic su **Salva**.

3. Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.


L'esercitazione è stata completata.

## <a name="Android"></a>Aggiungere notifiche push all'app Xamarin.Forms.Android

Le notifiche push verranno aggiunte all'app Android mediante il servizio Google Cloud Messaging (GCM). Saranno necessari un account Google attivo e il [componente client di Google Cloud Messaging].

###<a id="register"></a>Abilitare Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###<a id="configure"></a>Configurare il servizio mobile per l'invio di richieste push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>Aggiornare lo script insert registrato per l'invio di notifiche

>[AZURE.NOTE] Le procedure seguenti descrivono come aggiornare lo script registrato per l'operazione di inserimento nella tabella TodoItem del portale di Azure classico. È anche possibile accedere e modificare questo script del servizio mobile direttamente in Visual Studio, nel nodo Azure di Esplora server.

Nel [portale di Azure classico] fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

   ![][21]

2. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

   ![][22]

Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Verrà registrato un nuovo script insert, che invia una notifica push al dispositivo specificato nella richiesta di inserimento.

   >[AZURE.NOTE] Questo script ritarda l'invio della notifica per dare all'utente il tempo di chiudere l'app per ricevere una notifica di tipo avviso popup.


###<a id="configure-app"></a>Configurare il progetto esistente per le notifiche push

1. In Visualizzazione soluzione espandere la cartella **Componenti** nell'app Xamarin.Android e assicurarsi che il pacchetto Servizi mobili di Azure sia installato.

2. Fare clic con il pulsante destro del mouse sulla cartella **Componenti**, fare clic su **Altri componenti**, cercare il componente **Client di Google Cloud Messaging** e aggiungerlo al progetto.

1. Aprire il nuovo file di progetto MainActivity.cs e aggiungere l'istruzione using seguente alla classe:

		using Gcm.Client;


4.	Nella classe **MainActivity** aggiungere il codice seguente al metodo **OnCreate**, dopo la chiamata al metodo **LoadApplication**:

            try
            {
                // Check to ensure everything's setup right
                GcmClient.CheckDevice(this);
                GcmClient.CheckManifest(this);

                // Register for push notifications
                System.Diagnostics.Debug.WriteLine("Registering...");
                GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
            }
            catch (Java.Net.MalformedURLException)
            {
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

La classe **MainActivity** è ora pronta per l'aggiunta di notifiche push.

###<a id="add-push"></a>Aggiungere il codice delle notifiche push all'app

5. Nel progetto ToDoAzure.Droid creare una nuova classe denominata `GcmService`.

5. Aggiungere le istruzioni using seguenti alla classe **GcmService**:

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. Aggiungere le richieste di autorizzazione seguenti tra le istruzioni **using** e la dichiarazione **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. Nel file di progetto **GcmService.cs** aggiungere la classe seguente:

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {

            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	Nel codice precedente è necessario sostituire _`<PROJECT_NUMBER>`_ con il numero di progetto assegnato da Google quando è stato eseguito il provisioning dell'app nel portale per sviluppatori di Google.

8. Nel file di progetto GcmService.cs aggiungere il codice seguente che definisce la classe **GcmService**:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	Si noti che questa classe deriva da **GcmServiceBase** e che l'attributo **Service** deve essere applicato a questa classe.

	>[AZURE.NOTE]La classe **GcmServiceBase** implementa i metodi **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** e **OnError()**. È necessario eseguire l'override di questi metodi nella classe **GcmService**.

5. Aggiungere il codice seguente alla classe **GcmService** che sostituisce il gestore di eventi **OnRegistered**.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;

            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	Questo metodo usa l'ID di registrazione GCM restituito per la registrazione con Azure per l'invio di notifiche push.

10. Eseguire l'override del metodo **OnMessage** in **GcmService** con il codice seguente:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. Aggiungere il metodo seguente per eseguire l'override di **OnUnRegistered()** e **OnError()**, che sono necessari per la compilazione del progetto.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("GcmService", "Unregistered RegisterationId : " + registrationId);
		}

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>Testare le notifiche push nell'app

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure usando un dispositivo virtuale nell'emulatore.

Quando si esegue l'app nell'emulatore, assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

> [AZURE.IMPORTANT] Per ricevere notifiche push è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo **Settings** e quindi **Add Account**. Assicurarsi inoltre che l'emulatore sia connesso a Internet.

1. In **Tools** fare clic su **Open Android Emulator Manager**, selezionare il dispositivo e quindi fare clic su **Edit**.

    ![][125]

2. Selezionare **Google APIs** in **Target** e quindi fare clic su **OK**.

    ![][126]

3. Fare clic su **Run** sulla barra degli strumenti nella parte superiore della schermata e selezionare l'app. In questo modo l'emulatore viene avviato e l'app eseguita.

  L'app recupera la proprietà *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

1. Aggiungere una nuova attività nell'app.

2. Scorrere verso il basso dalla parte superiore della schermata per aprire il Notification Center del dispositivo e visualizzare la notifica.

	![][127]

## <a name="Windows"></a>Aggiungere notifiche push all'app Xamarin.Forms.Windows

Questa sezione illustra come usare Servizi mobili di Azure per inviare notifiche push all'app per Windows Phone Silverlight inclusa nella soluzione your Xamarin.Forms.

###<a id="update-app"></a> Aggiornare l'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1. In Visual Studio aprire il file App.xaml.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.Phone.Notification;

3. Aggiungere il codice seguente al file App.xaml.cs:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    Questo codice consente di recuperare il valore di ChannelURI per l'app dal Servizio notifica Push Microsoft (MPNS) usato da Windows Phone 8.x "Silverlight", quindi di registrarlo per le notifiche push.

	>[AZURE.NOTE]In questa esercitazione il servizio mobile invia una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di tipo riquadro, è invece necessario chiamare il metodo **BindToShellTile** sul canale.

4. All'inizio del gestore dell'evento **Application\_Launching** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **AcquirePushChannel**:

        AcquirePushChannel();

	In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.

5. Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

6.	In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità **ID\_CAP\_PUSH\_NOTIFICATION** sia selezionata.

   	![Abilitazione delle notifiche in Visual Studio](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup.

###<a id="update-scripts"></a> Aggiornare gli script del server per l'invio di notifiche push

È infine necessario aggiornare lo script registrato nell'operazione di inserimento nella tabella TodoItem per l'invio di notifiche.

1. Nel [portale di Azure classico] fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

    ![][21]

2. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

    ![][22]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3. Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Verrà registrato un nuovo script insert, che invia una notifica push al dispositivo specificato nella richiesta di inserimento.

3. Fare clic sulla scheda **Push**, selezionare **Abilita notifiche push non autenticate** e quindi fare clic su **Salva**.

	In questo modo il servizio mobile verrà abilitato per la connessione al Servizio di notifica Push di Microsof in modalità senza autenticazione per l'invio di notifiche push.

	>[AZURE.NOTE]In questa esercitazione verrà usato il Servizio notifica Push Microsoft in modalità senza autenticazione. In tale modalità, il Servizio notifica Push Microsoft limita il numero di notifiche che è possibile inviare a un canale di dispositivo. Per rimuovere questa restrizione è necessario generare e caricare un certificato facendo clic su **Upload** e selezionando il certificato. Per altre informazioni sulla generazione del certificato, vedere [Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone].

###<a id="test"></a> Testare le notifiche push nell'app

1. In Visual Studio premere F5 per eseguire l'app.

    >[AZURE.NOTE] Durante il test sull'emulatore di Windows Phone è possibile che venga generata un'eccezione RegistrationAuthorizationException e che venga restituita una risposta 401 (Non autorizzato). Questa situazione può verificarsi durante la chiamata a `RegisterNativeAsync()` a causa della modalità errata con cui l'orologio dell'emulatore di Windows Phone viene sincronizzato con il PC host. Di conseguenza, è possibile che un token di sicurezza venga rifiutato. Per risolvere questo problema, è sufficiente impostare manualmente l'orologio prima del test.

5. Nell'app creare una nuova attività con titolo **Hello push**, quindi fare immediatamente clic sul pulsante start o back per uscire dall'app.

  	Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto. Si noti che il dispositivo riceve una notifica di tipo avviso popup con il testo **hello push**.

	![Ricezione di una notifica di avviso popup](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]La notifica non viene ricevuta se l'app è ancora aperta. Per ricevere una notifica di tipo avviso popup mentre l'app è attiva, è necessario gestire l'evento [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[provisioning del dispositivo Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[portale di Azure classico]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Componente Servizi mobili di Azure]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Componente client di Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[esempio introduttivo sulle notifiche push di Azure per Xamarin.Forms]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[esempio completo di notifiche push di Azure per Xamarin.Forms]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms

<!---HONumber=AcomDC_0323_2016-->