<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Windows Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" writer="ricksal" manager="" editor="" />

Introduzione alle notifiche push in Servizi mobili
==================================================

[Windows Store C\#](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#") [Windows Store JavaScript](/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript") [Windows Phone](/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone") [iOS](/it-it/documentation/articles/mobile-services-ios-get-started-push "iOS") [Android](/it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android")

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

[WACOM.NOTE]In questa esercitazione viene illustrata l'integrazione di Servizi mobili con Hub di notifica, attualmente in versione di anteprima. Per impostazione predefinita, l'invio di notifiche push mediante Hub di notifica non è abilitata da un back-end JavaScript. Dopo aver creato il nuovo hub di notifica, non è possibile annullare il processo di integrazione. Attualmente le notifiche push per iOS sono disponibili solo utilizzando il supporto push predefinito descritto in [questa versione dell'argomento](/it-it/documentation/articles/mobile-services-android-get-started-push/).

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Abilitazione di Google Cloud Messaging](#register)
2.  [Configurazione di Servizi mobili](#configure)
3.  [Aggiunta di notifiche push all'app](#add-push)
4.  [Aggiornamento degli script per l'invio di notifiche push](#update-scripts)
5.  [Inserimento di dati per la ricezione di notifiche](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started/) o [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-js/) per collegare il progetto al servizio mobile.

Abilitazione di Google Cloud Messaging
--------------------------------------

[WACOM.INCLUDE [Abilitazione di GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Questo valore della chiave dell'API verrà utilizzato successivamente per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

Configurazione di Servizi mobili per l'invio di richieste push
--------------------------------------------------------------

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

    ![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2.  Fare clic sulla scheda **Push**, quindi su **Enable enhanced push** e su **Yes** per accettare la modifica della configurazione.

    ![](./media/mobile-services-android-get-started-push/mobile-enable-enhanced-push.png)

    ![](../includes/media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    Verrà aggiornata la configurazione del servizio mobile in modo da utilizzare la funzionalità di notifica push avanzata fornita da Hub di notifica. L'utilizzo di Hub di notifica è in parte gratuito con il servizio mobile a pagamento. Per ulteriori informazioni, vedere la pagina [Dettagli prezzi: servizi mobili](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    **Importante**

    Questa operazione reimposta le credenziali push e modifica il comportamento dei metodi push negli script. Tali modifiche non possono essere annullate. Non utilizzare questo metodo per aggiungere un hub di notifica a un servizio mobile di produzione. Per indicazioni su come abilitare le notifiche push avanzate in un servizio mobile di produzione, vedere [queste linee guida](http://go.microsoft.com/fwlink/p/?LinkId=391951).

3.  Immettere il valore **API Key** ottenuto da GCM nella procedura precedente, quindi fare clic su **Save**.

    ![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    **Importante**

    Le credenziali GCM per le notifiche push avanzate configurate nella scheda Push del portale vengono condivise con Hub di notifica per configurare l'hub di notifica con l'app.

Il servizio mobile e l'app sono ora configurati per funzionare con GCM e Hub di notifica.

Aggiunta di notifiche push all'app
----------------------------------

### Verificare la versione di Android SDK

[WACOM.INCLUDE [Verifica SDK](../includes/mobile-services-verify-android-sdk-version.md)]

Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti.

Se il test verrà eseguito con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK](http://go.microsoft.com/fwlink/?LinkId=389801) per determinare il livello minimo su cui è possibile impostare tale valore.

### Aggiungere Google Play Services al progetto

[WACOM.INCLUDE [Aggiunta di Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Aggiungere codice

1.  Aprire il file `AndroidManifest.xml`. Nel codice per i due passaggi successivi sostituire _`**my_app_package**`_ con il nome del pacchetto dell'app per il progetto, ovvero il valore dell'attributo `package` del tag `manifest`.

2.  Aggiungere le nuove autorizzazioni seguenti dopo l'elemento `uses-permission` esistente:

         <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
             android:protectionLevel="signature" />
         <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
         <uses-permission android:name="android.permission.GET_ACCOUNTS" />
         <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  Aggiungere il codice seguente dopo il tag di apertura `application`:

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
             <intent-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

4.  Scaricare e decomprimere [Mobile Services Android SDK], aprire la cartella **notifications**, copiare il file **notifications-1.0.1.jar** nella cartella *libs* del progetto Eclipse, quindi aggiornare la cartella *libs*.

    **Nota**

    È possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.

5.  Aprire il file *ToDoItemActivity.java* e aggiungere l'istruzione import seguente:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  Aggiungere la variabile privata seguente alla classe, dove *`<PROJECT_NUMBER>`* è il numero di progetto assegnato da Google all'app nella procedura precedente:

         public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  Nel metodo **onCreate**, prima della creazione di istanze di MobileServiceClient, aggiungere il codice seguente per la registrazione del gestore delle notifiche per il dispositivo:

         NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    La classe MyHandler.class cui viene fatto riferimento in questo codice verrà definita in seguito.

8.  In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi scegliere **New** e fare clic su **Class**.

9.  In **Name** digitare `MyHandler`, in **Superclass** digitare `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Finish**

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    Verrà creata la nuova classe MyHandler.

10. Aggiungere la seguente istruzione import:

        import android.content.Context;

11. Quindi, aggiungere il codice seguente alla classe:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. Aggiungere il codice seguente per eseguire l'override del metodo **onRegistered**: che registra il dispositivo nell'hub di notifica del servizio mobile.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
                
            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. Aggiungere il codice seguente per eseguire l'override del metodo **onReceive**, che determina la visualizzazione della notifica al momento della ricezione.

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");

            sendNotification(nhMessage);
            }
            
            private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, ToDoActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

L'app è ora aggiornata per il supporto delle notifiche push.

Aggiornamento dello script insert registrato nel portale di gestione
--------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

         function insert(item, user, request) {
         // Define a payload for the Google Cloud Messaging toast notification.
         var payload = 
             '{"data":{"message" : "Hello from Mobile Services!"}}';
            
         request.execute({
             success: function() {
                 // If the insert succeeds, send a notification.
                 push.gcm.send(null, payload, {
                     success: function(pushResponse) {
                         console.log("Sent push:", pushResponse, payload);
                         request.respond();
                         },              
                     error: function (pushResponse) {
                         console.log("Error Sending push:", pushResponse);
                         request.respond(500, { error: pushResponse });
                         }
                     });
                 },
             error: function(err) {
                 console.log("request.execute error", err)
                 request.respond();
             }
           });
         }

    Verrà registrato un nuovo script insert, che utilizza l'[oggetto gcm] per inviare una notifica push a tutti i dispositivi registrati dopo la corretta esecuzione dell'inserimento. 

Esecuzione del test delle notifiche push nell'app
-------------------------------------------------

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure utilizzando un dispositivo virtuale nell'emulatore.

### Configurazione dell'emulatore per il test

Quando si esegue l'app nell'emulatore, assicurarsi di utilizzare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1.  Riavviare Eclipse, quindi in Package Explorer fare clic con il pulsante destro del mouse sul progetto, scegliere **Properties**, fare clic su **Android**, selezionare **Google APIs** e infine fare clic su **OK**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

	Il progetto farà quindi riferimento a Google APIs.

2.  Da **Window** selezionare **Android Virtual Device Manager**, quindi selezionare il dispositivo e fare clic su **Edit**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3.  Selezionare **Google APIs** in **Target**, quindi fare clic su OK.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

    AVD sarà quindi configurato per l'utilizzo di Google APIs.

### Esecuzione del test

1.  Dal menu **Run** di Eclipse, fare clic su **Run** per avviare l'app.

2.  Digitare testo significativo nell'app, ad esempio *A new Mobile Services task*, quindi fare clic sul pulsante **Add**.

	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

1.  Scorrere verso il basso dalla parte superiore della schermata per aprire il Notification Center del dispositivo e visualizzare la notifica.

L'esercitazione è stata completata.

Passaggi successivi
-------------------

In questa esercitazione viene illustrata la funzionalità di notifica push di base fornita da Servizi mobili. Se per l'app sono richieste funzionalità più avanzate, ad esempio l'invio di notifiche su piattaforme diverse, il routing basato sulla sottoscrizione o la gestione di volumi molto elevati, valutare la possibilità di utilizzare Hub di notifica di Azure con il servizio mobile. Per ulteriori informazioni, vedere uno degli argomenti seguenti relativi a Hub di notifica:

-   [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>Informazioni su come sfruttare Hub di notifica nell'app per Android.

-   [Invio di notifiche ai sottoscrittori](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>Informazioni su come utilizzare i modelli per inviare notifiche push da un servizio mobile, senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/en-us/develop/mobile/tutorials/get-started-with-data-js/)
    <br/>Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    <br/>Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
    <br/>Ulteriori informazioni su come utilizzare Servizi mobili con HTML e JavaScript.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-js/
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645
