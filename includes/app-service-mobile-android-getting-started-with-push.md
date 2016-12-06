1. Nel progetto di **app** aprire il file `AndroidManifest.xml`. Nel codice per i due passaggi successivi sostituire *`**my_app_package**`* con il nome del pacchetto dell'app per il progetto, ovvero il valore dell'attributo `package` del tag `manifest`.
2. Aggiungere le nuove autorizzazioni seguenti dopo l'elemento `uses-permission` esistente:
   
        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. Aggiungere il codice seguente dopo il tag di apertura `application` :
   
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. Aprire il file *ToDoActivity.java*, quindi aggiungere l'istruzione import seguente:
   
        import com.microsoft.windowsazure.notifications.NotificationsManager;
5. Aggiungere la variabile privata seguente alla classe: sostituire *`<PROJECT_NUMBER>`* con il numero di progetto assegnato da Google all'app nella procedura precedente:
   
        public static final String SENDER_ID = "<PROJECT_NUMBER>";
6. Modificare la definizione di *MobileServiceClient* da **private** a **public static**, in modo che sia simile alla seguente:
   
        public static MobileServiceClient mClient;
7. Successivamente, è necessario aggiungere una nuova classe per gestire le notifiche. In Project Explorer (Esplora progetti) aprire i nodi **src** => **main** => **java** e fare clic con il pulsante destro del mouse sul nodo del nome del pacchetto, quindi scegliere **New** (Nuovo) e infine **Java Class** (Classe Java).
8. In **Name** (Nome) digitare `MyHandler`, quindi fare clic su **OK**.

    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


1. Nel file MyHandler sostituire la dichiarazione di classe con
   
        public class MyHandler extends NotificationsHandler {
2. Aggiungere le istruzioni import seguenti per la classe `MyHandler` :
   
       import com.microsoft.windowsazure.notifications.NotificationsHandler;
       import android.app.NotificationManager;
       import android.app.PendingIntent;
       import android.content.Context;
       import android.content.Intent;
       import android.os.AsyncTask;
       import android.os.Bundle;
       import android.support.v4.app.NotificationCompat;
3. Aggiungere quindi questo membro alla classe `MyHandler` :
   
       public static final int NOTIFICATION_ID = 1;
4. Nella classe `MyHandler` aggiungere il codice seguente per eseguire l'override del metodo **onRegistered** che registra il dispositivo nell'hub di notifica del servizio mobile.
   
       @Override
       public void onRegistered(Context context,  final String gcmRegistrationId) {
           super.onRegistered(context, gcmRegistrationId);
   
           new AsyncTask<Void, Void, Void>() {
   
               protected Void doInBackground(Void... params) {
                   try {
                       ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                       return null;
                   }
                   catch(Exception e) {
                       // handle error                
                   }
                   return null;              
               }
           }.execute();
       }
5. Nella classe `MyHandler` aggiungere il codice seguente per eseguire l'override del metodo **onReceive** , che determina la visualizzazione della notifica al momento della ricezione.
   
       @Override
       public void onReceive(Context context, Bundle bundle) {
               String msg = bundle.getString("message");
   
               PendingIntent contentIntent = PendingIntent.getActivity(context,
                       0, // requestCode
                       new Intent(context, ToDoActivity.class),
                       0); // flags
   
               Notification notification = new NotificationCompat.Builder(context)
                       .setSmallIcon(R.drawable.ic_launcher)
                       .setContentTitle("Notification Hub Demo")
                       .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                       .setContentText(msg)
                       .setContentIntent(contentIntent)
                       .build();
   
               NotificationManager notificationManager = (NotificationManager)
                       context.getSystemService(Context.NOTIFICATION_SERVICE);
               notificationManager.notify(NOTIFICATION_ID, notification);
       }
6. Nel file TodoActivity.java, aggiornare il metodo **onCreate** della classe *ToDoActivity* per registrare la classe del gestore di notifica. Assicurarsi di aggiungere il codice dopo la creazione di un'istanza di *MobileServiceClient* .

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    L'app è ora aggiornata per il supporto delle notifiche push.


<!--HONumber=Nov16_HO3-->


