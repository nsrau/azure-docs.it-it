<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento descrive come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Android.
In questa esercitazione verrà creata un'app per Android vuota che riceve notifiche push tramite il servizio Google Cloud Messaging (GCM). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

-   [Abilitazione di Google Cloud Messaging][Abilitazione di Google Cloud Messaging]
-   [Configurazione dell'hub di notifica][Configurazione dell'hub di notifica]
-   [Connessione dell'app all'hub di notifica][Connessione dell'app all'hub di notifica]
-   [Come inviare le notifiche all'app][Come inviare le notifiche all'app]
-   [Test dell'app][Test dell'app]

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a usare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici.

Per completare questa esercitazione, è necessario disporre di:

-   Android SDK (si presuppone che verrà usato Eclipse), che è possibile scaricare facendo clic su questo [collegamento][collegamento]
-   [Mobile Services Android SDK][Mobile Services Android SDK]

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app per Android.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario disporre di un account Azure attivo. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <span id="register"></span></a>Abilitazione di Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Questo valore della chiave dell'API verrà usato successivamente per abilitare l'hub di notifica con GCM e inviare notifiche push per conto dell'app.

## <span id="configure-hub"></span></a>Configurazione dell'hub di notifica

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] e fare clic su **+NEW** nella parte inferiore della schermata.

2.  Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

    ![][0]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

    ![][1]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic su **Configure** nella parte superiore.

    ![][2]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

    ![][3]

6.  Fare clic sulla scheda **Configure** nella parte superiore, immettere il valore **API Key** ottenuto nella sezione precedente, quindi fare clic su **Save**.

    ![][4]

7.  Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **View Connection String**. Prendere nota delle due stringhe di connessione.

L'hub di notifica è ora configurato per l'uso con GCM e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche push.

## <span id="connecting-app"></span></a>Connessione dell'app all'hub di notifica

### Creare un nuovo progetto Android

1.  Nel plug-in ADT per Eclipse, creare un nuovo progetto Android (File, New, Android Application).

    ![][5]

2.  Verificare che **Minimum Required SDK** sia impostato su *API 8: Android 2.2 (Froyo)* e che le due voci relative all'SDK successive siano impostate sulla versione disponibile più recente. Scegliere Next e seguire le istruzioni della procedura guidata, verificando che sia selezionata l'opzione **Create activity** per creare un'attività vuota. Nella finestra successiva accettare l'icona Launcher predefinita e fare clic su **Finish** nell'ultima finestra.

    ![][6]

### Aggiungere Google Play Services al progetto

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Aggiungere codice

1.  Scaricare Notification Hubs Android SDK da questo [collegamento][Mobile Services Android SDK]. Estrarre il file ZIP e copiare il file notificationhubs\\notification-hubs-0.1.jar nella directory \\libs del progetto in Package Explorer.

2.  Scaricare e decomprimere [Mobile Services Android SDK][Mobile Services Android SDK], aprire la cartella **notifications**, copiare il file **notifications-1.0.1.jar** nella cartella *libs* del progetto Eclipse, quindi aggiornare la cartella *libs*.

    <div class="dev-callout"><b>Nota</b>
<p>&Egrave; possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.</p>
</div>

    Configurare quindi l'applicazione per ottenere un *registrationId* da GCM e usare questo ID per registrare l'istanza dell'app nell'hub di notifica.

3.  Nel file AndroidManifest.xml aggiungere la riga di codice seguente sotto l'elemento <uses-sdk></uses-sdk>. Assicurarsi di sostituire il segnaposto `<your package>` con il pacchetto selezionato per l'app nel passaggio 1 (`com.yourCompany.wams_notificationhubs` in questo esempio).

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

    <div class="dev-callout"><b>Nota</b>
<p>Assicurarsi di impostare SENDER_ID sul numero di progetto ottenuto in precedenza.</p>
</div>

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

    <div class="dev-callout"><b>Nota</b>
<p>Sostituire il segnaposto con il nome del pacchetto.</p>
</div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi scegliere **New** e fare clic su **Class**.

10. In **Nome** digitare `MyHandler`, in **Superclass** digitare `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Fine**

    ![][7]

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

## <a name="send"></a>Come inviare le notifiche all'app

È possibile inviare notifiche usando gli hub di notifica da qualsia back-end tramite l'[interfaccia REST][interfaccia REST]. In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. Per un esempio relativo all'invio di notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere **Introduzione alle notifiche push in Servizi mobili** ([Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end .NET]). Per un esempio relativo all'invio di notifiche tramite le API REST, vedere **Come usare Hub di notifica da Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  In Visual Studio dal menu **File** scegliere **Nuovo** e quindi **Progetto**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console** e infine su **OK**.

    ![][8]

    Verrà creato un nuovo progetto di applicazione console.

2.  Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.

    Verrà visualizzata la Console Gestione pacchetti.

3.  Nella finestra della console eseguire il comando seguente:

        Install-Package WindowsAzure.ServiceBus

    Verrà aggiunto un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus][pacchetto NuGet WindowsAzure.ServiceBus].

4.  Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.ServiceBus.Notifications;

5.  Nella classe **Program** aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica del portale**: Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione "Configurazione dell'hub di notifica".

    > [WACOM.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo Listen non è autorizzata all'invio di notifiche.

6.  Aggiungere quindi le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>Test dell'app

Prima di testare l'app in un emulatore, sarà necessario completare queste procedure di configurazione dell'emulatore. Se si esegue il test in un dispositivo fisico, è possibile ignorare questi passaggi:

1.  assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

2.  In **Window** scegliere **Android Virtual Device Manager**, selezionare il dispositivo e quindi fare clic su **Edit**.

    ![][9]

3.  Selezionare **Google APIs** in **Target**, quindi fare clic su **OK**.

    ![][10]

4.  Per ricevere notifiche push è necessario configurare un account Google nell'emulatore Android Virtual Device scegliendo **Settings**, quindi **Add Account**. Assicurarsi inoltre che l'emulatore sia connesso a Internet.

Eseguire i passaggi seguenti per eseguire l'app in un dispositivo o nell'emulatore:

1.  Nella barra degli strumenti superiore di Eclipse fare clic su **Run** e selezionare l'app.

    Verrà avviato l'emulatore, se usato, e l'app verrà caricata ed eseguita. L'app recupera la proprietà *registrationId* da GCM ed esegue la registrazione con l'hub di notifica.

2.  Premere F5 in Visual Studio per eseguire l'app della console.

    Una notifica verrà inviata all'app.

3.  Quando viene visualizzata un'icona nell'area di notifica (angolo superiore sinistro), aprire la cassetta per visualizzare la notifica.

    ![][11]

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Android. Per indirizzare le notifiche a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti][Utilizzo di Hub di notifica per inviare notifiche push agli utenti], mentre se si desidera segmentare gli utenti in base a gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie][Utilizzo di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'utilizzo di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica][linee guida su Hub di notifica].



  [Abilitazione di Google Cloud Messaging]: #register
  [Configurazione dell'hub di notifica]: #configure-hub
  [Connessione dell'app all'hub di notifica]: #connecting-app
  [Come inviare le notifiche all'app]: #send
  [Test dell'app]: #run-app
  [collegamento]: http://go.microsoft.com/fwlink/?LinkId=389797
  [Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [interfaccia REST]: http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /it-it/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /it-it/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [pacchetto NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [Utilizzo di Hub di notifica per inviare notifiche push agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
  [Utilizzo di Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet
  [linee guida su Hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
