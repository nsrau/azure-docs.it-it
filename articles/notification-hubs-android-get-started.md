<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

Introduzione ad Hub di notifica
===============================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/it-it/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/it-it/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/it-it/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/it-it/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Android. In questa esercitazione verrà creata un'app per Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

-   [Abilitazione di Google Cloud Messaging](#register)
-   [Configurazione dell'hub di notifica](#configure-hub)
-   [Connessione dell'app all'hub di notifica](#connecting-app)
-   [Come inviare le notifiche all'app](#send)
-   [Test dell'app](#run-app)

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a utilizzare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici.

Per completare questa esercitazione, è necessario disporre di:

-   Android SDK (si presuppone che verrà utilizzato Eclipse), che è possibile scaricare facendo clic su questo [collegamento](http://go.microsoft.com/fwlink/?LinkId=389797)
-   [Mobile Services Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Android.

**Nota**

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Abilitazione di Google Cloud Messaging
--------------------------------------

[WACOM.INCLUDE [Abilitazione di GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Questo valore della chiave dell'API verrà utilizzato successivamente per abilitare l'hub di notifica con GCM e inviare notifiche push per conto dell'app.

Configurazione dell'hub di notifica
-----------------------------------

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic su **+NEW** nella parte inferiore della schermata.

2.  Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

	![][7]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

	![][8]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic su **Configure** nella parte superiore.

	![][9]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

	![][10]

6.  Fare clic sulla scheda **Configure** nella parte superiore, immettere il valore **API Key** ottenuto nella sezione precedente, quindi fare clic su **Save**.

	![][11]

7.  Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **View Connection String**. Prendere nota delle due stringhe di connessione.

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche push.

Connessione dell'app all'hub di notifica
----------------------------------------

### Creare un nuovo progetto Android

1.  Nel plug-in ADT per Eclipse, creare un nuovo progetto Android (File, New, Android Application).

	![][13]

2.  Verificare che **Minimum Required SDK** sia impostato su *API 8: Android 2.2 (Froyo)* e che le due voci relative all'SDK successive siano impostate sulla versione disponibile più recente. Scegliere Next e seguire le istruzioni della procedura guidata, verificando che sia selezionata l'opzione **Create activity** per creare un'attività vuota. Nella finestra successiva accettare l'icona Launcher predefinita e fare clic su **Finish** nell'ultima finestra.

	![][14]

### Aggiungere Google Play Services al progetto

[WACOM.INCLUDE [Aggiunta di Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Aggiungere codice

1.  Scaricare Notification Hubs Android SDK da questo [collegamento](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409). Estrarre il file ZIP e copiare il file notificationhubs\\notification-hubs-0.1.jar nella directory \\libs del progetto in Package Explorer.

2.  Scaricare e decomprimere [Mobile Services Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409), aprire la cartella **notifications**, copiare il file **notifications-1.0.1.jar** nella cartella *libs* del progetto Eclipse, quindi aggiornare la cartella *libs*.

    **Nota**

    È possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.

    Configurare quindi l'applicazione per ottenere un *registrationId* da GCM e utilizzare questo ID per registrare l'istanza dell'app nell'hub di notifica.

3.  Nel file AndroidManifest.xml aggiungere la riga di codice seguente sotto l'elemento &lt;uses-sdk/\>. Assicurarsi di sostituire il segnaposto `<your package>` con il pacchetto selezionato per l'app nel passaggio 1 (in questo esempio `com.yourCompany.wams_notificationhubs`).

         <uses-permission android:name="android.permission.INTERNET"/>
         <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
         <uses-permission android:name="android.permission.WAKE_LOCK"/>
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

         <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
         <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  Nella classe **MainActivity** aggiungere le istruzioni seguenti:

         import android.os.AsyncTask;    
         import com.google.android.gms.gcm.*;
         import com.microsoft.windowsazure.messaging.*;
         import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  Aggiungere i membri privati seguenti nella parte superiore della classe.

    **Nota**

    Assicurarsi di impostare SENDER\_ID sul numero di progetto ottenuto in precedenza.

         private String SENDER_ID = "<your project number>";
         private GoogleCloudMessaging gcm;
         private NotificationHub hub;

6.  Nel metodo **OnCreate** aggiungere il codice seguente e assicurarsi di sostituire i segnaposto con la stringa di connessione con accesso Listen ottenuta nella sezione precedente e con il nome dell'hub di notifica visualizzato nella parte superiore di Azure per l'hub (**non** l'URL completo).

         NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

         gcm = GoogleCloudMessaging.getInstance(this);
            
         String connectionString = "<your listen access connection string>";
         hub = new NotificationHub("<your notification hub name>", connectionString, this);
            
         registerWithNotificationHubs();

7.  In MainActivity.java creare il metodo seguente:

         @SuppressWarnings("unchecked")
         private void registerWithNotificationHubs() {
            new AsyncTask() {
               @Override
               protected Object doInBackground(Object... params) {
                  try {
                     String regid = gcm.register(SENDER_ID);
                     hub.register(regid);
                  } catch (Exception e) {
                     return e;
                  }
                  return null;
              }
            }.execute(null, null, null);
         }

8.  Poiché il sistema operativo Android non consente la visualizzazione di notifiche, è necessario scrivere un apposito ricevitore. In **AndroidManifest.xml** aggiungere l'elemento seguente all'interno dell'elemento `<application/>`.

    **Nota**

    Sostituire il segnaposto con il nome del pacchetto.

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

9.  In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi scegliere **New** e fare clic su **Class**.

10. In **Name** digitare `MyHandler`, in **Superclass** digitare `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Finish**

    ![](./media/notification-hubs-android-get-started/notification-hub-android-new-class.png)

    Verrà creata la nuova classe MyHandler.

11. Aggiungere le istruzioni import seguenti:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

12. Aggiungere il codice seguente alla classe:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

            
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("msg");

            sendNotification(nhMessage);
            }
            
            private void sendNotification(String msg) {
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

Come inviare le notifiche all'app
---------------------------------

È possibile utilizzare Hub di notifica per inviare notifiche da qualsiasi back-end che utilizza l'[interfaccia REST](http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx). In questa esercitazione vengono illustrati due modi per inviare le notifiche: con un'app console .NET e con un servizio mobile tramite uno script node.

### Per inviare notifiche tramite un'app console .NET:

1.  Creare una nuova applicazione console in Visual C\#:

	![][20]

2.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

         Install-Package WindowsAzure.ServiceBus

    e premere INVIO.

3.  Aprire il file Program.cs e aggiungere l'istruzione using seguente:

         using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente:

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{  private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;data private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot; : { private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;msg private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;: private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;Hello from Azure! private static async void SendNotificationAsync()? {?     NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");? }?quot;}}");
         }

5.  Aggiungere quindi le righe seguenti nel metodo Main:

          SendNotificationAsync();
          Console.ReadLine();

### Per inviare una notifica tramite con un servizio mobile

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e selezionare il servizio mobile. Se non è presente alcun servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started/#create-new-service).

2.  Selezionare la scheda **Scheduler** nella parte superiore.

	![][22]

3.  Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **On demand**.

	![][23]

4.  Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5.  Inserire lo script seguente all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a *DefaultFullSharedAccessSignature* ottenuti nel passaggio precedente. Fare clic su **Save**.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
         notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',

    function (error) {

             if (!error) {
                console.warn("Notification successful");
             }
             else
             {
               console.warn("Notification failed" + error);
             }
           }
         );

Test dell'app
-------------

Per testare l'app con un telefono effettivo, collegare il telefono al computer con un cavo USB.

Per testare l'app con l'emulatore:

1.  assicurarsi di utilizzare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

2.  In **Window** scegliere **Android Virtual Device Manager**, selezionare il dispositivo e quindi fare clic su **Edit**.

	![][18]

3.  Selezionare **Google APIs** in **Target**, quindi fare clic su **OK**.

 	![][19]

4.  Per ricevere notifiche push è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo **Settings**, quindi **Add Account**. Assicurarsi inoltre che l'emulatore sia connesso a Internet.

Indipendentemente dal dispositivo selezionato, eseguire le operazioni seguenti:

1.  Nella barra degli strumenti superiore di Eclipse fare clic su **Run** e selezionare l'app. L'app verrà caricata nel telefono collegato oppure verrà avviato l'emulatore nel quale verrà caricata ed eseguita l'app.

2.  L'app recupera la proprietà *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

3.  Ora è possibile utilizzare uno dei metodi illustrati nella sezione precedente per inviare una notifica all'app:

    -   Se si utilizza un'applicazione console .NET, premere F5 in Visual Studio per avviare l'applicazione e inviare una notifica.
    -   Se invece si utilizza uno script di Servizi mobili, fare clic su **Run Once** nella barra inferiore della schermata del servizio mobile affinché lo script invii una notifica.

4.  Verrà visualizzata un'icona nell'area di notifica nell'angolo superiore sinistro. Aprire la cassetta per visualizzare la notifica.

	![][21]

Passaggi successivi
-------------------

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Android. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet), mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Per ulteriori informazioni sull'utilizzo di Hub di notifica, vedere la pagina relativa alle [linee guida per gli hub di notifica](http://msdn.microsoft.com/it-it/library/jj927170.aspx) e quella relativa alle [procedure di Hub di notifica per Android](http://msdn.microsoft.com/it-it/library/dn282661.aspx).

<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/it-it/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet