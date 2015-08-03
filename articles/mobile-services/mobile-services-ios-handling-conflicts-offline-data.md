<properties
	pageTitle="Gestire i conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili (iOS) | Mobile Dev Center"
	description="Informazioni sull'uso di Servizi mobili di Azure per gestire i conflitti durante la sincronizzazione dei dati offline nell'applicazione iOS."
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015" 
	ms.author="krisragh;donnam"/>


# Gestione dei conflitti relativi alla sincronizzazione offline dei dati in Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

Questo argomento illustra come sincronizzare e gestire i conflitti quando si usano le funzionalità offline di Servizi mobili di Azure. Questa esercitazione è basata sulle procedure e sull'app di esempio dell'esercitazione precedente [Introduzione ai dati offline]. Prima di iniziare questa esercitazione è necessario completare [Introduzione ai dati offline in Servizi mobili].

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>.

Questa esercitazione descrive le operazioni di base seguenti:

1. [Aggiornare il progetto dell'app per consentire la modifica]
2. [Aggiornare il controller di visualizzazione dell'elenco attività]
3. [Aggiungere il controller di visualizzazione dell'attività]
4. [Aggiungere il controller di visualizzazione dell'attività e l'elemento Segue allo storyboard]
5. [Aggiungere dettagli elemento al controller di visualizzazione dell'attività]
6. [Aggiungere supporto per il salvataggio delle modifiche]
7. [Problemi di gestione dei conflitti]
8. [Aggiornare QSTodoService per supportare la gestione dei conflitti]
9. [Aggiungere l'helper della vista avvisi dell'interfaccia utente per supportare la gestione dei conflitti]
10. [Aggiungere il gestore dei conflitti al controller di visualizzazione dell'elenco attività]
11. [Testare l'app]

## Completare l'esercitazione di introduzione ai dati offline in Servizi mobili

Seguire le istruzioni dell'esercitazione [Introduzione ai dati offline in Servizi mobili] e completare il relativo progetto. Il progetto completato da quell'esercitazione verrà usato come punto di partenza per la presente esercitazione.

## <a name="update-app"></a>Aggiornare il progetto dell'app per consentire la modifica

Si procederà all'aggiornamento del progetto completato nell'esercitazione [Introduzione ai dati offline in Servizi mobili] per consentire la modifica degli elementi. Al momento, se si esegue la stessa app su due telefoni, si modifica lo stesso elemento su entrambi i telefoni in locale e si effettua il push delle modifiche nel server, l'operazione non riuscirà a causa di un conflitto.

Le funzionalità di sincronizzazione offline nell'SDK consentono di gestire tali conflitti tramite codice e di decidere in modo dinamico come agire sugli elementi in conflitto. La modifica del progetto di guida introduttiva consente di sperimentare con questa funzionalità.

### <a name="update-list-view"></a>Aggiornare il controller di visualizzazione dell'elenco attività

1. Selezionare **MainStoryboard_iPhone.storyboard** nello strumento di navigazione del progetto Xcode, quindi scegliere l'opzione relativa al **controller di visualizzazione dell'elenco attività**. Selezionare la cella della visualizzazione tabella e impostare la relativa modalità Accessory su **Disclosure indicator**. Questo indicatore segnala agli utenti che selezionando il controller di visualizzazione tabella associato, è possibile visualizzare una nuova visualizzazione. L'indicatore non produce alcun evento.

      ![][update-todo-list-view-controller-2]

2. In **TodoListViewController.m** rimuovere completamente le operazioni seguenti con i relativi contenuti, in quanto non necessarie:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### <a name="add-view-controller"></a>Aggiungere il controller di visualizzazione dell'attività

1. Creare una nuova classe Cocoa Touch denominata **QSItemViewController**, derivata da **UIViewController**.

2. In **QSItemViewController.h** aggiungere la definizione di tipo seguente:

        typedef void (^ItemEditCompletionBlock) (NSDictionary *editedItem);

3. In **QSItemViewController.h** aggiungere una proprietà per contenere l'elemento da modificare e una proprietà per il callback richiamato dopo che l'utente preme il pulsante per tornare indietro nella visualizzazione dettagli:

        @property (nonatomic, weak) NSMutableDictionary *item;
        @property (nonatomic, strong) ItemEditCompletionBlock editCompleteBlock;

4. In **QSItemViewController.m** aggiungere due proprietà private per i due campi dell'attività da modificare, lo stato di completamento e il testo dell'attività stessa:

        @interface QSItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

5. In **QSItemViewController.m** aggiornare l'implementazione stub di **viewDidLoad** con il codice seguente:

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

6. In **QSItemViewController.m** includere quattro metodi aggiuntivi per la gestione degli gli eventi di controllo modificati:

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

7. In **QSItemViewController**, aggiungere anche il metodo seguente, che viene chiamato quando l'utente preme il pulsante per tornare **indietro** nella barra di spostamento. Il metodo può essere chiamato su altri eventi, controllare quindi la visualizzazione padre. Se sono state apportate modifiche all'elemento, **self.item** viene modificato e viene chiamato il callback **editCompleteBlock**:

        - (void)didMoveToParentViewController:(UIViewController *)parent
        {
            if (![parent isEqual:self.parentViewController]) {
                NSNumber *completeValue = [NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0];

                Boolean changed =
                    [self.item valueForKey:@"text"] != [self.itemText text] ||
                    [self.item valueForKey:@"complete"] != completeValue;

                if (changed) {
                    [self.item setValue:[self.itemText text] forKey:@"text"];
                    [self.item setValue:completeValue forKey:@"complete"];

                    self.editCompleteBlock(self.item);
                }
            }
        }

### <a name="add-segue"></a>Aggiungere il controller di visualizzazione dell'attività e l'elemento Segue allo storyboard

1. Tornare al file **MainStoryboard_iPhone.storyboard** usando lo strumento di navigazione del progetto.

2. Aggiungere un nuovo controller per l'attività allo storyboard, a destra del **controller di visualizzazione dell'attività** corrente. Impostare la classe personalizzata del nuovo controller di visualizzazione su **QSItemViewController**. Per altre informazioni, vedere [Aggiunta di una scena a uno storyboard].

3. Aggiungere un elemento Segue **Show** dal **controller di visualizzazione dell'elenco attività** al **controller di visualizzazione dell'attività**. Quindi, nella finestra di ispezione degli attributi, impostare l'identificatore Segue su **detailSegue**.

    Non creare questo elemento Segue da qualsiasi cella o pulsante nel controller di visualizzazione di origine. Premere invece CTRL ed eseguire un'operazione di trascinamento dall'icona del controller di visualizzazione, sopra al **controller di visualizzazione dell'elenco attività** nell'interfaccia dello storyboard, al **controller di visualizzazione dell'attività**:

    ![][todo-list-view-controller-add-segue]

    Se si inserisce accidentalmente un elemento Segue da una cella, quando si esegue l'app, l'elemento Segue verrà attivato due volte, generando l'errore seguente:

        Nested push animation can result in corrupted navigation bar

    Per altre informazioni sugli elementi Segue, vedere [Aggiunta di un elemento Segue tra scene in uno storyboard].

4. Aggiungere al nuovo **controller di visualizzazione dell'attività** un campo di testo per l'elemento e un controllo Segmented per lo stato di completamento, con le relative etichette. Nel controllo Segmented, impostare il titolo per **Segment 0** su **Yes** e il titolo per **Segment 1** su **No**. Connettere questi due nuovi campi agli outlet nel codice. Per altre informazioni, vedere [Creare un'interfaccia utente] e [Controlli Segmented].

      ![][add-todo-item-view-controller-3]

5. Connettere questi nuovi campi agli outlet corrispondenti già aggiunti a **QSItemViewController.m**. Connettere il campo per il testo dell'elemento all'outlet **itemText** e il controllo Segmented dello stato di completamento all'outlet **itemComplete**. Per altre informazioni, vedere [Creazione di una connessione di outlet].

6. Impostare il delegato del campo di testo sul controller di visualizzazione. Premere CTRL ed effettuare un trascinamento dal campo di testo all'icona del controller di visualizzazione sotto al **controller di visualizzazione dell'attività** nell'interfaccia dello storyboard, quindi selezionare l'outlet del delegato; questo indica allo storyboard che il delegato del campo di testo è l'attuale controller di visualizzazione.

7. Verificare che l'app funzioni con tutte le modifiche apportate finora. Eseguire l'app nel simulatore. Aggiungere voci all'elenco delle attività, quindi fare clic su di esse. Verrà visualizzato il controller di visualizzazione degli elementi (attualmente vuoto).

      ![][add-todo-item-view-controller-4] ![][add-todo-item-view-controller-5]

### <a name="add-item-details"></a>Aggiungere dettagli elemento al controller di visualizzazione dell'attività

1. Si farà riferimento a **QSItemViewController** da **QSTodoListViewController.m**. Quindi, in **QSTodoListViewController.m**, si aggiungerà una riga per importare **QSItemViewController.h**.

        #import "QSItemViewController.h"

2. Aggiungere una nuova proprietà all'interfaccia di **QSTodoListViewController** in **QSTodoListViewController.m** per archiviare l'elemento in corso di modifica:

        @property (strong, nonatomic)   NSDictionary *editingItem;

3. Implementare **tableView:didSelectRowAtIndexPath:** in **QSTodoListViewController.m** per salvare l'elemento in corso di modifica e quindi chiamare l'elemento Segue per mostrare la visualizzazione dettagli.

        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            NSManagedObject *item = [self.fetchedResultsController objectAtIndexPath:indexPath];
            self.editingItem = [MSCoreDataStore tableItemFromManagedObject:item]; // map from managed object to dictionary

            [self performSegueWithIdentifier:@"detailSegue" sender:self];
        }

4. Implementare **prepareForSegue:sender:** in **QSTodoListViewController.m** per passare l'elemento al **controller di visualizzazione dell'elenco attività** e specificare il callback quando l'utente chiude la visualizzazione dettagli:

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSItemViewController *ivc = (QSItemViewController *) [segue destinationViewController];
                ivc.item = [self.editingItem mutableCopy];

                ivc.editCompleteBlock = ^(NSDictionary *editedValue) {
                    [self.todoService updateItem:editedValue completion:^(NSUInteger index) {
                        self.editingItem = nil;
                    }];
                };
            }
        }

5. Verificare che l'app funzioni con tutte le modifiche apportate finora. Eseguire l'app nel simulatore. Aggiungere voci all'elenco delle attività, quindi fare clic su di esse. Si noterà che il controller di visualizzazione dell'attività non è più vuoto, al contrario vi sono visualizzati i dettagli delle attività.

      ![][add-todo-item-view-controller-6]

### <a name="saving-edits"></a>Aggiungere supporto per il salvataggio delle modifiche

1. Quando si fa clic sul pulsante "Back" nella visualizzazione di navigazione, le modifiche vanno perse. Sono stati inviati dati alla visualizzazione dettagli, ma tali dati non vengono inviati alla visualizzazione master. Poiché viene già passato un puntatore a una copia dell'elemento, è possibile usarlo per recuperare l'elenco di aggiornamenti eseguito sull'elemento e aggiornarlo nel server. Per iniziare, aggiornare la classe wrapper sul server di **QSTodoService** in **QSTodoService.m** rimuovendo l'operazione **completeItem** e aggiungendo una nuova operazione **updateItem**. Questo perché **completeItem** contrassegna un solo elemento come completato, mentre **updateItem** aggiornerà gli elementi.

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:mutable completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

2. Rimuovere la dichiarazione per **completeItem** da **QSTodoService.h** e aggiungere la dichiarazione seguente per **updateItem**:

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion;

3. A questo punto è possibile testare l'app Verificare che l'app funzioni con tutte le modifiche apportate finora. Eseguire l'app nel simulatore. Aggiungere voci all'elenco delle attività, quindi fare clic su di esse. Provare a modificare un elemento, quindi tornare indietro. Verificare se la descrizione dell'elemento viene aggiornata nella visualizzazione master dell'app. Aggiornare l'app usando il movimento di trascinamento verso il basso, quindi verificare che la modifica sia riflessa nel servizio remoto.

### <a name="conflict-handling-problem"></a>Problemi di gestione dei conflitti

1. Si esaminerà di seguito la situazione in cui due client diversi cercano di modificare lo stesso elemento dati nello stesso momento. Nell'esempio seguente è presente un elemento "Mobile Services is Cool!" Verrà modificato, ad esempio, in "I love Mobile Services!" in un dispositivo mentre in un altro dispositivo verrà modificato in "I love Azure!".

      ![][conflict-handling-problem-1]

2. Avviare l'app in due posizioni: su due dispositivi iOS oppure nel simulatore e su un dispositivo iOS. Se non è disponibile un dispositivo fisico su cui eseguire il test, avviare un'istanza nel simulatore, quindi, usando un client REST, inviare una richiesta PATCH al servizio mobile. L'URL della richiesta PATCH riflette il nome del servizio mobile, il nome della tabella delle attività e l'ID della tabella delle attività in corso di modifica, mentre l'intestazione x-zumo-application è la chiave dell'applicazione:

        PATCH https://donnam-tutorials.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: xuAdWVDcLuCNfkTvOfaqzCCSBVHqoy96

        {
            "id": "CBBF4464-E08A-47C9-B6FB-6DCB30ACCE7E",
            "text": "I love Azure!"
        }

3. Aggiornare ora gli elementi nelle due istanze dell'app. Verrà visualizzato un errore nel log dell'output in Xcode:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  Questo perché, nella chiamata a **pullWithQuery:completion:** del blocco di completamento, il parametro di errore sarà non Null, il che causerà la visualizzazione dell'errore sull'output tramite **NSLog**.

### <a name="service-add-conflict-handling"></a>Aggiornare QSTodoService per supportare la gestione dei conflitti

1. Si farà in modo che l'utente decida come gestire il conflitto eseguendo azioni nel client. Per fare ciò, verrà implementato il protocollo **MSSyncContextDelegate**. Sia in **QSTodoService.h** che in **QSTodoService.m**, modificare la dichiarazione del metodo factory **(QSTodoService *)defaultService;** nell'istruzione riportata di seguito, in modo da ricevere il delegato del contesto di sincronizzazione come parametro.

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. In **QSTodoService.m** modificare la riga **init** come indicato di seguito, ricevendo nuovamente il contesto di sincronizzazione come parametro:

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3. In **QSTodoService.m** modificare la chiamata **init** in **defaultServiceWithDelegate** in **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. Tornando in **QSTodoService.m**, modificare l'inizializzazione di **self.client.syncContext** per passare **syncDelegate** invece di **Null** per il delegato:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### <a name="add-alert-view"></a>Aggiungere l'helper della vista avvisi dell'interfaccia utente per supportare la gestione dei conflitti

1. Se è presente un conflitto, verrà consentito all'utente di scegliere la versione da mantenere:
  * mantenere la versione del client (che esegue l'override della versione del server),
  * mantenere la versione del server (che aggiorna la tabella locale del client), oppure
  * non mantenere nessuna delle versioni (annulla il push e lascia l'operazione in sospeso).

  Poiché potrebbe verificarsi un altro aggiornamento durante la visualizzazione del prompt, si continuerà a visualizzare le opzioni fino a quando il server smette di restituire un errore come risposta. Nel codice verrà usata una classe helper che mostra una vista avvisi e accetta un delegato richiamato quando viene visualizzata la vista avvisi. Definire per prima la classe helper **QSUIAlertViewWithBlock**.

2. Aggiungere la nuova classe, **QSUIAlertViewWithBlock**, usando Xcode, quindi sovrascrivere **QSUIAlertViewWithBlock.h** con i contenuti seguenti:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. Sovrascrivere quindi **QSUIAlertViewWithBlock.m** con il file seguente:

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

### <a name="add-conflict-handling"></a>Aggiungere il gestore dei conflitti al controller di visualizzazione dell'elenco attività

1. In **QSTodoListViewController.m** modificare **viewDidLoad**. Sostituire la chiamata a **defaultService** con una chiamata a **defaultServiceWithDelegate**:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. In **QSTodoListViewController.h** aggiungere **&lt;MSSyncContextDelegate&gt;** alla dichiarazione dell'interfaccia, in modo da implementare il protocollo **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Aggiungere l'istruzione di importazione seguente nella parte superiore di **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4. Infine, si aggiungeranno le due operazioni seguenti a **QSTodoListViewController.m** per usare la classe helper e richiedere all'utente di riconciliare il conflitto usando uno di tre metodi.

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

### <a name="test-app"></a>Testare l'app

Si procederà ora al test dell'app con i conflitti. Modificare lo stesso elemento in due diverse istanze dell'app in esecuzione nello stesso momento o usando l'app e un client REST.

Eseguire il movimento di aggiornamento nelle istanze delle app mediante trascinamento dall'alto. A questo punto verrà visualizzata una richiesta di risoluzione del conflitto:

![][conflict-ui]


### Riepilogo

Al fine di configurare il progetto completo da [Introduzione ai dati offline] per il rilevamento dei conflitti, si è inizialmente aggiunta la possibilità di modificare e aggiornare le attività.

A tale scopo, si è aggiunto un nuovo controller di visualizzazione dei dettagli delle attività, connesso al controller di visualizzazione principale, quindi si è aggiunta la possibilità di salvare modifiche e di eseguirne il push nel cloud.

Si è quindi appreso cosa succede quando si verifica un conflitto. Si è aggiunto supporto per un gestore di conflitti mediante l'implementazione del protocollo **MSSyncContextDelegate**. Si è anche aggiunto il supporto per il delegato del contesto di sincronizzazione tramite la classe di interfaccia del server di **QSTodoService**, **QSTodoListViewController** e delle classi di supporto.

Nel corso della procedura, si è aggiunta la classe helper **QSUIAlertViewWithBlock** per visualizzare un avviso agli utenti, quindi si è completato il lavoro aggiungendo codice a **QSTodoListViewController** per chiedere all'utente di riconciliare il conflitto in uno di tre modi.

<!-- URLs. -->

[Aggiornare il progetto dell'app per consentire la modifica]: #update-app
[Aggiornare il controller di visualizzazione dell'elenco attività]: #update-list-view
[Aggiungere il controller di visualizzazione dell'attività]: #add-view-controller
[Aggiungere il controller di visualizzazione dell'attività e l'elemento Segue allo storyboard]: #add-segue
[Aggiungere dettagli elemento al controller di visualizzazione dell'attività]: #add-item-details
[Aggiungere supporto per il salvataggio delle modifiche]: #saving-edits
[Problemi di gestione dei conflitti]: #conflict-handling-problem
[Aggiornare QSTodoService per supportare la gestione dei conflitti]: #service-add-conflict-handling
[Aggiungere l'helper della vista avvisi dell'interfaccia utente per supportare la gestione dei conflitti]: #add-alert-view
[Aggiungere il gestore dei conflitti al controller di visualizzazione dell'elenco attività]: #add-conflict-handling
[Testare l'app]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Controlli Segmented]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creazione di una connessione di outlet]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Creare un'interfaccia utente]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Aggiunta di un elemento Segue tra scene in uno storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Aggiunta di una scena a uno storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Introduzione ai dati offline]: mobile-services-ios-get-started-offline-data.md
[Introduzione ai dati offline in Servizi mobili]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md

<!---HONumber=July15_HO4-->