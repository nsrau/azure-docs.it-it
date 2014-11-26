<properties linkid="develop-notificationhubs-tutorials-get-started-nokia-x" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="kirillg" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows universale</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento descrive come usare **Hub di notifica di Azure** per inviare notifiche push a un'applicazione per **Nokia X**. In questa esercitazione si creerà un'app per Android vuota che riceve notifiche push tramite il servizio di notifica Nokia. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

-   [Configurare il servizio di notifica Nokia][Configurare il servizio di notifica Nokia]
-   [Configurazione dell'hub di notifica][Configurazione dell'hub di notifica]
-   [Connessione dell'app all'hub di notifica][Connessione dell'app all'hub di notifica]
-   [Come inviare una notifica all'app][Come inviare una notifica all'app]
-   [Test dell'app][Test dell'app]

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario disporre di un account Azure attivo. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

Per completare questa esercitazione, è necessario disporre di:

1.  Ambiente di sviluppo di Nokia X, che è possibile configurare seguendo le istruzioni disponibili facendo clic su questo [collegamento][collegamento]. Assicurarsi di aver installato i pacchetti specifici per Nokia X e configurato l'emulatore Nokia X seguendo le istruzioni.
2.  Impostazione del dispositivo Nokia X (facoltativa), che è possibile configurare seguendo le istruzioni disponibili facendo clic su questo [collegamento][1].
3.  Android SDK (si presuppone che verrà usato Eclipse), che è possibile scaricare facendo clic su questo [collegamento][2].
4.  Mobile Services Android SDK, che è possibile scaricare facendo clic su questo [collegamento][3]<a>.

## <span id="register"></span></a>Configurare il servizio di notifica Nokia

1.  Accedere alla [console per sviluppatori di API di notifica Nokia][console per sviluppatori di API di notifica Nokia].

2.  Passare alla scheda **Create services** e creare un nuovo servizio fornendo un ID mittente in **Sender ID** e una descrizione del servizio in **Service description**.

    ![][0]

3.  Annotare l'ID visualizzato nel campo **Sender ID** e la chiave di autorizzazione in **Authorization Key** dopo aver creato il servizio correttamente.

4.  Passare quindi alla scheda **My services** per elencare tutti i servizi creati con i rispettivi **ID mittente** e **chiave di autorizzazione**.

    ![][4]

5.  Per informazioni dettagliate, fare clic su questo [collegamento][5].

## <span id="configure-hub"></span></a>Configurazione dell'hub di notifica

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] e fare clic su **+NEW** nella parte inferiore della schermata.

2.  Fare clic su **Servizi app**, selezionare **Service Bus**, **Hub di notifica**, quindi **Creazione rapida**.

    ![][6]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata e, facoltativamente, uno spazio dei nomi, quindi fare clic su **Crea un nuovo hub di notifica**.

    ![][7]

4.  Passare all'hub di notifica appena creato. Fare clic sulla scheda Service Bus a sinistra e fare cli sullo spazio dei nomi in cui è stato creato l'hub di notifica, quindi fare clic sulla scheda Hub di notifica.

    ![][8]

5.  Una volta eseguito l'accesso all'hub di notifica creato, fare clic sulla scheda **Configura** posta in alto.

    ![][9]

6.  Scorrere verso il basso fino all'opzione **impostazioni di notifica nokia X** e incollare la chiave di autorizzazione ottenuta dal servizio di notifica Nokia, quindi fare clic su **Salva** nella parte inferiore della pagina.

    ![][10]

7.  Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **Informazioni di connessione** nella parte inferiore della pagina.

    ![][11]

8.  Annotare le due stringhe di connessione della firma di accesso condiviso per **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature**, che verranno usate più avanti in questa esercitazione.

## <span id="connect-hub"></span></a>Connessione dell'app all'hub di notifica

### Creare un nuovo progetto Android

1.  Nel plug-in ADT per Eclipse, creare un nuovo progetto Android (File -\> New -\> Android Application).

2.  Verificare che Minimum Required SDK sia impostato su **API 16: Android 4.1 (Jelly Bean)** e che le due voci relative all'SDK successive siano impostate sulla versione disponibile più recente. Scegliere Next e seguire le istruzioni della procedura guidata, verificando che sia selezionata l'opzione **Create activity** per creare un'**attività vuota**. Nella finestra successiva accettare l'icona Launcher predefinita e fare clic su **Finish** nell'ultima finestra.

    ![][12]

3.  Assicurarsi che la destinazione di compilazione progetto sia correttamente impostata (per questo esempio, Platform = 4.1.2 e API Level = 16). Fare clic con il pulsante destro del mouse su Project, selezionare Properties e quindi Android nella finestra di dialogo Project Properties.

    ![][13]

4.  Aggiungere il file JAR del servizio di notifica Nokia al progetto. Questa libreria helper di Nokia Notifications **push.jar** offre un'API simile alla libreria helper GCM.
    Passare a Project Properties -\> Java Build Path -\> Libraries -\> Add External JARs e aggiungere il file **push.jar** disponibile in **\\extras\\nokia\\nokia\_x\_services\\libs\\nna\\push.jar**. Il file Javadoc per la libreria si trova nel percorso **\\extras\\nokia\\nokia\_x\_services\\javadocs\\nna** in cui è installato IDE Eclipse.

5.  Assicurarsi di copiare anche la libreria push.jar nella directory \\libs del progetto in Package Explorer.

6.  Scaricare Notification Hubs Android SDK da questo [collegamento][3]. Estrarre il file ZIP e copiare il file notificationhubs\\notification-hubs-0,3.jar nella directory \\libs del progetto in Package Explorer.

    <div class="dev-callout"><b>Nota</b>
<p>&Egrave; possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.</p>
</div>

### Aggiungere codice

1.  Aprire il file **AndroidManifest.xml** e sostituire l'elemento applicazione con le righe di codice seguenti, accertandosi di sostituire `[YourPackageName]` con il nome del pacchetto specificato durante la creazione dell'applicazione Android.

        <!-- Push Notifications connects to Internet services. -->
        <uses-permission android:name="android.permission.INTERNET" />

        <!-- Keeps the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

         <!--
         Creates a custom permission so only this app can receive its messages.

         NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
               where PACKAGE is the application's package name.
        -->
        <permission 
            android:name="[YourPackageName].permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />

        <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />

        <application
            android:allowBackup="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name="[YourPackageName].MainActivity"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>

            <receiver
                android:name="com.nokia.push.PushBroadcastReceiver"
                android:permission="com.nokia.pushnotifications.permission.SEND" >
                <intent-filter>

                    <!-- Receives the actual messages. -->
                    <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
                    <!-- Receives the registration id. -->
                    <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
                    <category android:name="[YourPackageName]" />
                </intent-filter>
            </receiver>

            <service android:name="[YourPackageName].PushIntentService" />
        </application> 

2.  In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi fare clic su **New**, **Class** e creare una nuova classe denominata **ConfigurationSettings.java**
    ![][14].

    Quindi, aggiungere il codice seguente che definisce le costanti usate nell'esempio:

        public class ConfigurationSettings {
            public static String NotificationHubName = "";
            public static String NotificationHubConnectionString = "";
            public static String SenderId = "";
        }

    Compilare le costanti sopra illustrate con il **SenderId** di configurazione della console push Nokia, quindi **NotificationHubName** e **NotificationHubConnectionString** (DefaultListenSharedAccessSignature) dal portale di gestione.

3.  In **MainActivity.java**, aggiungere l'istruzione import seguente:

        import com.nokia.push.PushRegistrar;

    quindi aggiungere il codice seguente nel metodo onCreate:

        // Make sure the device has the proper dependencies.
        PushRegistrar.checkDevice(this);

        // Make sure the manifest was properly set - comment out this line
        // while developing the app, then uncomment it when it's ready
        PushRegistrar.checkManifest(this);

        // Register the device with the Nokia Notification service
        PushRegistrar.register(this, ConfigurationSettings.SenderId);

4.  Aggiungere un'altra nuova classe denominata **PushIntentService.java** e il codice seguente, che verrà registrato sull'hub di notifica e gestirà anche i messaggi ricevuti da quest'ultimo.

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import android.util.Log;

        import com.microsoft.windowsazure.messaging.NotificationHub;
        import com.nokia.push.PushBaseIntentService;

        /**
         * IntentService responsible for handling push notification messages.
         */
        public class PushIntentService extends PushBaseIntentService {

            private NotificationManager mNotificationManager;
            private static NotificationHub hub;
            public static final int NOTIFICATION_ID = 1;
            private static final String TAG = "NokiaXPush/PushIntentService";

            /**
             * Constructor.
             */
            public PushIntentService() {
            }

            @Override
            protected String[] getSenderIds(Context context) {
                return new String[] { ConfigurationSettings.SenderId };
            }   

            @Override
            protected void onRegistered(Context context, String registrationId) {
                Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
                RegisterWithNotificationHub(context, registrationId);
            }

            public static void RegisterWithNotificationHub(Context context, String registrationId) {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName, 
                            ConfigurationSettings.NotificationHubConnectionString, 
                            context);
                }
                try {
                    hub.register(registrationId);
                    Log.i(TAG, "Registered with Notification Hub - '" 
                            + ConfigurationSettings.NotificationHubName + "'" 
                            + " with RegistrationID - '"
                            + registrationId + "'");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            protected void onMessage(Context context, Intent intent) {
                String message = intentExtrasToString(intent.getExtras());
                Log.i(TAG, "Received message. Extras: " + message);
                generateNotification(context, message);
            }

            /**
             * Extracts the key-value pairs from the given Intent extras and returns
             * them in a string.
             */
            private String intentExtrasToString(Bundle extras) {
                StringBuilder sb = new StringBuilder();
                sb.append("{ ");

                for (String key : extras.keySet()) {
                    sb.append(sb.length() <= 2 ? "" : ", ");
                    sb.append(key).append("=").append(extras.get(key));
                }

                sb.append(" }");
                return sb.toString();
            }

            /**
             * Issues a notification to inform the user that server has sent a message.
             */
            private void generateNotification(Context context, String message)
            {
                mNotificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);

                PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
                    new Intent(context, MainActivity.class), 0);

                NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("Notification Hub Demo")
                    .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(message))
                    .setContentText(message);

                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

            @Override
            protected void onError(Context arg0, String errorId) {
                Log.i(TAG, "Received error:  " + errorId);
            }

            @Override
            protected void onUnregistered(Context arg0, String errorId) {
                Log.i(TAG, "Received recoverable error: " + errorId);
            }
        }

## <a name="send"></a>Come inviare una notifica all'app

È possibile usare Hub di notifica per inviare notifiche da qualsiasi back-end che usa l'[interfaccia REST][interfaccia REST]. In questa esercitazione verrà illustrato l'utilizzo di un'app console .NET.

1.  Creare una nuova applicazione console in Visual C#:

    ![][15]

2.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus][pacchetto NuGet WindowsAzure.ServiceBus]. Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare quanto segue e premere Invio:

        Install-Package WindowsAzure.ServiceBus

3.  Aprire il file Program.cs e aggiungere l'istruzione using seguente:

        using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
            await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
        }

5.  Aggiungere quindi le righe seguenti nel metodo Main:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-app"></a>Test dell'app

Per testare l'app con un telefono effettivo, collegare il telefono al computer con un cavo USB.

Per testare l'app con l'emulatore:

1.  Nella barra degli strumenti superiore di Eclipse fare clic su Run e selezionare l'app.

2.  L'app verrà caricata nel telefono collegato oppure verrà avviato l'emulatore nel quale verrà caricata ed eseguita l'app.

3.  L'app recupera la proprietà registrationId dal servizio di notifica Nokia ed esegue la registrazione con l'hub di notifica.

    <div class="dev-callout"><b>Nota</b>
<p>
Se &egrave; possibile registrare correttamente l'app per Android con l'hub di notifica, verr&agrave; visualizzato un messaggio come il seguente nei log &quot;Eclipse Logcat&quot;:
&quot;Registered with Notification Hub - &quot;<yourNotificationHubName>&quot; with RegistrationID - &quot;<RegistrationIdReturnedByNokiaNotificationService'' </p>
</div>

4.  Per inviare una notifica di prova quando si usa l'applicazione console .NET, premere il tasto F5 in Visual Studio per eseguire l'applicazione. Verrà inviata una notifica che sarà visualizzata nell'area di notifica posta nella parte superiore del dispositivo o dell'emulatore.




  [Configurare il servizio di notifica Nokia]: #register
  [Configurazione dell'hub di notifica]: #configure-hub
  [Connessione dell'app all'hub di notifica]: #connect-hub
  [Come inviare una notifica all'app]: #send
  [Test dell'app]: #test-app
  [collegamento]: http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html
  [1]: http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html
  [2]: http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409
  [3]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [console per sviluppatori di API di notifica Nokia]: https://console.push.nokia.com/ncm/Web/index.jsp
  [0]: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
  [4]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
  [5]: http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [6]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
  [7]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
  [8]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
  [9]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
  [10]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
  [11]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
  [12]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
  [13]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png
  [14]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
  [interfaccia REST]: http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx
  [15]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
  [pacchetto NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
