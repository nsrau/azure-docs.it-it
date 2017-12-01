1. Nel progetto di **app** aprire il file `AndroidManifest.xml`. Aggiungere il codice seguente dopo il tag di apertura `application` :

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Aprire il file `ToDoActivity.java` e apportare le modifiche seguenti:

    - Aggiungere l'istruzione import:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Modificare la definizione di `MobileServiceClient` da **private** a **private static** in modo che sia simile alla seguente:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Aggiungere il metodo `registerPush`:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Aggiornare il metodo **onCreate** della classe `ToDoActivity`. Assicurarsi di aggiungere il codice dopo la creazione di un'istanza di `MobileServiceClient`.

        ```java
        registerPush();
        ```

3. Aggiungere una nuova classe per gestire le notifiche. In Esplora progetti aprire i nodi **app** > **java** > **spazio-dei-nomi-progetto-corrente** e fare clic con il pulsante destro del mouse sul nodo del nome del pacchetto. Fare clic su **Nuovo** e quindi su **Java Class** (Classe Java). In Nome digitare `ToDoMessagingService` e fare clic su OK. Sostituire quindi la dichiarazione di classe con:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Aggiungere un'altra classe per gestire gli aggiornamenti dei token. Creare una classe Java `ToDoInstanceIdService` e sostituire la dichiarazione di classe con:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

L'app è ora aggiornata per il supporto delle notifiche push.
