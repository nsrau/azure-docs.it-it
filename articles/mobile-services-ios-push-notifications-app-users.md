<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Invio di notifiche push agli utenti tramite Servizi mobili

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS" class="current">iOS</a><a href="/it-it/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

In questo argomento viene estesa l'[esercitazione sulle notifiche push precedente][esercitazione sulle notifiche push precedente] tramite l'aggiunta di una nuova tabella per l'archiviazione dei token del servizio di notifica Push di Apple. Questi token possono essere utilizzati per inviare notifiche push agli utenti dell'app per iPhone o iPad.

In questa esercitazione vengono descritte le operazioni per aggiornare le notifiche push nell'app:

1.  [Creazione della tabella Devices][Creazione della tabella Devices]
2.  [Aggiornamento dell'app][Aggiornamento dell'app]
3.  [Aggiornamento degli script del server][Aggiornamento degli script del server]
4.  [Verifica del comportamento delle notifiche push][Verifica del comportamento delle notifiche push]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili e sull'esercitazione [Introduzione alle notifiche push][esercitazione sulle notifiche push precedente]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione alle notifiche push][esercitazione sulle notifiche push precedente].

## <a name="create-table"></a>

## <span class="short-header">Creazione della tabella</span>Creazione della nuova tabella Dispositivi

</h2>
1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][0]

2.  Fare clic sulla scheda **Data** e quindi su **Create**.

    ![][1]

    Verrà visualizzata la finestra di dialogo **Create new table**.

3.  Mantenere l'impostazione predefinita di **Anybody with the application key** per tutte le autorizzazioni, digitare *Devices* in **Table name**, quindi fare clic sul segno di spunta.

    ![][2]

    Verrà creata la tabella **Devices**, in cui sono archiviati i token del dispositivo utilizzati per l'invio di notifiche push separati dai dati dell'elemento.

Successivamente, si procederà alla modifica dell'app per le notifiche push per archiviare i dati in questa nuova tabella anziché nella tabella **TodoItem**.

## <a name="update-app"></a>Aggiornamento dell'app

1.  In Xcode aprire il file QSTodoService.h e aggiungere la dichiarazione del metodo seguente:

        // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

    Questo consente al delegato dell'app di registrare un token del dispositivo con il servizio mobile.

2.  Nel file QSTodoService.m aggiungere il metodo di istanza seguente:

        // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
        {
            MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
            NSDictionary *device = @{ @"deviceToken" : deviceToken };

            // Insert the item into the devices table and add to the items array on completion
            [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                if (error) {
                    NSLog(@"ERROR %@", error);
                }
            }];
        }

    Questo consente agli altri chiamanti di registrare il token del dispositivo con Servizi mobili.

3.  Nel file QSAppDelegate.m aggiungere l'istruzione import seguente:

        #import "QSTodoService.h"

    Questo codice consente al delegato dell'app di rilevare l'implementazione di TodoService.

4.  Nel file QSAppDelegate.m sostituire il metodo **didRegisterForRemoteNotificationsWithDeviceToken** con il codice seguente:

        // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

           // Register the APNS deviceToken with the Mobile Service Devices table.
           NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
           NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];

           QSTodoService *instance = [QSTodoService defaultService];
           [instance registerDeviceToken:token];
        }

5.  Nel file QSTodoListViewController.m individuare il metodo **(IBAction)onAdd** e *rimuovere* il codice seguente:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    Sostituirlo con il codice seguente:

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

L'app è stata aggiornata per utilizzare la nuova tabella Devices per archiviare i token del dispositivo utilizzati per inviare le notifiche push al dispositivo.

## <a name="update-scripts"></a>Aggiornamento degli script del server

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **Devices**.

    ![][3]

2.  In **devices** fare clic sulla scheda **Script** e selezionare **Insert**.

    ![][4]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **Devices**.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
           var devicesTable = tables.getTable('Devices');
           devicesTable.where({
               token: item.token
           }).read({
               success: insertTokenIfNotFound
           });

           function insertTokenIfNotFound(existingTokens) {
               if (existingTokens.length > 0) {
                   request.respond(200, existingTokens[0]);
               } else {
                   request.execute();
               }
           }
        }

    Lo script verifica la tabella **Dispositivi** per individuare un dispositivo esistente con lo stesso token. L'operazione di inserimento viene eseguita solo se non viene trovato alcun dispositivo corrispondente, in modo da evitare l'inserimento di record del dispositivo duplicati.

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
              var devicesTable = tables.getTable('Devices');
              devicesTable.read({
                  success: function(devices) {
                      // Set timeout to delay the notifications, 
                      // to provide time for the app to be closed 
                      // on the device to demonstrate toast notifications.
                      setTimeout(function() {
                          devices.forEach(function(device) {

                              push.apns.send(device.deviceToken, {
                                  alert: "Toast: " + item.text,
                                  payload: {
                                      inAppMessage: 
                                      "Hey, a new item arrived: '" + 
                                      item.text + "'"
                                  }
                              });
                          });
                      }, 2500);
                  }
              });
          }

    }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutti i dispositivi archiviati nella tabella **Devices**.

## <a name="test"></a><span class="short-header">Test dell'app</span>Esecuzione del test delle notifiche push nell'app

1.  Scegliere **Run** per generare il progetto e avviare l'app in un dispositivo con iOS, quindi nell'app digitare un testo significativo, ad esempio *Nuova attività di Servizi mobili*, infine fare clic sull'icona con il segno più (**+**).

    ![][6]

2.  Verificare che venga ricevuta una notifica, quindi fare clic su **OK** per eliminarla.

    ![][7]

3.  Ripetere il passaggio 2 e chiudere immediatamente l'app, quindi verificare che venga visualizzato il seguente avviso popup.

    ![][8]

L'esercitazione è stata completata.

## Passaggi successivi

L'esercitazione sulle nozioni di base dell'utilizzo delle notifiche push è terminata. Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.



  [esercitazione sulle notifiche push precedente]: /it-it/develop/mobile/tutorials/get-started-with-push-ios
  [Creazione della tabella Devices]: #create-table
  [Aggiornamento dell'app]: #update-app
  [Aggiornamento degli script del server]: #update-scripts
  [Verifica del comportamento delle notifiche push]: #test-app
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png
  [3]: ./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png
  [4]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png
  [5]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png
  [7]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png
  [8]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-ios
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
