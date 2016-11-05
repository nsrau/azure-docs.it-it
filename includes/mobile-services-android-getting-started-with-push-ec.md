1. Nel progetto di app aprire il file `AndroidManifest.xml`. Nel codice per i due passaggi successivi sostituire *`**my_app_package**`* con il nome del pacchetto dell'app per il progetto, ovvero il valore dell'attributo `package` del tag `manifest`. 
2. Aggiungere le nuove autorizzazioni seguenti dopo l'elemento `uses-permission` esistente:
   
        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. Aggiungere il codice seguente dopo il tag di apertura `application`:
   
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. Scaricare e decomprimere [Mobile Services SDK per Android], aprire la cartella **notifications**, copiare il file **notifications-1.0.1.jar** nella cartella *libs* del progetto Eclipse, quindi aggiornare la cartella *libs*.
   
   > [!NOTE]
   > È possibile che i numeri presenti alla fine del nome del file cambino nelle versioni successive dell'SDK.
   > 
   > 
5. Aprire il file *ToDoItemActivity.java*, quindi aggiungere l'istruzione import seguente:
   
       import com.microsoft.windowsazure.notifications.NotificationsManager;
6. Aggiungere la variabile privata seguente alla classe: sostituire *`<PROJECT_NUMBER>`* con il numero di progetto assegnato da Google all'app nella procedura precedente:
   
        public static final String SENDER_ID = "<PROJECT_NUMBER>";
7. Modificare la definizione di *MobileServiceClient* da **private** a **public static**, in modo che sia simile alla seguente:
   
        public static MobileServiceClient mClient;
8. Successivamente, è necessario aggiungere una nuova classe per gestire le notifiche. In Package Explorer fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), quindi scegliere **New** e fare clic su **Class**.
9. In **Name** digitare `MyHandler`, in **Superclass** digitare `com.microsoft.windowsazure.notifications.NotificationsHandler` e quindi fare clic su **Finish**.
   
   ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)
   
   Verrà creata la nuova classe MyHandler.
10. Aggiungere le istruzioni import seguenti per la classe `MyHandler`:
    
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
11. Quindi, aggiungere i membri seguenti per la classe `MyHandler`:
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;
12. Nella classe `MyHandler` aggiungere il codice seguente per eseguire l'override del metodo **onRegistered** che registra il dispositivo nell'hub di notifica del servizio mobile.
    
        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
    
            new AsyncTask<Void, Void, Void>() {
    
                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
                        return null;
                    }
                    catch(Exception e) { 
                        // handle error                
                    }
                    return null;              
                }
            }.execute();
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
14. Nel file TodoActivity.java, aggiornare il metodo **onCreate** della classe *ToDoActivity* per registrare la classe del gestore di notifica. Assicurarsi di aggiungere il codice dopo la creazione di un'istanza di *MobileServiceClient*.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    L'app è ora aggiornata per il supporto delle notifiche push.

<!-- URLs. -->
[Mobile Services SDK per Android]: http://aka.ms/Iajk6q

<!---HONumber=August15_HO6-->