<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Gestire i conflitti con dati offline in Servizi mobili (iOS) | Dev Center di Servizi mobili di Azure" metaKeywords="" description="Informazioni sull'uso di Servizi mobili di Azure per gestire i conflitti durante la sincronizzazione dei dati offline nell'applicazione iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Gestione dei conflitti con dati offline in Servizi mobili" authors="krisragh" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Gestire i conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili

<div class="dev-center-tutorial-selector sublanding">
<a href="/it-it/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/it-it/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/it-it/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS" class="current">iOS</a>
</div>

Questo argomento descrive come sincronizzare e gestire i conflitti quando si usano le funzionalità offline di Servizi mobili di Azure. Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati offline][Introduzione ai dati offline]. Prima di iniziare questa esercitazione è necessario completare [Introduzione ai dati offline in Servizi mobili][Introduzione ai dati offline].

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

## Completare l'esercitazione di introduzione ai dati offline in Servizi mobili

Seguire le istruzioni dell'esercitazione [Introduzione ai dati offline in Servizi mobili][Introduzione ai dati offline] e completare il relativo progetto. Il progetto completato da quell'esercitazione verrà usato come punto di partenza per la presente esercitazione.

## Aggiornare il progetto dell'app per consentire la modifica

Si procederà all'aggiornamento del progetto completato nell'esercitazione [Introduzione ai dati offline in Servizi mobili][Introduzione ai dati offline] per consentire la modifica degli elementi. Al momento, se si esegue la stessa app su due telefoni, si modifica lo stesso elemento su entrambi i telefoni in locale e si effettua il push delle modifiche nel server, l'operazione non riuscirà a causa di un conflitto.

Le funzionalità di sincronizzazione offline nell'SDK consentono di gestire tali conflitti tramite codice e di decidere in modo dinamico come agire sugli elementi in conflitto. La modifica del progetto di guida introduttiva consente di sperimentare con questa funzionalità.

### Aggiornare il controller di visualizzazione dell'elenco attività

1.  Aggiornare lo storyboard dell'iPhone. Se si sta lavorando con un iPad, è possibile usare gli stessi passaggi anche per lo storyboard dell'iPad.

2.  Selezionare **MainStoryboard\_iPhone.storyboard** nello strumento di navigazione del progetto Xcode, quindi scegliere l'opzione relativa al **controller di visualizzazione dell'elenco attività**. Nel menu superiore fare clic su **Editor -\> Embed In -\> Navigation Controller**

    ![][0]

3.  Nel **controller di visualizzazione dell'elenco attività**, selezionare la cella per la visualizzazione tabella e impostare l'opzione Accessory Mode su **Disclosure indicator**. Questo indicatore segnala agli utenti che se viene fatto clic sul controller di visualizzazione tabella associato, verrà visualizzata una nuova visualizzazione. L'indicatore non produce alcun evento.

    ![][1]

4.  In **TodoListViewController.m** rimuovere completamente le operazioni seguenti con i relativi contenuti, in quanto non necessarie:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### Aggiungere il controller di visualizzazione dell'attività

1.  Aggiungere una nuova classe Objective-C denominata **QSTodoItemViewController**, derivata da **UIViewController**, al progetto:

    ![][2]

    ![][3]

2.  In **QSTodoItemViewController.h** aggiungere una proprietà per contenere l'elemento da modificare:

        @property (nonatomic, weak) NSMutableDictionary *item;

3.  In **QSTodoItemViewController.m** aggiungere due proprietà private per i due campi dell'attività da modificare: lo stato di completamento e il testo dell'attività stessa:

        @interface QSTodoItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

4.  In **QSTodoItemViewController.m** aggiornare l'implementazione stub di **viewDidLoad** con il codice seguente:

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

5.  In **QSTodoItemViewController.m** aggiungere quattro metodi aggiuntivi per la gestione dei vari eventi:

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

        - (void)viewWillDisappear:(BOOL)animated {
            [self.item setValue:[self.itemText text] forKey:@"text"];
            [self.item setValue:[NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0] forKey:@"complete"];
        }

### Aggiungere il controller di visualizzazione dell'attività e l'elemento Segue allo storyboard

1.  Tornare al file **MainStoryboard\_iPhone.storyboard** usando lo strumento di navigazione del progetto.

2.  Aggiungere un nuovo controller per l'attività allo storyboard, a destra del **controller di visualizzazione dell'attività** corrente. Impostare la classe personalizzata del nuovo controller di visualizzazione su **QSTodoItemViewController**. Per altre informazioni, vedere [Aggiunta di una scena a uno storyboard][Aggiunta di una scena a uno storyboard].

3.  Aggiungere un elemento Segue push dal **controller di visualizzazione dell'elenco attività** al **controller di visualizzazione dell'attività** e denominare tale elemento **detailSegue**. Per altre informazioni, vedere [Aggiunta di un elemento Segue tra scene in uno storyboard][Aggiunta di un elemento Segue tra scene in uno storyboard]. Non creare questo elemento Segue da qualsiasi cella o pulsante nel controller di visualizzazione di origine. Trascinare invece, tramite CTRL + trascinamento, dall'icona del controller di visualizzazione sotto al **controller di visualizzazione dell'elenco attività** nell'interfaccia dello storyboard nel **controller di visualizzazione dell'attività**. Se si inserisce accidentalmente un elemento Segue da una cella, quando si esegue l'app, l'elemento Segue verrà attivato due volte, generando l'errore seguente:

        Nested push animation can result in corrupted navigation bar

4.  Aggiungere al nuovo **controller di visualizzazione dell'attività** un campo di testo per l'elemento e un controllo Segmented per lo stato di completamento, con le relative etichette. Nel controllo Segmented, impostare il titolo per **Segment 0** su **Yes** e il titolo per **Segment 1** su **No**. Connettere questi due nuovi campi agli outlet nel codice. Per altre informazioni, vedere [Creare un'interfaccia utente][Creare un'interfaccia utente] e [Controlli Segmented][Controlli Segmented].

    ![][4]

5.  Connettere questi nuovi campi agli outlet corrispondenti già aggiunti a **QSTodoItemViewController.m**. Connettere il campo per il testo dell'elemento all'outlet **itemText** e il controllo Segmented dello stato di completamento all'outlet **itemComplete**. Per altre informazioni, vedere [Creazione di una connessione di outlet][Creazione di una connessione di outlet].

6.  Impostare il delegato del campo di testo sul controller di visualizzazione. Questa operazione garantisce la registrazione del campo di testo quando si modificherà un elemento e si premerà INVIO. Premere CTRL ed effettuare un trascinamento dal campo di testo all'icona del controller di visualizzazione sotto al **controller di visualizzazione dell'attività** nell'interfaccia dello storyboard, quindi selezionare l'outlet del delegato; questo indica allo storyboard che il delegato del campo di testo è l'attuale controller di visualizzazione.

7.  Verificare che l'app funzioni con tutte le modifiche apportate finora. Eseguire l'app nel simulatore. Aggiungere voci all'elenco delle attività, quindi fare clic su di esse. Verrà visualizzato il controller di visualizzazione degli elementi (attualmente vuoto).

    ![][5]

    ![][6]

### Aggiungere dettagli elemento al controller di visualizzazione delle attività

1.  Si farà riferimento a **QSTodoItemViewController** da **QSTodoListViewController.m**. Quindi, in **QSTodoListViewController.m**, si aggiungerà una riga per importare **QSTodoItemViewController.h**.

        #import "QSTodoItemViewController.h"

2.  Aggiungere due nuove proprietà all'interfaccia di **QSTodoListViewController** in **QSTodoListViewController.m** per archiviare l'elemento in corso di modifica:

        @property (nonatomic)           NSInteger       editedItemIndex;
        @property (strong, nonatomic)   NSMutableDictionary *editedItem;

3.  Implementare **tableView:didSelectRowAtIndexPath:** in **QSTodoListViewController.m** per salvare l'elemento in corso di modifica e quindi chiamare l'elemento Segue per mostrare la visualizzazione dettagli.

          - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
              self.editedItemIndex = [indexPath row];
              self.editedItem = [[self.todoService.items objectAtIndex:[indexPath row]] mutableCopy];

              [self performSegueWithIdentifier:@"detailSegue" sender:self];
          }

4.  Implementare **prepareForSegue:sender:** in **QSTodoListViewController.m** per passare l'elemento al **controller di visualizzazione dell'attività**.

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSTodoItemViewController *ivc = (QSTodoItemViewController *)[segue destinationViewController];
                ivc.item = self.editedItem;
            }
        }

5.  Verificare che l'app funzioni con tutte le modifiche apportate finora. Eseguire l'app nel simulatore. Aggiungere voci all'elenco delle attività, quindi fare clic su di esse. Si noterà che il controller di visualizzazione dell'attività non è più vuoto, al contrario vi sono visualizzati i dettagli delle attività.

    ![][7]

### Aggiungere supporto per il salvataggio delle modifiche

1.  Quando si fa clic sul pulsante "Back" nella visualizzazione di navigazione, le modifiche vanno perse. Sono stati inviati dati alla visualizzazione dettagli, ma tali dati non vengono inviati alla visualizzazione master. Poiché viene già passato un puntatore a una copia dell'elemento, è possibile usarlo per recuperare l'elenco di aggiornamenti eseguito sull'elemento e aggiornarlo nel server. Per iniziare, aggiornare la classe wrapper sul server di **QSTodoService** in **QSTodoService.m** rimuovendo l'operazione **completeItem** e aggiungendo una nuova operazione **updateItem**. Questo perché **completeItem** contrassegna un solo elemento come completato, mentre **updateItem** aggiornerà gli elementi.

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Replace the original in the items array
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:item completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                NSInteger index = -1;
                if (!error) {
                    BOOL isComplete = [[item objectForKey:@"complete"] boolValue];
                    NSString *remoteId = [item objectForKey:@"id"];
                    index = [items indexOfObjectPassingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
                        return [remoteId isEqualToString:[obj objectForKey:@"id"]];
                    }];

                    if (index != NSNotFound && isComplete)
                    {
                        [mutableItems removeObjectAtIndex:index];
                    }
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });
            }];
        }

2.  Rimuovere la dichiarazione per **completeItem** da **QSTodoService.h** e aggiungere la dichiarazione seguente per **updateItem**:

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion;

3.  In **QSTodoListViewController.m** aggiungere l'operazione **viewWillAppear** per chiamare il metodo di aggiornamento ogni volta che la visualizzazione master viene visualizzata dopo l'uscita dal controller della visualizzazione dettagli.

        - (void)viewWillAppear:(BOOL)animated {
            if (self.editedItem && self.editedItemIndex >= 0) {
                // Returning from the details view controller
                NSDictionary *item = [self.todoService.items objectAtIndex:self.editedItemIndex];

                BOOL changed = ![item isEqualToDictionary:self.editedItem];
                if (changed) {
                    [self.tableView setUserInteractionEnabled:NO];

                    // Change the appearance to look greyed out until we remove the item
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:self.editedItemIndex inSection:0];

                    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
                    cell.textLabel.textColor = [UIColor grayColor];

                    // Ask the todoService to update the item, and remove the row if it's been completed
                    [self.todoService updateItem:self.editedItem atIndex:self.editedItemIndex completion:^(NSUInteger index) {
                        if ([[self.editedItem objectForKey:@"complete"] boolValue]) {
                            // Remove the row from the UITableView
                            [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                                  withRowAnimation:UITableViewRowAnimationTop];
                        } else {
                            [self.tableView reloadRowsAtIndexPaths:[NSArray arrayWithObject:indexPath]
                                                  withRowAnimation:UITableViewRowAnimationAutomatic];
                        }

                        [self.tableView setUserInteractionEnabled:YES];

                        self.editedItem = nil;
                        self.editedItemIndex = -1;
                    }];
                } else {
                    self.editedItem = nil;
                    self.editedItemIndex = -1;
                }
            }
        }

4.  A questo punto è possibile testare l'app Verificare che l'app funzioni con tutte le modifiche apportate finora. Eseguire l'app nel simulatore. Aggiungere voci all'elenco delle attività, quindi fare clic su di esse. Provare a modificare un elemento, quindi tornare indietro. Verificare se la descrizione dell'elemento viene aggiornata nella visualizzazione master dell'app. Aggiornare l'app usando il movimento di trascinamento verso il basso, quindi verificare che la modifica sia riflessa nel cloud.

### Gestione dei problemi di conflitto

1.  Si esaminerà di seguito la situazione in cui due client diversi cercano di modificare lo stesso elemento dati nello stesso momento. Nell'elenco di esempio riportato di seguito è presente un elemento denominato "Hello world 3". Modificarlo ad esempio in "Hello world 13" su un dispositivo, quindi modificarlo in "Hello world 23" su un altro dispositivo.

    ![][8]

2.  Avviare l'app in due ambiti diversi: su due dispositivi iOS oppure nel simulatore e su un dispositivo iOS. Se non è disponibile un dispositivo fisico su cui eseguire il test, avviare un'istanza nel simulatore, quindi, usando un client REST, inviare una richiesta PATCH al servizio mobile. L'URL della richiesta PATCH riflette il nome del servizio mobile, il nome della tabella delle attività e l'ID della tabella delle attività in corso di modifica, mentre l'intestazione x-zumo-application è la chiave dell'applicazione:

        PATCH https://todolist.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: shYOoDFdKhmzLEbnMQqPYrCLhwGOVA10

        {
            "id": "D265929E-B17B-42D1-8FAB-D0ADF26486FA",
            "text": "Hello world 23"
        }

3.  Aggiornare ora gli elementi nelle due istanze dell'app. Verrà visualizzato un errore nel log dell'output in Xcode:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

Questo perché, nella chiamata a **pullWithQuery:completion:** del blocco di completamento, il parametro di errore sarà non Null, il che causerà la visualizzazione dell'errore sull'output tramite **NSLog**.

### Aggiornare QSTodoService per supportare la gestione dei conflitti

1.  Si farà in modo che l'utente decida come gestire il conflitto eseguendo azioni nel client. Per fare ciò, verrà implementato il protocollo **MSSyncContextDelegate**. Sia in **QSTodoService.h** che in **QSTodoService.m**, modificare la dichiarazione del metodo factory \*\*(QSTodoService \*)defaultService;\*\* nell'istruzione riportata di seguito, in modo da ricevere il delegato del contesto di sincronizzazione come parametro.

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2.  In **QSTodoService.m** modificare la riga **init** come indicato di seguito, ricevendo nuovamente il contesto di sincronizzazione come parametro:

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3.  In **QSTodoService.m** modificare la chiamata **init** in **defaultServiceWithDelegate** in **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4.  Tornando in **QSTodoService.m**, modificare l'inizializzazione di **self.client.syncContext** per passare **syncDelegate** invece di **Null** per il delegato:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### Aggiungere l'helper della vista avvisi dell'interfaccia utente per supportare la gestione dei conflitti

1.  Se è presente un conflitto, verrà consentito all'utente di scegliere la versione da mantenere:

-   mantenere la versione del client (che esegue l'override della versione del server),
-   mantenere la versione del server (che aggiorna la tabella locale del client), oppure
-   non mantenere nessuna delle versioni (annulla il push e lascia l'operazione in sospeso).

Poiché potrebbe verificarsi un altro aggiornamento durante la visualizzazione del prompt, si continuerà a visualizzare le opzioni fino a quando il server smette di restituire un errore come risposta. Nel codice verrà usata una classe helper che mostra una vista avvisi e accetta un delegato richiamato quando viene visualizzata la vista avvisi. Definire per prima la classe helper **QSUIAlertViewWithBlock**.

1.  Aggiungere la nuova classe, **QSUIAlertViewWithBlock**, usando Xcode, quindi sovrascrivere **QSUIAlertViewWithBlock.h** con i contenuti seguenti:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

2.  Sovrascrivere quindi **QSUIAlertViewWithBlock.m** con il file seguente:

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### Aggiungere il gestore dei conflitti al controller di visualizzazione dell'elenco attività

1.  In **QSTodoListViewController.m** sostituire la chiamata a **defaultService** in **viewDidLoad** con una chiamata a **defaultServiceWithDelegate**, come indicato di seguito:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2.  In **QSTodoListViewController.h** aggiungere **\<MSSyncContextDelegate\>** alla dichiarazione dell'interfaccia, in modo da implementare il protocollo **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate>

3.  Aggiungere l'istruzione di importazione seguente nella parte superiore di **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4.  Infine, si aggiungeranno le due operazioni seguenti a **QSTodoListViewController.m** per usare la classe helper e richiedere all'utente di riconciliare il conflitto usando uno di tre metodi.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

### Test dell'app

Si procederà ora al test dell'app con i conflitti. Modificare lo stesso elemento in due istanze diverse dell'app eseguite simultaneamente. Eseguire il movimento di aggiornamento nelle istanze delle app mediante trascinamento dall'alto. Verrà visualizzata la richiesta di riconciliazione della modifica.

### Riepilogo

Al fine di configurare il progetto completo da [Introduzione ai dati offline][Introduzione ai dati offline] per il rilevamento dei conflitti, si è inizialmente aggiunta la possibilità di modificare e aggiornare le attività.

A tale scopo, si è aggiunto un nuovo controller di visualizzazione dei dettagli delle attività, connesso al controller di visualizzazione principale, quindi si è aggiunta la possibilità di salvare modifiche e di eseguirne il push nel cloud.

Si è quindi appreso cosa succede quando si verifica un conflitto. Si è aggiunto supporto per un gestore di conflitti mediante l'implementazione del protocollo **MSSyncContextDelegate**. Si è anche aggiunto il supporto per il delegato del contesto di sincronizzazione tramite la classe di interfaccia del server di **QSTodoService**, **QSTodoListViewController** e delle classi di supporto.

Nel corso della procedura, si è aggiunta la classe helper **QSUIAlertViewWithBlock** per visualizzare un avviso agli utenti, quindi si è completato il lavoro aggiungendo codice a **QSTodoListViewController** per chiedere all'utente di riconciliare il conflitto in uno di tre modi.

<!-- URLs. -->

  [Introduzione ai dati offline]: /it-it/documentation/articles/mobile-services-ios-get-started-offline-data/
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28
  [0]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-1.png
  [1]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
  [2]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-1.png
  [3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-2.png
  [Aggiunta di una scena a uno storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
  [Aggiunta di un elemento Segue tra scene in uno storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
  [Creare un'interfaccia utente]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
  [Controlli Segmented]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
  [4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
  [Creazione di una connessione di outlet]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
  [5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
  [6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
  [7]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
  [8]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
