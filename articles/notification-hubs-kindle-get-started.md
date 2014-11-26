<properties linkid="develop-notificationhubs-tutorials-get-started-kindle" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="sethm" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-kindle" ms.devlang="Java" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle" class="current">Kindle</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento descrive come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Kindle.
In questa esercitazione verrà creata un'app per Kindle vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM).

Per completare questa esercitazione, è necessario disporre di:

-   Android SDK (si presuppone che verrà usato Eclipse), che è possibile scaricare [da questa pagina][da questa pagina].
-   Attenersi alla procedura riportata [in questa pagina][in questa pagina] per impostare l'ambiente di sviluppo per Kindle.

## Aggiunta di una nuova app al portale per sviluppatori

1.  Innanzitutto, creare un'app nel [portale per gli sviluppatori][portale per gli sviluppatori].

    ![][0]

2.  Copiare la **chiave applicazione**.

    ![][1]

3.  Nel portale, fare clic sul nome dell'app, quindi fare clic sulla scheda **Device Messaging**.

    ![][2]

4.  Fare clic su **Create a New Security Profile**, quindi creare un nuovo profilo di sicurezza (ad esempio il **profilo di sicurezza TestAdm**). Fare quindi clic su **Salva**.

    ![][3]

5.  Fare clic su "Security Profiles" per visualizzare il profilo di sicurezza creato. Copiare i valori **Client ID** e **Client Secret** per utilizzarli in seguito.

    ![][4]

## Creazione di una chiave API

1.  Aprire un prompt dei comandi con privilegi di amministratore.
2.  Passare alla cartella Android SDK.
3.  Immettere il comando seguente:

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  Per la password **keystore**, digitare **android**.

5.  Copiare l'ID digitale **MD5**.
6.  Nel portale per sviluppatori, fare clic su **Android/Kindle** nella scheda **Messaging**, immettere il nome del pacchetto per l'app (ad esempio, **com.sample.notificationhubtest**) e il valore **MD5**, quindi fare clic su **Generate API Key**.

## Aggiunta di credenziali all'hub

Nel portale, aggiungere il segreto client e l'ID client alla scheda **Configure** dell'hub di notifica.

## Configurazione dell'applicazione

<b>Nota</b>
    <p>Quando si crea un'applicazione, usare come requisito minimo il livello API 17.</p>

Aggiungere le librerie ADM al progetto Eclipse.

1.  Per ottenere la libreria ADM, [scaricare l'SDK][scaricare l'SDK]. Estrarre il file zip SDK.
2.  In Eclipse fare clic con il pulsante destro del mouse sul progetto e scegliere **Properties**. Selezionare **Java Build Path** a sinistra, quindi selezionare la scheda **Libraries** nella parte superiore. Fare clic su **Add External Jar**, quindi selezionare il file `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` dalla directory in cui è stato estratto Amazon SDK.
3.  Scaricare NotificationHubs Android SDK (collegamento).
4.  Decomprimere il pacchetto, quindi trascinare il file `notification-hubs-sdk.jar` nella cartella `libs` in Eclipse.

Modificare il manifesto dell'app per supportare ADM:

1.  Aggiungere lo spazio dei nomi Amazon nell'elemento manifesto radice:

        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2.  Aggiungere le autorizzazioni come primo elemento sotto l'elemento manifesto. Sostituire **[YOUR PACKAGE NAME]** con il pacchetto utilizzato per creare l'app.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  Inserire l'elemento seguente come primo figlio dell'elemento dell'applicazione. Ricordare di sostituire **[YOUR SERVICE NAME]** con il nome del gestore di messaggi ADM che verrà creato nella sezione successiva (incluso il pacchetto) e sostituire **[YOUR PACKAGE NAME]** con il nome del pacchetto utilizzato per creare l'app.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver"

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## Creazione del gestore di messaggi ADM:

1.  Creare una nuova classe che eredita da `com.amazon.device.messaging.ADMMessageHandlerBase` e denominarla `MyADMMessageHandler`, come mostrato nella figura seguente:

    ![][6]

2.  Aggiungere le istruzioni `import` seguenti:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3.  Aggiungere il codice seguente alla classe creata. Ricordare di sostituire il nome dell'hub e la stringa di connessione (ascolto):

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

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

4.  Aggiungere il codice seguente al metodo `OnMessage()`:

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5.  Aggiungere il codice seguente al metodo `OnRegistered`:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Aggiungere il codice seguente al metodo `OnUnregistered`:

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7.  Quindi, nel metodo `MainActivity`, aggiungere l'istruzione import seguente:

        import com.amazon.device.messaging.ADM;             

8.  Alla fine del metodo `OnCreate` aggiungere il codice seguente:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## Aggiunta della chiave API all'app

1.  In Eclipse creare un nuovo file denominato **api\_key.txt** negli asset della directory del progetto.
2.  Aprire il file e copiare la **chiave API** generata nel portale per sviluppatori Amazon.

## Esecuzione dell'app

1.  Avviare l'emulatore.
2.  Nell'emulatore, scorrere dall'alto e fare clic su **Settings**, quindi fare clic su **My account** ed effettuare la registrazione usando un account Amazon valido.
3.  In Eclipse eseguire l'app.

<div class="dev-callout"><b>Nota</b>
    <p>Se si verifica un problema, controllare l'ora dell'emulatore (o del dispositivo). Il valore dell'ora deve essere accurato. Per modificare l'ora dell'emulatore Kindle &egrave; possibile eseguire il comando seguente dalla directory di strumenti della piattaforma Android SDK: </p>
</div>

        adb shell  date -s "yyyymmdd.hhmmss"

## Invio di un messaggio

Per inviare un messaggio usando .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]



  [da questa pagina]: http://go.microsoft.com/fwlink/?LinkId=389797
  [in questa pagina]: https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment
  [portale per gli sviluppatori]: https://developer.amazon.com/home.html
  [0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
  [1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
  [2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
  [3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
  [4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
  [5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
  [scaricare l'SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
  [6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
  [7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
