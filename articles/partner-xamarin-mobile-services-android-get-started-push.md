<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.Android apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services" documentationCenter="Mobile" authors="" />

Introduzione alle notifiche push in Servizi mobili
==================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per Xamarin.Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite il servizio Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push](#register)
2.  [Configurazione di Servizi mobili](#configure)
3.  [Aggiunta di notifiche push all'app](#add-push)
4.  [Aggiornamento degli script per l'invio di notifiche push](#update-scripts)
5.  [Inserimento di dati per la ricezione di notifiche](#test)

Per completare questa esercitazione, è necessario disporre di:

-   Un account Google attivo

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-android).

Registrazione dell'appRegistrazione dell'app per le notifiche push
------------------------------------------------------------------

**Nota**

Per completare la procedura descritta in questo argomento, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Passare al sito Web [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303), eseguire l'accesso con le credenziali dell'account Google e quindi fare clic su **Create project...**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png)

    > [WACOM.NOTE] Se si dispone già di un progetto esistente, dopo l'accesso verrà visualizzata la pagina **Dashboard**. Per creare un nuovo progetto dalla pagina Dashboard, espandere **API Project**, fare clic su **Create** in **Other projects**, quindi immettere un nome di progetto e fare clic su **Create project**.

2.  Fare clic sul pulsante Overview nella colonna di sinistra e prendere nota del numero di progetto nella sezione Dashboard.

    Più avanti nell'esercitazione si imposterà questo valore come variabile **PROJECT\_ID** nel client.

3.  Nella pagina [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) fare clic su **Services**, quindi fare clic sull'interruttore per attivare **Google Cloud Messaging for Android** e accettare le condizioni per l'utilizzo del servizio.

4.  Fare clic su **API Access** e quindi su **Create new Server key**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png)

5.  In **Configure Server Key for API Project** fare clic su **Create**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png)

6.  Prendere nota del valore **API key**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png)

Questo valore della chiave dell'API verrà utilizzato successivamente per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

## Configurazione del servizioConfigurazione di Servizi mobili per l'invio di richieste push

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png)

2.  Fare clic sulla scheda **Push**, immettere il valore **API Key** ottenuto da GCM nella procedura precedente, quindi fare clic su **Save**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png)

    Il servizio mobile è ora configurato per l'utilizzo con GCM per l'invio di notifiche push.

## Aggiunta di notifiche pushAggiunta di notifiche push all'app

1.  In primo luogo si aggiungerà **PushSharp** al progetto come riferimento. A questo scopo è necessario compilare l'ultima versione di PushSharp e aggiungere la DLL compilata come riferimento al progetto Xamarin.Android.

2.  Visitare la [pagina Github di PushSharp](https://github.com/Redth/PushSharp) e scaricare l'ultima versione. Dopo aver estratto la raccolta di file, passare alla cartella del progetto di esempio seguente:

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. e aprire il file del progetto:

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  Compilare l'esempio di client PushSharp MonoForAndroid in modalità **Release**.

4.  Creare una cartella **\_external** nella cartella del progetto Xamarin.Android.

5.  Copiare il file seguente dall'esempio di client PushSharp MonoForAndroid alla nuova cartella **\_external** nella cartella del progetto Xamarin.Android:

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Aprire il progetto Xamarin.Android in Xamarin Studio (o Visual Studio).

7.  Fare clic con il pulsante destro del mouse sulla cartella **References** del progetto e scegliere **Edit References**.

8.  Passare alla scheda **.Net Assembly**, individuare la cartella **\_external** del progetto, selezionare il file **PushSharp.Client.MonoForAndroid.dll** compilato in precedenza e fare clic su **Add**. Fare clic su OK per chiudere la finestra di dialogo.

9.  Aprire **Constants.cs** e aggiungere la riga seguente, sostituendo **PROJECT\_ID** con il valore Project\_ID di Google annotato in precedenza:

         public const string SenderID = "PROJECT_ID"; // Google API Project Number

10. Copiare il file **PushService.cs** dall'esempio di client PushSharp MonoForAndroid alla cartella del progetto Xamarin.Android e aggiungerlo al progetto.

11. Cambiare lo spazio dei nomi utilizzato in **PushService.cs** in modo che corrisponda a quello del progetto, ad esempio XamarinTodoQuickStart.

12. Impostare la matrice **SENDER\_IDS** in **PushService.cs** in modo che faccia riferimento alla costante **SenderID** creata in precedenza:

        public static string[] SENDER_IDS = new string[] { Constants.SenderID };

13. Aggiungere una nuova proprietà statica a **PushHandlerService** in **PushService.cs** per tenere traccia dell'ID di registrazione del dispositivo:

        public static string RegistrationID { get; private set; }

14. Aggiornare il metodo **OnRegistered** in **PushService.cs** in modo che archivi l'ID di registrazione ricevuto nella variabile statica locale:

        protected override void OnRegistered(Context context, string registrationId)
            {
                Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
            }

15. Aggiornare il metodo **OnMessage** in **PushService.cs** in modo da visualizzare il messaggio push ricevuto nell'ambito della notifica (sostituire la chiamata esistente a **createNotification**):

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item: " + message);

16. Si noti che nel metodo **OnMessage** è incluso per impostazione predefinita il codice seguente per archiviare l'ultimo messaggio push ricevuto:

        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. Aggiornare il metodo **createNotification** in **PushService.cs** in modo che faccia riferimento a **TodoActivity** anziché a **DefaultActivity**.

18. Aprire il file **TodoActivity.cs** e aggiungere l'istruzione using seguente:

        using PushSharp.Client;

19. In **TodoActivity.cs** inserire le righe seguenti nel punto immediatamente precedente a quando viene creato **MobileServiceClient**:

        // Check to ensure everything's setup right
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Register for push notifications
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. Aprire **TodoItem.cs** e aggiungere un nuovo campo per tenere traccia dell'ID del dispositivo registrato per la persona che ha aggiunto l'elemento TodoItem:

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. In **TodoActivity.cs** aggiornare il metodo **AddItem** per impostare il valore di **RegistrationID** dell'elemento **TodoItem** appena aggiunto in base all'ID di registrazione del dispositivo ricevuto durante la registrazione:

        // Create a new item
        var item = new TodoItem() {
            Text = textNewTodo.Text,
            Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
            };

L'app è ora aggiornata per il supporto delle notifiche push.

Aggiornamento dello script insertAggiornamento dello script insert registrato nel portale di gestione
-----------------------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

	![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png)

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

1.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Write to the response and then send the notification in the background
                     request.respond();
                     push.gcm.send(item.channel, item.text, {
                         success: function(response) {
                             console.log('Push notification sent: ', response);
                         }, error: function(error) {
                             console.log('Error sending push notification: ', error);
                         }
                     });
                 }
             });
         }

    Verrà registrato un nuovo script insert, che utilizza l'[oggetto gcm](http://go.microsoft.com/fwlink/p/?LinkId=282645) per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert.

Test dell'appEsecuzione del test delle notifiche push nell'app
--------------------------------------------------------------

1.  Eseguire l'app e aggiungere un nuovo elemento Todo. Verificare che sia possibile ricevere una notifica push sul nuovo elemento Todo aggiunto.

2.  Esaminare la scheda **Logs** dell'app per dispositivi mobili nel portale di gestione di Azure per visualizzare i messaggi di log aggiunti al metodo **Insert** nella tabella **TodoItem** in precedenza.

3.  Osservare la tabella **TodoItem** nel portale di gestione di Azure per visualizzare la nuova colonna **channel** aggiunta che contiene gli identificatori di registrazione univoci dei dispositivi.

L'esercitazione è stata completata.

Download dell'esempio completato
--------------------------------

Scaricare il [progetto di esempio completato](http://go.microsoft.com/fwlink/p/?LinkId=331303). Assicurarsi di aggiornare le variabili **ApplicationURL**, **ApplicationKey** e **SenderID** con le proprie impostazioni di Azure.

Passaggi successivi
-------------------

In questo semplice esempio un utente riceve una notifica push con i dati appena inseriti. Nell'esercitazione successiva, [Notifiche push per utenti di app](/en-us/develop/mobile/tutorials/push-notifications-to-users-android), sarà possibile creare una tabella Devices distinta in cui archiviare i token di dispositivo e sarà possibile inviare una notifica push a tutti i canali archiviati quando si verifica un inserimento.

