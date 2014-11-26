<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push Notifications to Users (WP8)" pageTitle="Push notifications to users (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Windows Phone app." metaCanonical="" services="" documentationCenter="" title="Push notifications to users by using Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Invio di notifiche push agli utenti tramite Servizi mobili

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>

In questo argomento viene estesa l'[esercitazione sulle notifiche push precedente][esercitazione sulle notifiche push precedente] tramite l'aggiunta di una nuova tabella per l'archiviazione degli URI di canale del servizio di notifica Push di Microsoft. Questi canali possono essere utilizzati per inviare notifiche push agli utenti dell'app per Windows Phone 8.

In questa esercitazione vengono descritte le operazioni per aggiornare le notifiche push nell'app:

1.  [Creazione della tabella Channel][Creazione della tabella Channel]
2.  [Aggiornamento dell'app][Aggiornamento dell'app]
3.  [Aggiornamento degli script del server][Aggiornamento degli script del server]
4.  [Verifica del comportamento delle notifiche push][Verifica del comportamento delle notifiche push]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione [Introduzione alle notifiche push][esercitazione sulle notifiche push precedente]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione alle notifiche push][esercitazione sulle notifiche push precedente].

## <a name="create-table"></a>Creazione di una nuova tabella

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][0]

2.  Fare clic sulla scheda **Data** e quindi su **Create**.

    ![][1]

    Verrà visualizzata la finestra di dialogo **Create new table**.

3.  Mantenere l'impostazione predefinita di **Anybody with the application key** per tutte le autorizzazioni, digitare *Channel* in **Table name**, quindi fare clic sul segno di spunta.

    ![][2]

    Verrà creata la tabella **Channel**, in cui sono archiviati gli URI di canale utilizzati per l'invio di notifiche push separati dai dati dell'elemento.

Successivamente, si procederà alla modifica dell'app per le notifiche push per archiviare i dati in questa nuova tabella anziché nella tabella **TodoItem**.

## <a name="update-app"></a>Aggiornamento dell'app

1.  In Visual Studio 2012 Express per Windows Phone aprire il progetto creato durante l'esercitazione [Introduzione alle notifiche push][esercitazione sulle notifiche push precedente], aprire il file MainPage.xaml.cs e rimuovere la proprietà **Channel** dalla classe **TodoItem**. L'aspetto dovrebbe risultare simile al seguente:

        public class TodoItem
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

2.  Sostituire il metodo del gestore di eventi **ButtonSave\_Click** con la versione originale di questo metodo, come illustrato di seguito:

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3.  Aggiungere il codice seguente per creare una nuova classe **Channel**:

        public class Channel
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "uri")]
            public string Uri { get; set; }
        }

4.  Aprire il file App.xaml.cs e sostituire il metodo **AcquirePushChannel** con il codice seguente:

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
           var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
           channelTable.InsertAsync(channel);
        }

    Questo codice consente di inserire un canale nella tabella Channel.

## <a name="update-scripts"></a>Aggiornamento degli script del server

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **Channel**.

    ![][3]

2.  In **channel** fare clic sulla scheda **Script** e selezionare **Insert**.

    ![][4]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **Channel**.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

            function insert(item, user, request) {
                var channelTable = tables.getTable('Channel');
                channelTable
                    .where({ uri: item.uri })
                    .read({ success: insertChannelIfNotFound });
                function insertChannelIfNotFound(existingChannels) {
                    if (existingChannels.length > 0) {
                        request.respond(200, existingChannels[0]);
                    } else {
                        request.execute();
                    }
                }
            }

    Lo script verifica se nella tabella **Channel** esiste un canale con lo stesso URI. L'operazione di inserimento viene eseguita solo se non viene trovato alcun canale corrispondente, in modo da evitare l'inserimento di record di canale duplicati.

4.  Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

    ![][5]

5.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var channelTable = tables.getTable('Channel');
                channelTable.read({
                    success: function(channels) {
                        channels.forEach(function(channel) {
                            push.mpns.sendFlipTile(channel.uri, {
                                title: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutti i canali archiviati nella tabella **Channel**.

## <a name="test-app"></a>Test dell'app

1.  In Visual Studio scegliere **Distribuisci soluzione** dal menu **Compila**.

2.  Toccare il riquadro **TodoList** o **hello push** per avviare l'app.

    ![][6]

3.  Nell'app immettere il testo "hello push again" nella casella di testo, quindi fare clic su **Salva**.

    ![][7]

    Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto.

4.  Premere **Start** per tornare al menu corrispondente.

    ![][8]

    Si noti che l'applicazione ha ricevuto la notifica push e che il titolo del riquadro è ora **hello push**.

5.  (Facoltativo) Eseguire l'app contemporaneamente in due dispositivi e ripetere il passaggio precedente.

    La notifica verrà inviata a tutte le istanze dell'app in esecuzione.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'utilizzo delle notifiche push è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

<!-- anchors -->  

  [esercitazione sulle notifiche push precedente]: /it-it/develop/mobile/tutorials/get-started-with-push-wp8
  [Creazione della tabella Channel]: #create-table
  [Aggiornamento dell'app]: #update-app
  [Aggiornamento degli script del server]: #update-scripts
  [Verifica del comportamento delle notifiche push]: #test-app
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
  [3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
  [4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
  [5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
  [7]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
  [8]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-wp8
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
