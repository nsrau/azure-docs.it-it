<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Introduzione alle notifiche push in Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows Store C#</a>
    <a href="/it-it/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/it-it/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/it-it/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/it-it/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite il servizio Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE]Questo argomento riguarda i servizi mobili *esistenti* che *non sono stati ancora aggiornati* per l'integrazione di Hub di notifica. Quando si crea un *nuovo* servizio mobile, questa funzionalità integrata viene abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push][].
>
> Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. *È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile*. Una volta eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push][].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

-   [Abilitazione di Google Cloud Messaging][]
-   [Configurazione di Servizi mobili][]
-   [Aggiungere notifiche push all'app][]
-   [Aggiornamento degli script per l'invio di notifiche push][]
-   [Inserimento di dati per la ricezione di notifiche][]

Per completare questa esercitazione, è necessario disporre di:

-   [Mobile Services Android SDK][]
-   Un account Google attivo

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][].

## <span id="register"></span></a>Abilitazione di Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM][]]

Questo valore della chiave dell'API verrà utilizzato successivamente per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

## <span id="configure"></span></a>Configurazione di Servizi mobili per l'invio di richieste push

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sull'app.

    ![][]

2.  Fare clic sulla scheda **Push**, immettere il valore **API Key** ottenuto da GCM nella procedura precedente, quindi fare clic su **Save**.

    ![][1]

Il servizio mobile è ora configurato per l'utilizzo con GCM per l'invio di notifiche push.

## <span id="add-push"></span></a>Aggiungere notifiche push all'app

### Aggiungere Google Play Services al progetto

[WACOM.INCLUDE [Add Play Services][]]

### Aggiungere codice

1.  Aprire il file del progetto **AndroidManifest.xml**. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti. Se è necessario impostare un valore inferiore a 16 perché si utilizza un dispositivo meno recente, per determinare il livello minimo su cui è possibile impostare tale valore, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK][]. Impostare la proprietà in modo appropriato.

2.  Verificare che nell'elemento `uses-sdk` il valore di **targetSdkVersion** sia impostato sul numero di una piattaforma SDK installata (passaggio 1). È consigliabile impostare tale valore sulla versione più recente disponibile.

3.  Il tag **uses-sdk** potrebbe avere un aspetto analogo al seguente, in base alle scelte effettuate nei passaggi precedenti:

        <uses-sdk
            android:minSdkVersion="17"
            android:targetSdkVersion="19" />

4.  Nel codice per i due passaggi successivi sostituire *`**my_app_package**`* con il nome del pacchetto dell'app per il progetto, ovvero il valore `package` dell'attributo del tag `manifest`.

5.  Aggiungere le nuove autorizzazioni seguenti dopo l'elemento `uses-permission` esistente:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

6.  Aggiungere il codice seguente dopo il tag di apertura `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

7.  Aprire il file ToDoItem.java, quindi aggiungere il codice seguente alla classe **TodoItem**:

            @com.google.gson.annotations.SerializedName("handle")
            private String mHandle;

            public String getHandle() {
                return mHandle;
            }

            public final void setHandle(String handle) {
                mHandle = handle;
            }

    Tale codice consente di creare una nuova proprietà che include l'ID di registrazione.

    <div class="dev-callout"><b>Nota</b>
<p>Se lo schema dinamico &egrave; abilitato nel servizio mobile, una nuova colonna <strong>handle</strong> verr&agrave; aggiunta automaticamente alla tabella <strong>TodoItem</strong> quando viene inserito un nuovo elemento contenente tale propriet&agrave;.</p>
</div>

8.  Scaricare e decomprimere [Mobile Services Android SDK][], aprire la cartella **notifications**, copiare il file **notifications-1.0.1.jar** nella cartella *libs* del progetto Eclipse, quindi aggiornare la cartella *libs*.

    <div class="dev-callout"><b>Nota</b>
<p>&Egrave; possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.</p>
</div>

9.  Aprire il file *ToDoItemActivity.java*, quindi aggiungere l'istruzione import seguente:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Aggiungere la variabile privata seguente alla classe, dove *`<PROJECT_NUMBER>`* è il numero di progetto assegnato da Google all'app nella procedura precedente:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. Nel metodo **onCreate**, prima della creazione di istanze di MobileServiceClient, aggiungere il codice seguente per la registrazione del gestore delle notifiche per il dispositivo:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Aggiungere la riga di codice seguente al metodo **addItem**, prima dell'inserimento dell'elemento nella tabella:

        item.setHandle(MyHandler.getHandle());

    Questo codice consente di impostare la proprietà `handle` dell'elemento sull'ID di registrazione del dispositivo.

13. In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi scegliere **New** e fare clic su **Class**.

14. In **Nome** digitare `MyHandler`, in **Superclass** digitare `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Fine**

    ![][2]

    Verrà creata la nuova classe MyHandler.

15. Aggiungere le istruzioni import seguenti:

        import android.content.Context;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

16. Aggiungere il codice seguente:

        @com.google.gson.annotations.SerializedName("handle")
        private static String mHandle;

        public static String getHandle() {
            return mHandle;
        }

        public static final void setHandle(String handle) {
            mHandle = handle;
        }

17. Sostituire l'override del metodo **onRegistered** con il codice seguente:

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            setHandle(gcmRegistrationId);
        }

L'app è ora aggiornata per il supporto delle notifiche push.

## <span id="update-scripts"></span></a>Aggiornamento dello script insert registrato nel portale di gestione

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][3]

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

    ![][4]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) {
                            console.log('Push notification sent: ', response);
                        }, error: function(error) {
                            console.log('Error sending push notification: ', error);
                        }
                    });
                }
            });
        }

    Verrà registrato un nuovo script insert, che utilizza l'[oggetto gcm][] per inviare una notifica push (il testo inserito) al dispositivo specificato nella richiesta insert.

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

<div class="dev-callout"><b>Nota</b>
    <p>Quando si esegue l'app nell'emulatore, assicurarsi di utilizzare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.</p>
</div>

1.  Riavviare Eclipse, quindi in Package Explorer fare clic con il pulsante destro del mouse sul progetto, scegliere **Properties**, fare clic su **Android**, selezionare **Google APIs** e infine fare clic su **OK**.

    ![][5]

    Il progetto farà quindi riferimento a Google APIs.

2.  Da **Window** selezionare **Android Virtual Device Manager**, quindi selezionare il dispositivo e fare clic su **Edit**.

    ![][6]

3.  Selezionare **Google APIs** in **Target**, quindi fare clic su OK.

    ![][7]

    AVD sarà quindi configurato per l'utilizzo di Google APIs.

4.  Scegliere **Run** dal menu **Run** per avviare l'app.

5.  Digitare testo significativo nell'app, ad esempio *A new Mobile Services task*, quindi fare clic sul pulsante **Add**.

    ![][8]

6.  Una casella nera di notifica verrà visualizzata brevemente nella parte inferiore dello schermo.

    ![][9]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.    ![][27]-->

L'esercitazione è stata completata.

## <a name="next-steps"></a>Passaggi successivi

In questo semplice esempio un utente riceve una notifica push con i dati appena inseriti. Il token di dispositivo utilizzato da GCM viene fornito al servizio mobile dal client nella richiesta. Nell'esercitazione successiva, [Notifiche push per utenti di app][], sarà possibile creare una tabella Devices distinta in cui archiviare i token di dispositivo e sarà possibile inviare una notifica push a tutti i canali archiviati quando si verifica un inserimento.

<!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /it-it/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript"
  [Windows Phone]: /it-it/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone"
  [iOS]: /it-it/develop/mobile/tutorials/get-started-with-push-ios "iOS"
  [Android]: /it-it/develop/mobile/tutorials/get-started-with-push-android "Android"
  [Appcelerator]: /it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-android-get-started-push/ "JavaScript backend"
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Abilitazione di Google Cloud Messaging]: #register
  [Configurazione di Servizi mobili]: #configure
  [Aggiungere notifiche push all'app]: #add-push
  [Aggiornamento degli script per l'invio di notifiche push]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche]: #test
  [Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-android
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [configurazione di Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [oggetto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [8]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [9]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
  [Notifiche push per utenti di app]: /it-it/develop/mobile/tutorials/push-notifications-to-users-android
