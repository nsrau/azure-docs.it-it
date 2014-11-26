<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Notify Users" pageTitle="Notify Users of your mobile service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your mobile service by using Notification Hubs" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Utilizzo di Hub di notifica per inviare notifiche agli utenti
=============================================================

[Servizi mobili](/it-it/manage/services/notification-hubs/notify-users "Servizi mobili") [ASP.NET](/it-it/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

In questa esercitazione viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un utente specifico dell'app su un dispositivo specifico. Per autenticare i client e generare le notifiche, viene utilizzato un back-end di Servizi mobili di Azure. Questa esercitazione si basa sull'hub di notifica creato nell'esercitazione precedente **Introduzione ad Hub di notifica**. Il codice di registrazione della notifica viene spostato dal client al servizio back-end. In tal modo la registrazione verrà completata solo dopo la corretta autenticazione del client da parte del servizio e le credenziali dell'hub di notifica non verranno distribuite con l'app client. Il servizio controlla inoltre i tag richiesti durante la registrazione.

In questa esercitazione vengono descritte le operazioni di base seguenti:

-   [Aggiornamento del servizio mobile per la registrazione per le notifiche](#register-notification)
-   [Aggiornamento dell'app per la registrazione dell'accesso e delle richieste](#update-app)
-   [Aggiornamento del servizio mobile per l'invio delle notifiche](#send-notifications)

Prerequisiti
------------

Prima di iniziare questa esercitazione, è necessario completare quelle seguenti:

-   **Introduzione ad Hub di notifica** ([Windows Store C\#](/it-it/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/it-it/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/it-it/manage/services/notification-hubs/get-started-notification-hubs-android)).

-   **Introduzione all'autenticazione in Servizi mobili** ([Windows Store C\#](/it-it/develop/mobile/tutorials/get-started-with-users-dotnet/)/[iOS](/it-it/develop/mobile/tutorials/get-started-with-users-ios/)/[Android](/it-it/develop/mobile/tutorials/get-started-with-users-android/))

Questa esercitazione si basa sull'app e sull'hub di notifica creati in **Introduzione ad Hub di notifica**. Sfrutta anche il servizio mobile autenticato configurato in **Introduzione all'autenticazione in Servizi mobili**.

**Nota**

Per impostazione predefinita, nell'esercitazione **Introduzione all'autenticazione in Servizi mobili** viene utilizzata l'autenticazione tramite Facebook. In questa esercitazione non è possibile utilizzare l'autenticazione con account Microsoft perché due app di Windows Store non possono condividere un'unica registrazione a Live Connect. Per utilizzare l'autenticazione con account Microsoft, è necessario registrare il servizio mobile e l'hub di notifica per la stessa app in Live Connect.

Registrazione per le notificheAggiornamento del servizio mobile per la registrazione per le notifiche
-----------------------------------------------------------------------------------------------------

Poiché la registrazione per le notifiche deve essere completata solo dopo la corretta autenticazione di un client da parte di un servizio, per eseguire tale operazione viene utilizzata un'API personalizzata definita nel servizio mobile. Tale API viene chiamata da un client autenticato per richiedere la registrazione per le notifiche. In questa sezione verrà aggiornato il servizio mobile autenticato definito al termine dell'esercitazione **Introduzione all'autenticazione in Servizi mobili**.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) fare clic su **Service Bus**, sullo spazio dei nomi, su **Notification Hubs**, quindi selezionare l'hub di notifica e infine fare clic su **Connection Information**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png)

2.  Prendere nota del nome dell'hub di notifica e copiare la stringa di connessione per **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png)

    La stringa di connessione, unitamente al nome dell'hub di notifica, verranno utilizzati per effettuare la registrazione e inviare le notifiche.

3.  Sempre nel portale di gestione fare clic su **Mobile Services** e quindi sull'app.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png)

4.  Fare clic sulla scheda **API** e quindi su **Create a custom API**.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png)

     Verrà visualizzata la finestra di dialogo **Create a new custom API**.

5.  Digitare *register\_notifications* in **API name**, selezionare **Only Authenticated Users** per **POST Permissions**, quindi fare clic sul pulsante del segno di spunta.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png)

	Verrà creata l'API con cui viene richiesto agli utenti di eseguire l'autenticazione prima di effettuare la chiamata tramite il metodo HTTP POST. Non è necessario impostare gli altri metodi HTTP dal momento che non verranno implementati.

1.  Fare clic sulla nuova voce **register\_notifications** nella tabella dell'API.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png)

2.  Fare clic sulla scheda **Script** e sostituire il codice esistente con quello riportato di seguito:

         exports.post = function(request, response) {

             // Create a notification hub instance.
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
                 '<FULL_SAS_CONNECTION_STRING>');
            
             // Get the registration info that we need from the request. 
             var platform = request.body.platform;
             var userId = request.user.userId;
             var installationId = request.header('X-ZUMO-INSTALLATION-ID');
                
             // Function called when registration is completed.
             var registrationComplete = function(error, registration) {
                 if (!error) {
                     // Return the registration.
                     response.send(200, registration);
                 } else {
                     response.send(500, 'Registration failed!');
                 }
             }
             // Function called to log errors.
             var logErrors = function(error) {
                 if (error) {
                     console.error(error)
                 }
             }
             // Get existing registrations.
             hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
                 var firstRegistration = true;
                 if (existingRegs.length > 0) {
                      for (var i = 0; i < existingRegs.length; i++) {
                         if (firstRegistration) {
                             // Update an existing registration.
                             if (platform === 'win8') {
                                 existingRegs[i].ChannelUri = request.body.channelUri;                        
                                 hub.updateRegistration(existingRegs[i], registrationComplete);                        
                             } else if (platform === 'ios') {
                                 existingRegs[i].DeviceToken = request.body.deviceToken;
                                 hub.updateRegistration(existingRegs[i], registrationComplete);
                             } else {
                                 response.send(500, 'Unknown client.');
                             }
                             firstRegistration = false;
                         } else {
                             // We shouldn't have any extra registrations; delete if we do.
                             hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
                         }
                     }
                 } else {
                     // Create a new registration.
                     if (platform === 'win8') {                
                         hub.wns.createNativeRegistration(request.body.channelUri, 
                         [userId, installationId], registrationComplete);
                     } else if (platform === 'ios') {
                         hub.apns.createNativeRegistration(request.body.deviceToken, 
                         [userId, installationId], registrationComplete);
                     } else {
                         response.send(500, 'Unknown client.');
                     }
                 }
             });
         }

    Questo codice ottiene le informazioni su piattaforma e ID dispositivo dal corpo del messaggio. Tali dati, unitamente all'ID installazione dell'intestazione della richiesta e all'ID utente dell'utente collegato, verranno utilizzate per aggiornare una registrazione, se esistente, oppure per crearne una nuova. Questa registrazione viene contrassegnata con l'ID utente e l'ID installazione.

3.  Aggiornare lo script in modo da sostituire *`<NOTIFICATION_HUB_NAME>`* e *`<FULL_SAS_CONNECTION_STRING>`* con i valori per l'hub di notifica, quindi fare clic su **Salva**.

    **Nota**

    Assicurarsi di utilizzare **DefaultFullSharedAccessSignature** per *`<FULL_SAS_CONNECTION_STRING>`*. Questa attestazione consentirà al metodo API personalizzato di creare e aggiornare le registrazioni.

Aggiornamento dell'appAggiornamento dell'app per la registrazione dell'accesso e delle richieste
------------------------------------------------------------------------------------------------

È quindi necessario aggiornare l'app TodoList in modo che richieda la registrazione per le notifiche chiamando la nuova API personalizzata.

1.  Attenersi alla procedura riportata in una delle versioni seguenti della pagina relativa all'**utilizzo di un servizio mobile per registrare l'utente corrente per le notifiche push**, a seconda della piattaforma client in uso:

    -   [Versione per Windows Store C\#](/it-it/manage/services/notification-hubs/register-users-mobile-services-dotnet)
    -   [Versione per iOS](/it-it/manage/services/notification-hubs/register-users-ios)

2.  Eseguire l'app aggiornata, accedere tramite Facebook, quindi verificare che venga visualizzato l'ID di registrazione assegnato alla notifica.

Invio delle notificheAggiornamento del servizio mobile per l'invio delle notifiche
----------------------------------------------------------------------------------

Il passaggio finale consiste nell'aggiungere nel servizio mobile il codice per l'invio delle notifiche. Tale codice viene aggiunto allo script del server registrato nel gestore insert della tabella **TodoItem**.

1.  Di nuovo nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png)

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

	![](./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png) 

	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

1.  Sostituire la funzione insert con il codice seguente:

         function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Create the payload for a Windows Store app.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}

    In questo modo verrà effettuato un tentativo di inviare le notifiche al tag dell'utente attualmente connesso nelle app di Windows Store e iOS.

1.  Aggiornare lo script in modo da sostituire *`<NOTIFICATION_HUB_NAME>`* e *`<FULL_SAS_CONNECTION_STRING>`* con i valori per l'hub di notifica, quindi fare clic su **Salva**.

    Verrà registrato un nuovo script insert, che utilizza l'oggetto Hub di notifica per inviare una notifica push (il testo inserito) al canale specificato nella richiesta insert.

    **Nota**

    Assicurarsi di utilizzare **DefaultFullSharedAccessSignature** per *`<FULL_SAS_CONNECTION_STRING>`*. Questa attestazione garantisce allo script insert accesso completo, oltre alla possibilità di inviare notifiche ai client registrati.

Test dell'appEsecuzione del test delle notifiche push nell'app
--------------------------------------------------------------

Dopo avere configurato le notifiche, è possibile testare l'app inserendo i dati per generare una notifica.

1.  Eseguire l'app.

    All'avvio verrà nuovamente visualizzata una notifica relativa alla registrazione.

2.  Immettere il testo come in precedenza e aggiungere il nuovo elemento.

    Si noti che al termine dell'inserimento l'app riceve una notifica push da Hub di notifica.

    **Nota**

    Se per una piattaforma per la quale è richiesto l'invio di una notifica non è disponibile nessuna registrazione, verrà generato un errore che, in questo caso, può essere ignorato. Per informazioni su come utilizzare i modelli per evitare questa situazione, vedere [Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse](/it-it/manage/services/notification-hubs/notify-users-xplat-mobile-services/).

3.  (Facoltativo) Distribuire l'app client in un secondo dispositivo, quindi eseguirla e inserire testo.

    Verrà visualizzata una notifica su ogni dispositivo.

Passaggi successivi
-------------------

Dopo avere completato questa esercitazione, provare a eseguire quelle seguenti:

-   **Utilizzo di Hub di notifica per inviare le ultime notizie ([Windows Store C\#](/it-it/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/it-it/manage/services/notification-hubs/breaking-news-ios))**
    In questa esercitazione specifica della piattaforma viene illustrato come utilizzare tag per consentire agli utenti di sottoscrivere i tipi di notifiche a cui sono interessati.

-   **[Utilizzo di Hub di notifica per inviare notifiche agli utenti tra piattaforme diverse](/it-it/manage/services/notification-hubs/notify-users-xplat-mobile-services)**
    Questa esercitazione costituisce un'estensione di quella **Utilizzo di Hub di notifica per inviare notifiche agli utenti** e include informazioni relative all'utilizzo di modelli specifici delle piattaforme per la registrazione per le notifiche. In questo modo sarà possibile inviare notifiche da un unico metodo nel codice lato server.

Per ulteriori informazioni su Hub di notifica, vedere [Hub di notifica di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj927170.aspx).

