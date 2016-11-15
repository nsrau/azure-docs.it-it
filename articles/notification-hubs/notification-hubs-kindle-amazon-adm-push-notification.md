---
title: Introduzione ad Hub di notifica di Azure per le app per Kindle | Documentazione Microsoft
description: In questa esercitazione, sono presenti informazioni su come usare Hub di notifica di Azure per inviare notifiche push a un&quot;applicazione per Kindle.
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7206f152ed7270abc62536a9ee164f7227833bcc


---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introduzione ad Hub di notifica per le app per Kindle
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
In questa esercitazione viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione Kindle.
Verrà creata un'app Kindle vuota che riceve notifiche push tramite Amazon Device Messaging (ADM).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario disporre di:

* Ottenere Android SDK (si presuppone che verrà utilizzato Eclipse) dal <a href="http://go.microsoft.com/fwlink/?LinkId=389797">sito Android</a>.
* Seguire i passaggi in <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Configurazione dell’ambiente di sviluppo</a> per impostare l'ambiente di sviluppo per Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Aggiunta di una nuova app al portale per sviluppatori
1. Innanzitutto, creare un'app nel [portale per gli sviluppatori di Amazon].
   
    ![][0]
2. Copiare la **chiave applicazione**.
   
    ![][1]
3. Nel portale, fare clic sul nome dell'app, quindi fare clic sulla scheda **Device Messaging** .
   
    ![][2]
4. Fare clic su **Create a New Security Profile**, quindi creare un nuovo profilo di sicurezza, ad esempio il **profilo di sicurezza TestAdm**. Fare quindi clic su **Salva**.
   
    ![][3]
5. Fare clic su **Security Profiles** per visualizzare il profilo di sicurezza creato. Copiare i valori **Client ID** e **Client Secret** per usarli in seguito.
   
    ![][4]

## <a name="create-an-api-key"></a>Creazione di una chiave API
1. Aprire un prompt dei comandi con privilegi di amministratore.
2. Passare alla cartella Android SDK.
3. Immettere il comando seguente:
   
        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
   
    ![][5]
4. Per la password **keystore**, digitare **android**.
5. Copiare l'ID digitale **MD5** .
6. Nel portale per sviluppatori, fare clic su **Android/Kindle** nella scheda **Messaging**, immettere il nome del pacchetto per l'app, ad esempio, **com.sample.notificationhubtest**, e il valore **MD5**, quindi fare clic su **Generate API Key** (Genera chiave API).

## <a name="add-credentials-to-the-hub"></a>Aggiunta di credenziali all'hub
Nel portale, aggiungere il segreto client e l'ID client alla scheda **Configure** dell'hub di notifica.

## <a name="set-up-your-application"></a>Configurazione dell'applicazione
> [!NOTE]
> Quando si crea un'applicazione, usare come requisito minimo il livello API 17.
> 
> 

Aggiungere le librerie ADM al progetto Eclipse:

1. Per ottenere la libreria ADM, [scaricare l'SDK]. Estrarre il file zip SDK.
2. In Eclipse fare clic con il pulsante destro del mouse sul progetto e scegliere **Properties**. Selezionare **Java Build Path** (Percorso compilazione Java) a sinistra e quindi selezionare la scheda **Libraries** (Librerie) nella parte superiore. Fare clic su **Add External Jar** (Aggiungi JAR esterni), quindi selezionare il file `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` dalla directory in cui è stato estratto Amazon SDK.
3. Scaricare NotificationHubs Android SDK (collegamento).
4. Decomprimere il pacchetto, quindi trascinare il file `notification-hubs-sdk.jar` nella cartella `libs` in Eclipse.

Modificare il manifesto dell'app per supportare ADM:

1. Aggiungere lo spazio dei nomi Amazon nell'elemento manifesto radice:

        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

1. Aggiungere le autorizzazioni come primo elemento sotto l'elemento manifesto. Sostituire **[YOUR PACKAGE NAME]** con il pacchetto usato per creare l'app.
   
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
2. Inserire l'elemento seguente come primo figlio dell'elemento dell'applicazione. Ricordare di sostituire **[YOUR SERVICE NAME]** con il nome del gestore di messaggi ADM che verrà creato nella sezione successiva (incluso il pacchetto) e sostituire **[YOUR PACKAGE NAME]** con il nome del pacchetto utilizzato per creare l'app.
   
        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />
   
        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />
   
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

## <a name="create-your-adm-message-handler"></a>Creazione del gestore di messaggi ADM
1. Creare una nuova classe che eredita da `com.amazon.device.messaging.ADMMessageHandlerBase` e denominarla `MyADMMessageHandler`, come mostrato nella figura seguente:
   
    ![][6]
2. Aggiungere le istruzioni `import` seguenti:
   
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub
3. Aggiungere il codice seguente alla classe creata. Ricordare di sostituire il nome dell'hub e la stringa di connessione (ascolto):
   
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
                  .setSmallIcon(R.mipmap.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                  .setContentText(msg);
   
             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
4. Aggiungere il codice seguente al metodo `OnMessage()` :
   
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
5. Aggiungere il codice seguente al metodo `OnRegistered` :
   
            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }
6. Aggiungere il codice seguente al metodo `OnUnregistered` :
   
         try {
             getNotificationHub(getApplicationContext()).unregister();
         } catch (Exception e) {
             Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
         }
7. Quindi, nel metodo `MainActivity` , aggiungere l'istruzione import seguente:
   
        import com.amazon.device.messaging.ADM;
8. Alla fine del metodo `OnCreate` aggiungere il codice seguente:
   
        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                         MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Aggiunta della chiave API all'app
1. In Eclipse creare un nuovo file denominato **api_key.txt** negli asset della directory del progetto.
2. Aprire il file e copiare la chiave API generata nel portale per sviluppatori Amazon.

## <a name="run-the-app"></a>Esecuzione dell'app
1. Avviare l'emulatore.
2. Nell'emulatore, scorrere dall'alto e fare clic su **Settings** (Impostazioni), quindi fare clic su **My account** ed effettuare la registrazione usando un account Amazon valido.
3. In Eclipse eseguire l'app.

> [!NOTE]
> Se si verifica un problema, controllare l'ora dell'emulatore (o del dispositivo). Il valore dell'ora deve essere accurato. Per modificare l'ora dell'emulatore Kindle è possibile eseguire il comando seguente dalla directory di strumenti della piattaforma Android SDK:
> 
> 

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Invio di un messaggio
Per inviare un messaggio usando .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[portale per gli sviluppatori di Amazon]: https://developer.amazon.com/home.html
[scaricare l'SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png



<!--HONumber=Nov16_HO2-->


