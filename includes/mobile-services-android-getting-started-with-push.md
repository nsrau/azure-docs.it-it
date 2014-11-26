1.  Aprire il file `AndroidManifest.xml`. Nel codice per i due passaggi successivi sostituire *`**my_app_package**`* con il nome del pacchetto dell'app per il progetto, ovvero il valore `package` dell'attributo del tag `manifest`.

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

4.  Scaricare e decomprimere [Mobile Services Android SDK][Mobile Services Android SDK], aprire la cartella **notifications**, copiare il file **notifications-1.0.1.jar** nella cartella *libs* del progetto Eclipse, quindi aggiornare la cartella *libs*.

    <div class="dev-callout"><b>Nota</b>
<p>&Egrave; possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive di SDK.</p>
</div>

5.  Aprire il file *ToDoItemActivity.java*, quindi aggiungere l'istruzione import seguente:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  Aggiungere la variabile privata seguente alla classe, dove *`<PROJECT_NUMBER>`* è il numero di progetto assegnato da Google all'app nella procedura precedente:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  Nel metodo **onCreate**, prima della creazione di istanze di MobileServiceClient, aggiungere il codice seguente per la registrazione del gestore delle notifiche per il dispositivo:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    La classe MyHandler.class cui viene fatto riferimento in questo codice verrà definita in seguito.

8.  In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi scegliere **New** e fare clic su **Class**.

9.  In **Nome** digitare `MyHandler`, in **Superclass** digitare `com.microsoft.windowsazure.notifications.NotificationsHandler`, quindi fare clic su **Fine**

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    Verrà creata la nuova classe MyHandler.

10. Aggiungere le istruzioni import seguenti per la classe `MyHandler`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. Quindi, aggiungere i membri seguenti per la classe `MyHandler`:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. Nella classe `MyHandler` aggiungere il metodo seguente per eseguire l'override del metodo **onRegistered**: che registra il dispositivo nell'hub di notifica del servizio mobile.

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

13. Nella classe `MyHandler` aggiungere il codice seguente per eseguire l'override del metodo **onReceive**, che determina la visualizzazione della notifica al momento della ricezione.

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



  [Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
   
