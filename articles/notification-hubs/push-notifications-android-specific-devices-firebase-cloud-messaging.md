---
title: Eseguire il push di notifiche a dispositivi Android specifici con Hub di notifica di Azure e Google Firebase Cloud Messaging | Microsoft Docs
description: In questa esercitazione si apprenderà come usare Hub di notifica di Azure e Google Firebase Cloud Messaging per effettuare il push di notifiche a dispositivi Android (FCM).
services: notification-hubs
documentationcenter: android
author: jwargo
manager: patniko
editor: spelluru'
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: dd11696ea4a90e61408a03400fa3315ee7eaff5f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653498"
---
# <a name="tutorial-push-notifications-to-specific-android-devices-using-azure-notification-hubs-and-google-firebase-cloud-messaging-fcm"></a>Esercitazione: Eseguire il push di notifiche a dispositivi Android specifici con Hub di notifica di Azure e Google Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app per Android. Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app nelle quali le notifiche devono essere inviate a gruppi di utenti che hanno dichiarato un interesse, ad esempio lettori di feed RSS, app per fan di musica e così via.

È possibile abilitare gli scenari di trasmissione includendo uno o più *tag* durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per altre informazioni sui tag, vedere [Espressioni di routing e tag per Hub di notifica](notification-hubs-tags-segment-push-message.md).

In questa esercitazione vengono completate le azioni seguenti:

> [!div class="checklist"]
> * Aggiungere la selezione delle categorie all'app per dispositivi mobili.
> * Registrarsi per le notifiche con i tag.
> * Inviare notifiche con tag.
> * Testare l'app

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione si basa sull'app creata nell'[Esercitazione: Inviare notifiche push a dispositivi Android usando gli Hub di notifica di Azure e Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md). Prima di iniziare questa esercitazione, completare l'[Esercitazione: Inviare notifiche push a dispositivi Android usando gli Hub di notifica di Azure e Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="add-category-selection-to-the-app"></a>Aggiungere la selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente all'attività principale esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione nell'hub di notifica con le categorie selezionate come tag.

1. Aprire il file `res/layout/activity_main.xml file` e sostituirne il contenuto con quanto segue:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
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
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Aprire il file `res/values/strings.xml` e aggiungere le righe seguenti:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    Il layout grafico del file `main_activity.xml` dovrà essere simile a quello nell'immagine seguente:

    ![][A1]
3. Creare una classe `Notifications` nello stesso pacchetto della classe `MainActivity`.

    ```java
    import java.util.HashSet;
    import java.util.Set;
    import java.util.concurrent.TimeUnit;
    
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    
    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private FirebaseInstanceId fcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;
        public static String FCM_token = "";
        private static final String TAG = "Notifications";
    
        public Notifications(Context context, String hubName, String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;
    
            fcm = FirebaseInstanceId.getInstance();
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }
    
        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }
    
        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }
    
        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() {
                            @Override
                            public void onSuccess(InstanceIdResult instanceIdResult) {
                                FCM_token = instanceIdResult.getToken();
                                Log.d(TAG, "FCM Registration Token: " + FCM_token);
                            }
                        });
    
                        TimeUnit.SECONDS.sleep(1);
    
                        String templateBodyFCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    
                        hub.registerTemplate(FCM_token,"simpleFCMTemplate", templateBodyFCM,
                                categories.toArray(new String[categories.size()]));
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
    ```

    Questa classe utilizza l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltre i metodi per effettuare la registrazione per queste categorie.
4. Nella classe `MainActivity`, aggiungere un campo per `Notifications`:

    ```java
    private Notifications notifications;
    ```
5. Quindi, aggiornare il metodo `onCreate` come illustrato nel codice seguente. Registrare con Hub di notifica nel metodo **subscribeToCategories** della classe **Notifiche**. 

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;

        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
        notifications = new Notifications(this, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);
        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Verificare che il nome dell'hub e la stringa di connessione siano impostati correttamente nella classe NotificationSettings.

    > [!NOTE]
    > Poiché le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completa viene utilizzata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.

6. Quindi, aggiungere le importazioni seguenti:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    import android.view.View;
    ```
7. Aggiungere il metodo `subscribe` seguente per gestire l'evento clic del pulsante sottoscrivi:

    ```java
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
    ```

    Questo metodo crea un elenco di categorie e usa la classe `Notifications` per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Se le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

L'app può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie.

## <a name="register-for-notifications"></a>Registrazione per le notifiche

Questa procedura consente di effettuare la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nella risorsa di archiviazione locale.

1. Verificare che il codice seguente sia alla fine del metodo `onCreate` nella classe `MainActivity`:

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Con questo codice, ogni volta che l'app viene avviata vengono recuperate le categorie dall'archiviazione locale e viene richiesta una registrazione per tali categorie.
2. Aggiornare quindi il metodo `onStart()` della classe `MainActivity` come indicato di seguito:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

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
    ```

    Il codice aggiorna l'attività principale in base allo stato delle categorie salvate in precedenza.

Ora l'app è completa e può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie. Definire quindi un back-end per l'invio delle notifiche delle categorie all'app.

## <a name="send-tagged-notifications"></a>Inviare notifiche con tag

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testare l'app

1. In Android Studio eseguire l'app sul dispositivo Android o un emulatore. L'interfaccia utente dell'app fornisce un insieme di interruttori che permettono di scegliere le categorie per le quali registrarsi.
2. Abilitare uno o più interruttori di categorie e quindi fare clic su **Subscribe**. L'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica. Le categorie registrate vengono restituite e visualizzate in notifica di tipo avviso popup.

    ![Abbonarsi alle categorie](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Eseguire l'applicazione console .NET, che consente di inviare le notifiche per ogni categoria. Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

    ![Notifiche relative alle notizie di tecnologia](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione verranno inviate notifiche di trasmissione ai dispositivi Android specifici che hanno effettuato la registrazione alle categorie. Per informazioni sulle procedure per eseguire il push di notifiche a utenti specifici, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
>[Eseguire il push di notifiche a utenti specifici](push-notifications-android-specific-users-firebase-cloud-messaging.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
