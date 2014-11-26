<properties linkid="develop-mobile-tutorials-build-realtime-apps-with-pusher-ios" urlDisplayName="Build Realtime Apps with Pusher" pageTitle="Build Realtime Apps with Pusher (iOS) - Mobile Services" metaKeywords="" description="Learn how to use Pusher to send notifications to your Azure Media Services app on iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Build Real-time Apps with Mobile Services and Pusher" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Compilazione di app in tempo reale tramite Servizi mobili e Pusher

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="" title="iOS" class="current">iOS</a> 
</div>

In questo argomento viene illustrato come aggiungere funzionalità in tempo reale a un'app basata su Servizi mobili di Azure. Una volta completata l'esercitazione, i dati dell'elenco attività saranno sincronizzati, in tempo reale, tra tutte le istanze in esecuzione dell'app.

Nell'esercitazione [Invio di notifiche push agli utenti][Invio di notifiche push agli utenti] viene illustrato come utilizzare le notifiche push per informare gli utenti della presenza di nuove voci nell'elenco attività. Le notifiche push rappresentano un'ottima soluzione per mostrare modifiche occasionali. Tuttavia, un'app necessita talvolta di notifiche in tempo reale frequenti. È possibile aggiungere le notifiche in tempo reale al servizio mobile tramite l'API Pusher. In questa esercitazione verrà utilizzato il servizio Pusher con Servizi mobili per sincronizzare le modifiche di un elenco attività in tutte le istanze in esecuzione dell'app.

Pusher è un servizio basato su cloud che, come Servizi mobili, semplifica la compilazione di app in tempo reale. È possibile utilizzare Pusher per creare rapidamente sondaggi in diretta, chat room, giochi per più giocatori, app di collaborazione per trasmettere dati e contenuto in diretta e molto altro ancora. Per ulteriori informazioni, visitare il sito [][]<http://pusher.com></a>.

In questa esercitazione vengono descritte le operazioni di base per aggiungere funzionalità di collaborazione in tempo reale all'applicazione Elenco attività:

1.  [Creazione di un account Pusher][Creazione di un account Pusher]
2.  [Aggiornamento dell'app][Aggiornamento dell'app]
3.  [Installazione degli script del server][Installazione degli script del server]
4.  [Test dell'app][Test dell'app]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

## <a name="sign-up"></a>Creazione di un account Pusher

[WACOM.INCLUDE [pusher-sign-up](../includes/pusher-sign-up.md)]

## <a name="update-app"></a>Aggiornamento dell'app

Dopo avere configurato l'account Pusher, nel passaggio successivo si procederà alla modifica del codice dell'app per iOS per aggiungere la nuova funzionalità.

### Installare la libreria libPusher

La libreria [libPusher][libPusher] consente di accedere a Pusher da iOS.

1.  Scaricare la libreria libPusher [da qui][da qui].

2.  Nel progetto creare un gruppo denominato *libPusher*.

3.  In Finder decomprimere il file ZIP scaricato, selezionare le cartelle **libPusher-combined.a** e **/headers** e trascinare questi elementi nel gruppo **libPusher** nel progetto.

4.  Selezionare **Copy items into destination group's folder**, quindi fare clic su **Finish**.

    ![][0]

I file della libreria libPusher verranno copiati nel progetto.

1.  Nella radice del progetto nell'area di esplorazione dei progetti fare clic su **Build Phases**, quindi su **Add Build Phase** e infine su **Add Copy Files**.

2.  Trascinare il file **libPusher-combined.a** dall'area di esplorazione dei progetti nella nuova fase di compilazione.

3.  Impostare **Destination** su **Frameworks** e fare clic su **Copy only when installing**.

    ![][1]

4.  Nell'area **Link Binary With Libraries** aggiungere le librerie seguenti:

    -   libicucore.dylib
    -   CFNetwork.framework
    -   Security.framework
    -   SystemConfiguration.framework

5.  Infine, in **Build Settings** individuare l'impostazione di compilazione di destinazione **Other Linker Flags** e aggiungere il flag **-all\_load**.

    ![][2]

    Viene mostrato il flag **-all\_load** impostato per la destinazione di compilazione di debug.

A questo punto, la libreria è installata e pronta per l'uso.

### Aggiungere il codice all'applicazione

1.  In Xcode aprire il file **QSTodoService.h** e aggiungere le dichiarazioni del metodo seguenti:

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2.  Sostituire le dichiarazioni esistenti di **addItem** e **completeItem** con le seguenti:

        - (void) addItem:(NSDictionary *) item;
        - (void) completeItem: (NSDictionary *) item;

3.  Nel file **QSTodoService.m** aggiungere il codice seguente per implementare i nuovi metodi:

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item
        {
            NSInteger itemId = [[item objectForKey: @"id"] integerValue];

            return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
        }

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
        {
            NSUInteger index = [self getItemIndex:item];

            // Only complete action if item not already in list
            if(index == NSNotFound)
            {
                NSUInteger newIndex = [items count];
                [(NSMutableArray *)items insertObject:item atIndex:newIndex];
                return newIndex;
            }
            else
                return -1;
        }

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item
        {
            NSUInteger index = [self getItemIndex:item];

            // Only complete action if item exists in items list
            if(index != NSNotFound)
            {
                NSMutableArray *mutableItems = (NSMutableArray *) items;
                [mutableItems removeObjectAtIndex:index];
            }       
            return index;
        }

    Ora, QSTodoService consente di trovare voci in base all'**id** e di aggiungere e completare voci in locale senza inviare richieste esplicite al servizio remoto.

4.  Sostituire i metodi **addItem** e **completeItem** con il codice seguente:

        -(void) addItem:(NSDictionary *)item
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
                [self logErrorIfNotNil:error];
            }];
        }

        -(void) completeItem:(NSDictionary *)item
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@(YES) forKey:@"complete"];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
                [self logErrorIfNotNil:error];
            }];
        }

    Si noti che gli elementi sono stati aggiunti e completati, unitamente agli aggiornamenti all'interfaccia utente, quando gli eventi sono ricevuti da Pusher anziché quando la tabella dati viene aggiornata.

5.  Nel file **QSTodoListViewController.h** aggiungere le istruzioni import seguenti:

        #import "PTPusherDelegate.h"
        #import "PTPusher.h"
        #import "PTPusherEvent.h"
        #import "PTPusherChannel.h"

6.  Modificare la dichiarazione dell'interfaccia per aggiungere **PTPusherDelegate** in modo da ottenere l'aspetto seguente:

        @interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7.  Aggiungere la nuova proprietà seguente:

        @property (nonatomic, strong) PTPusher *pusher;

8.  Aggiungere il codice seguente per dichiarare un nuovo metodo:

        // Sets up the Pusher client
        - (void) setupPusher;

9.  In **QSTodoListViewController.m**, add the following line under the other <**@synthesise*>\* lines to implement the new property:

        @synthesize pusher = _pusher;

10. Ora aggiungere il codice seguente per implementare il nuovo metodo:

        // Sets up the Pusher client
        - (void) setupPusher {

            // Create a Pusher client, using your Pusher app key as the credential
            // TODO: Move Pusher app key to configuration file
            self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
            self.pusher.reconnectAutomatically = YES;

            // Subscribe to the 'todo-updates' channel
            PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

            // Bind to the 'todo-added' event
            [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

                // Add item to the todo list
                NSUInteger index = [self.todoService itemAdded:channelEvent.data];

                // If the item was not already in the list, add the item to the UI
                if(index != -1)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }
            }];

            // Bind to the 'todo-completed' event
            [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

                // Update the item to be completed
                NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

                // As long as the item did exit in the list, update the UI
                if(index != NSNotFound)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }               
            }];
        }

11. Replace the `**your_app_key**` placeholder with the app\_key value you copied from the Connection Info dialog earlier.

12. Sostituire il metodo **onAdd** con il codice seguente:

        - (IBAction)onAdd:(id)sender
        {
            if (itemText.text.length  == 0) {
                return;
            }

            NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
            [self.todoService addItem:item];

            itemText.text = @"";
        }

13. Nel file **QSTodoListViewController.m** individuare il metodo (void)viewDidLoad e aggiungere una chiamata al metodo **setupPusher** in modo che le prime righe abbiano l'aspetto seguente:

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self setupPusher];

14. Alla fine del metodo **tableView:commitEditingStyle:forRowAtIndexPath** sostituire la chiamata a **completeItem** con il codice seguente:

        // Ask the todoService to set the item's complete value to YES
        [self.todoService completeItem:item];

L'app è ora in grado di ricevere eventi da Pusher e di aggiornare l'elenco attività locale di conseguenza.

## <a name="install-scripts"></a>Installazione degli script del server

Ora non rimane che configurare gli script del server. Verrà inserito uno script per gestire l'inserimento o l'aggiornamento di una voce nella tabella TodoList.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sul servizio mobile.

2.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][3]

3.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

    ![][4]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

4.  Sostituire la funzione insert con il codice seguente:

        var Pusher = require('pusher');

        function insert(item, user, request) {   

            request.execute({
                success: function() {
                    // After the record has been inserted, trigger immediately to the client
                    request.respond();

                    // Publish event for all other active clients
                    publishItemCreatedEvent(item);
                }
            });

            function publishItemCreatedEvent(item) {

                // Ideally these settings would be taken from config
                var pusher = new Pusher({
                  appId: '**your_app_id**',
                  key: '**your_app_key**',
                  secret: '**your_app_secret**'
                });     

                // Publish event on Pusher channel
                pusher.trigger( 'todo-updates', 'todo-added', item );   
            }
        }

5.  Sostituire i segnaposto nello script precedente con i valori copiati dalla finestra di dialogo Connection Info:

    -   **`**your_app_id**`**: the app\_id value
    -   **`**your_app_key**`**: the app\_key value
    -   **`**your_app_key_secret**`**: the app\_key\_secret

6.  Fare clic sul pulsante **Save**. Ora è stato configurato uno script che consente di pubblicare un evento in Pusher ogni volta che viene inserita una nuova voce nella tabella **TodoItem**.

7.  Selezionare **Update** nell'elenco a discesa **Operation**.

8.  Sostituire la funzione update con il codice seguente:

        var Pusher = require('pusher');

        function update(item, user, request) {   

            request.execute({
                success: function() {
                    // After the record has been updated, trigger immediately to the client
                    request.respond();

                    // Publish event for all other active clients
                    publishItemUpdatedEvent(item);
                }
            });

            function publishItemUpdatedEvent(item) {

                // Ideally these settings would be taken from config
                var pusher = new Pusher({
                  appId: '**your_app_id**',
                  key: '**your_app_key**',
                  secret: '**your_app_secret**'
                });     

                // Publish event on Pusher channel
                pusher.trigger( 'todo-updates', 'todo-completed', item );

            }
        }

9.  Ripetere il passaggio 5 di questo script per sostituire i segnaposto.

10. Fare clic sul pulsante **Save**. Ora è stato configurato uno script che consente di pubblicare un evento in Pusher ogni volta che un nuovo elemento viene aggiornato.

## <a name="test-app"></a>Test dell'app

Per testare l'app sarà necessario eseguire due istanze. È possibile eseguire un'istanza in un dispositivo iOS e un'altra nel simulatore iOS.

1.  Connettere il dispositivo iOS, premere il pulsante **Run** (o la combinazione di tasti Comando+R) per avviare l'app nel dispositivo, quindi arrestare il debug.

    Ora l'app è installata nel dispositivo.

2.  Eseguire l'app nel simulatore iOS e avviare al contempo l'app nel dispositivo iOS.

    A questo punto, sono in esecuzione due istanze dell'app.

3.  Aggiungere un nuovo elemento Todo in una delle istanze dell'app.

    Verificare che il nuovo elemento aggiunto venga visualizzato anche nell'altra istanza.

4.  Selezionare un elemento Todo per contrassegnarlo come completato in un'istanza dell'app.

    Verificare che la nuova voce aggiunta non sia più visualizzata nell'altra istanza.

Congratulazioni. L'app del servizio mobile è stata configurata per mantenere la sincronizzazione tra tutti i client in tempo reale.

## <a name="nextsteps"> </a>Passaggi successivi

Dopo avere sperimentato quanto è facile utilizzare il servizio Pusher con Servizi mobili, utilizzare i collegamenti seguenti per ulteriori informazioni su Pusher.

-   Documentazione sull'API Pusher: <http://pusher.com/docs>
-   Esercitazioni su Pusher: <http://pusher.com/tutorials>

Per ulteriori informazioni sulla registrazione e l'utilizzo di script del server, vedere [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili].



  [Invio di notifiche push agli utenti]: /it-it/develop/mobile/tutorials/push-notifications-to-users-ios
  [0]: http://pusher.com
  [Creazione di un account Pusher]: #sign-up
  [Aggiornamento dell'app]: #update-app
  [Installazione degli script del server]: #install-scripts
  [Test dell'app]: #test-app
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
  [libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
  [da qui]: http://go.microsoft.com/fwlink/p/?LinkId=276998
  [0]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
  [1]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
  [2]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p/?LinkId=262293
