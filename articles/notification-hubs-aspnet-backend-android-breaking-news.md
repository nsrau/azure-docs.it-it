<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-ios" urlDisplayName="Breaking News" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda"/>

# Utilizzo di Hub di notifica per inviare le ultime notizie

<div class="dev-center-tutorial-selector sublanding">       
    <a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >App Windows universali</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
    <a href="/it-it/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android" class="current">Android</a>
</div>

In questo argomento viene illustrato come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app per Android. Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app nelle quali le notifiche devono essere inviate a gruppi di utenti che hanno dichiarato un interesse, ad esempio lettori di feed RSS, app per fan di musica e così via.

È possibile abilitare gli scenari di trasmissione includendo uno o più *tag* durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per altre informazioni sui tag, vedere l'articolo relativo alle [linee guida per gli hub di notifica][linee guida per gli hub di notifica].

In questa esercitazione vengono descritte le operazioni di base per abilitare questo scenario:

1.  [Aggiunta della selezione delle categorie all'app][Aggiunta della selezione delle categorie all'app]
2.  [Registrazione per le notifiche][Registrazione per le notifiche]
3.  [Invio di notifiche dal back-end][Invio di notifiche dal back-end]
4.  [Esecuzione dell'app e generazione di notifiche][Esecuzione dell'app e generazione di notifiche]

Questo argomento si basa sull'app creata nell'esercitazione [Introduzione ad Hub di notifica][Introduzione ad Hub di notifica]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ad Hub di notifica][Introduzione ad Hub di notifica].

## <a name="adding-categories"></a>Aggiunta della selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente all'attività principale esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione nell'hub di notifica con le categorie selezionate come tag.

1.  Aprire il file res/layout/activity\_main.xml e sostituire il contenuto con il seguente:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2.  Aprire il file res/values/strings.xml e aggiungere le righe seguenti:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    A questo punto il layout grafico del file main\_activity.xml dovrebbe essere simile al seguente:

    ![](./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG)

3.  Creare una classe **Notifications** nello stesso pacchetto della classe **MainActivity**.

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;        

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId) {
                this.context = context;
                this.senderId = senderId;

                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
                            hub.register(regid, categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }

                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Questa classe usa l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltri i metodi per effettuare la registrazione per queste categorie.

4.  Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with listen access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuta in precedenza.

    <div class="dev-callout"><strong>Nota</strong> 
    <p>Poich&eacute; le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completa viene usata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.</p>
</div>

5.  Nella classe **MainActivity** rimuovere i campi privati per **NotificationHub** e **GoogleCloudMessaging** e aggiungere un campo per **Notifications**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

6.  A questo punto, nel metodo **onCreate** rimuovere l'inizializzazione del campo **hub** e il metodo **registerWithNotificationHubs**. Aggiungere quindi le righe seguenti, che inizializzano un'istanza della classe **Notifications**. Il metodo deve contenere le righe seguenti:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID);
        }

7.  Aggiungere quindi il metodo seguente:

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Questo metodo crea un elenco di categorie e usa la classe **Notifications** per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Se le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

L'app può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie.

## <a name="register"></a>Registrazione per le notifiche

Questa procedura consente di effettuare la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nell'archiviazione locale.

<div class="dev-callout"><strong>Nota</strong> 
    <p>Poich&eacute; il valore di registrationId assegnato da Google Cloud Messaging (GCM) pu&ograve; cambiare in qualsiasi momento, &egrave; necessario ripetere la registrazione per le notifiche di frequente per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite di frequente, oltre una volta al giorno, &egrave; possibile ignorare la registrazione per conservare la larghezza di banda qualora sia trascorso meno di un giorno dalla registrazione precedente.</p>
</div>

1.  Aggiungere il codice seguente alla classe **Notifications**:

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

    Verranno restituite le categorie definite nella classe.

2.  Aggiungere quindi questo codice alla fine del metodo **onCreate** nella classe **MainActivity**:

        notifications.subscribeToCategories(notifications.retrieveCategories());

    In questo modo, ogni volta che l'app viene avviata vengono recuperate le categorie dall'archiviazione locale e viene richiesta una registrazione per queste categorie. Il metodo **InitNotificationsAsync** è stato creato nell'ambito dell'esercitazione [Introduzione ad Hub di notifica], ma non è necessario in questo argomento.

3.  Aggiungere quindi il metodo seguente a **MainActivity**:

        @Override
        protected void onStart() {
            super.onStart();

            Set<String> categories = notifications.retrieveCategories();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            world.setChecked(categories.contains("world"));
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            politics.setChecked(categories.contains("politics"));
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            business.setChecked(categories.contains("business"));
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            technology.setChecked(categories.contains("technology"));
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            science.setChecked(categories.contains("science"));
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            sports.setChecked(categories.contains("sports"));
        }

    L'attività principale viene aggiornata in base allo stato delle categorie salvate in precedenza.

Ora l'app è completa e può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie. Verrà ora definito un back-end per l'invio delle notifiche delle categorie all'app.

## <a name="send"></a><span class="short-header">Invio di notifiche</span>Invio di notifiche dal back-end

[WACOM.INCLUDE [notification-hubs-back-end][notification-hubs-back-end]]

## <a name="test-app"></a>Esecuzione dell'app e generazione di notifiche

1.  In Eclipse compilare l'app e avviarla in un dispositivo o emulatore.

    Si noti che l'interfaccia utente dell'app fornisce un set di interruttori che consentono di scegliere le categorie per le quali effettuare la sottoscrizione.

2.  Abilitare uno o più interruttori di categorie e quindi fare clic su **Subscribe**.

    L'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica. Le categorie registrate vengono restituite e visualizzate in una finestra di dialogo.

3.  Inviare una nuova notifica dal back-end in uno dei modi seguenti:

    -   **App console .NET:** avviare l'app console.

    -   **Java/PHP:** eseguire l'app o lo script.

    Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione si è appreso a trasmettere le ultime novità in base alle categorie. Per informazioni su altri scenari di Hub di notifica avanzati, provare a completare le esercitazioni seguenti:

-   [Utilizzo di Hub di notifica per la trasmissione di notizie localizzate][Utilizzo di Hub di notifica per la trasmissione di notizie localizzate]

    Altre informazioni su come espandere l'app relativa alle ultime novità per abilitare l'invio di notifiche localizzate.

-   [Utilizzo di Hub di notifica per inviare notifiche agli utenti][Utilizzo di Hub di notifica per inviare notifiche agli utenti]

    Altre informazioni su come eseguire il push di notifiche a utenti autenticati specifici. Si tratta di un'ottima soluzione per inviare le notifiche solo a utenti specifici.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs.-->

  [App Windows universali]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/ "Windows Universal"
  [Windows Phone]: /it-it/documentation/articles/notification-hubs-windows-phone-send-breaking-news/ "Windows Phone"
  [iOS]: /it-it/documentation/articles/notification-hubs-ios-send-breaking-news/ "iOS"
  [Android]: /it-it/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/ "Android"
  [linee guida per gli hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
  [Aggiunta della selezione delle categorie all'app]: #adding-categories
  [Registrazione per le notifiche]: #register
  [Invio di notifiche dal back-end]: #send
  [Esecuzione dell'app e generazione di notifiche]: #test-app
  [Introduzione ad Hub di notifica]: /it-it/documentation/articles/notification-hubs-android-get-started/
   
  [notification-hubs-back-end]: ../includes/notification-hubs-back-end.md
  [Utilizzo di Hub di notifica per la trasmissione di notizie localizzate]: /it-it/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [Utilizzo di Hub di notifica per inviare notifiche agli utenti]: /it-it/manage/services/notification-hubs/notify-users
