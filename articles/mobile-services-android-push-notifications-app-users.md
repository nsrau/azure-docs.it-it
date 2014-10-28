<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Invio di notifiche push agli utenti tramite Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
    <a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>In questo argomento viene estesa l'<a href="/it-it/develop/mobile/tutorials/get-started-with-push-android">esercitazione precedente relativa alle notifiche push</a> mediante l'aggiunta di una nuova tabella per l'archiviazione degli URI di registrazione di Google Cloud Messaging (GCM) che possono quindi essere utilizzati per inviare le notifiche push a pi&ugrave; utenti dell'app per Android. In questa esercitazione un singolo aggiornamento generer&agrave; le notifiche push a tutti i dispositivi registrati ogni volta che viene effettuata un'operazione di inserimento nella tabella ToDoList. Nell'esercitazione precedente, la notifica &egrave; stata inviata solo al dispositivo che ha effettuato l'operazione di inserimento.</p>
</div>

In questa esercitazione vengono descritte le operazioni per aggiornare le notifiche push nell'app:

1.  [Creazione della tabella Registration][]
2.  [Aggiornamento dell'app][]
3.  [Aggiornamento degli script del server][]
4.  [Verifica del comportamento delle notifiche push][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione [Introduzione alle notifiche push][esercitazione precedente relativa alle notifiche push]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione alle notifiche push][esercitazione precedente relativa alle notifiche push].

## <a name="create-table"></a>Creazione di una nuova tabella

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sull'app.

    ![][]

2.  Fare clic sulla scheda **Data** e quindi su **Create**.

    ![][1]

    Verrà visualizzata la finestra di dialogo **Create new table**.

3.  Mantenere l'impostazione predefinita di **Anybody with the application key** per tutte le autorizzazioni, digitare *Registration* in **Table name**, quindi fare clic sul segno di spunta.

    ![][2]

    Verrà creata la tabella **Registration**, in cui sono archiviati gli URI di registrazione utilizzati per l'invio di notifiche push separati dai dati dell'elemento.

Successivamente, si procederà alla modifica dell'app per le notifiche push per archiviare i dati di registrazione in questa nuova tabella anziché nella tabella **TodoItem**.

## <a name="update-app"></a>Aggiornamento dell'app

1.  In Eclipse aprire Package Explorer, fare clic con il pulsante destro del mouse sul pacchetto (nel nodo `src`), scegliere **New**, quindi fare clic su **Class**.

2.  In **Name** digitare `Registration`, quindi fare clic su **Finish**.

    ![][3]

    Verrà creata la nuova classe Registration.

3.  Aprire il file ToDoItem.java, quindi tagliare il codice seguente:

        @com.google.gson.annotations.SerializedName("handle")
        private String mHandle;

        public String getHandle() {
            return mHandle;
        }

        public final void setHandle(String handle) {
            mHandle = handle;
        }

4.  Incollare il codice tagliato nel passaggio precedente nel corpo della classe **Registration** creata in precedenza.

5.  Aggiungere il codice seguente alla classe **Registration**:

        @com.google.gson.annotations.SerializedName("id")
        private int mId;

        /**
         * Returns the item id
         */
        public int getId() {
            return mId;
        }

        /**
         * Sets the item id
         * 
         * @param id : id to set
         */
        public final void setId(int id) {
            mId = id;
        }

6.  Aprire il file **ToDoActivity.java** ed eliminare le righe seguenti nel metodo `addItem`:

        item.setHandle(MyHandler.getHandle());

7.  Individuare la proprietà `mClient` e sostituirla con il codice seguente:

        /**
         * Mobile Service Client reference
         */
        private static MobileServiceClient mClient;

        /**
         * Returns the client reference
         */
        public static MobileServiceClient getClient() {
            return mClient;
        }

8.  Nel file **MyHandler** aggiungere le istruzioni import seguenti:

        import android.util.Log;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  Alla fine del metodo `onRegistered` aggiungere il codice seguente:

        MobileServiceClient client = ToDoActivity.getClient();
        MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
        registrations.insert(registration, new TableOperationCallback<Registration>() {

            public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                if (exception != null) {
                    Log.e("MyHandler", exception.getMessage());
                } else {
                    Log.e("MyHandler", "Registration OK");
                }
            }
        });

L'app è ora aggiornata per il supporto delle notifiche push agli utenti.

## <a name="update-scripts"></a>Aggiornamento degli script del server

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **Registration**.

    ![][4]

2.  In **registration** fare clic sulla scheda **Script** e selezionare **Insert**.

    ![][5]

    Viene visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **Registration**.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registration');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertRegistrationIfNotFound });
            function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

    Lo script verifica se nella tabella **Registration** esiste una registrazione con lo stesso URI. L'operazione di inserimento viene eseguita solo se non viene trovata alcuna registrazione corrispondente, in modo da evitare l'inserimento di record di registrazione duplicati.

4.  Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

    ![][6]

5.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    sendNotifications(item.text);

                }
            });

        function sendNotifications(item_text) {
            var registrationTable = tables.getTable('Registration');
            registrationTable.read({
                success: function(registrations) {
                    registrations.forEach(function(registration) {
                        push.gcm.send(registration.handle, item_text, {
                            success: function(response) {
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
        }

    }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutte le registrazioni archiviate nella tabella **Registration**.

## <a name="test-app"></a>Test dell'app

1.  In Eclipse, dal menu **Run** scegliere **Run** per avviare l'app.

2.  Digitare testo significativo nell'app, ad esempio *A new Mobile Services task*, quindi fare clic sul pulsante **Add**.

3.  Una casella nera di notifica verrà visualizzata brevemente nella parte inferiore dello schermo.

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

L'esercitazione è stata completata.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'utilizzo delle notifiche push è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili][]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Come utilizzare la libreria client di Android per Servizi mobili][]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Phone]: /it-it/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone"
  [iOS]: /it-it/develop/mobile/tutorials/push-notifications-to-users-ios "iOS"
  [Android]: /it-it/develop/mobile/tutorials/push-notifications-to-users-android "Android"
  [esercitazione precedente relativa alle notifiche push]: /it-it/develop/mobile/tutorials/get-started-with-push-android
  [Creazione della tabella Registration]: #create-table
  [Aggiornamento dell'app]: #update-app
  [Aggiornamento degli script del server]: #update-scripts
  [Verifica del comportamento delle notifiche push]: #test-app
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-android
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-android
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Come utilizzare la libreria client di Android per Servizi mobili]: /it-it/develop/mobile/how-to-guides/work-with-android-client-library
