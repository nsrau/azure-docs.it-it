<properties urlDisplayName="Using Offline Data" pageTitle="Uso della sincronizzazione dei dati offline in Servizi mobili (iOS) | Mobile Developer Center" metaKeywords="" description="Informazioni su come usare Servizi mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'applicazione per iOS" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Introduzione alla sincronizzazione dei dati offline in Servizi mobili


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


Questa esercitazione illustra la funzionalità di sincronizzazione offline di Servizi mobili in iOS, grazie alla quale gli sviluppatori possono scrivere app utilizzabili anche se l'utente finale non ha accesso alla rete.

La sincronizzazione offline ha diversi usi potenziali:

* Migliorare la velocità di risposta dell'app memorizzando i dati del server nella cache locale del dispositivo
* Rendere le app resilienti rispetto alla connettività di rete intermittente
* Permettere agli utenti finali di creare e modificare i dati anche in mancanza di accesso di rete, supportando scenari con connettività scarsa o assente
* Sincronizzare i dati tra più dispositivi e rilevare i conflitti quando lo stesso record viene modificato da due dispositivi

In questa esercitazione verrà illustrato come aggiornare l'app creata nell'esercitazione [Introduzione a Servizi mobili] per supportare le funzionalità offline di Servizi mobili di Azure. Quindi, verranno aggiunti dati in uno scenario offline, verrà effettuata la sincronizzazione degli elementi con il database online, quindi si effettuerà l'accesso al portale di gestione di Azure per visualizzare le modifiche apportate durate l'esecuzione dell'app.

>[WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e ottenere un massimo di 10 servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">versione di valutazione gratuita di Azure</a>.

In questa esercitazione viene descritto come è possibile usare Servizi mobili di Azure per archiviare e recuperare i dati da un'app di Windows Store. e vengono pertanto riproposte molte delle procedure già completate nella guida introduttiva a Servizi mobili. Se si tratta della prima esperienza con Servizi mobili, è consigliabile iniziare dall'esercitazione [Introduzione a Servizi mobili].

>[WACOM.NOTE] È possibile ignorare queste sezioni e passare al download di una versione del progetto introduttivo che dispone già di supporto offline.  Per scaricare un progetto con il supporto offline abilitato, vedere l'[esempio introduttivo offline relativo a iOS].


In questa esercitazione vengono descritte le operazioni di base seguenti:

1. [Ottenere l'app di guida introduttiva di esempio]
2. [Scaricare la versione di anteprima di SDK e aggiornare il framework]
3. [Configurare Core Data]
4. [Definizione del modello di Core Data]
5. [Inizializzazione e uso della tabella di sincronizzazione e del contesto di sincronizzazione]
6. [Testare l'app]

## <a name="get-app"></a>Ottenere l'app di guida introduttiva di esempio

Seguire le istruzioni disponibili in [Introduzione a Servizi mobili] e scaricare il progetto di guida introduttiva.

## <a name="update-app"></a>Scaricare la versione di anteprima di SDK e aggiornare il framework

1. Per aggiungere il supporto offline all'applicazione, scaricare una versione di Mobile Services SDK per iOS che supporta la sincronizzazione offline. Poiché si tratta di una funzionalità di anteprima, non è ancora disponibile l'SDK ufficialmente scaricabile. [Scaricare qui la versione di anteprima di SDK].

2. Rimuovere quindi il riferimento **WindowsAzureMobileServices.framework** esistente dal progetto in Xcode selezionandolo, quindi facendo clic sul menu **Modifica** e scegliendo "Sposta nel cestino" per eliminare effettivamente i file.

      ![][update-framework-1]

3. Decomprimere i contenuti della nuova versione di anteprima di SDK, quindi trascinare e rilasciare il nuovo **WindowsAzureMobileServices.framework** SDK per sostituire l'SKD precedente. Assicurarsi che l'opzione "Copy items into destination group's folder (if needed)" sia selezionata.

      ![][update-framework-2]


## <a name="setup-core-data"></a>Configurare Core Data

1. Mobile Services SDK per iOS permette di usare qualsiasi archivio permanente, purché sia conforme con il protocollo **MSSyncContextDataSource**. L'SDK include un'origine dati che implementa questo protocollo basato su [Core Data].

2. Poiché l'app usa i dati principali, passare a **Targets** --> **Build Phases** e in **Link Binary with Libraries** aggiungere **CoreData.framework**.

      ![][core-data-1]

      ![][core-data-2]

3. Si sta aggiungendo Core Data a un progetto esistente in Xcode che non supporta già questo framework. È quindi necessario aggiungere codice standard a diverse parti del progetto. Aggiungere prima di tutto il codice seguente in **QSAppDelegate.h**:

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. Sostituire quindi i contenuti di **QSAppDelegate.m** con il codice seguente. Questo codice è quasi uguale al codice ottenuto quando si crea una nuova applicazione in Xcode e si seleziona la casella di controllo "Use Core Data", ma si usa un tipo di concorrenza di coda privata durante l'inizializzazione di **_managedObjectContext**. Dopo questa modifica si è quasi pronti per usare Core Data, ma non sono state ancora eseguite operazioni con questo framework.

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>Definizione del modello di Core Data

1. Per continuare a configurare l'applicazione con Core Data, definire il modello di dati. Il modello di dati non verrà usato subito. È prima di tutto necessario definire il modello o lo schema di Core Data. Per iniziare, fare clic su **File** -> **New File**, quindi selezionare **Data Model** nella sezione **Core Data**. Quando viene richiesto un nome di file, usare **QSTodoDataModel.xcdatamodeld**.

      ![][defining-core-data-main-screen]

2. Definire quindi le entità effettive (tabelle) necessarie. Verranno create tre tabelle (entità) mediante l'editor di modelli di Core Data. Per altre informazioni, vedere la pagina relativa alla [Guida per l'editor di modelli di Core Data].

  * TodoItem: per l'archiviazione degli elementi stessi.
  * MS_TableOperations: per tenere traccia degli elementi da sincronizzare con il server (necessario per il corretto funzionamento della funzionalità offline).
  * MS_TableOperationErrors: per tenere traccia di eventuali errori verificatisi durante la sincronizzazione offline (necessario per il corretto funzionamento della funzionalità offline).

      ![][defining-core-data-model-editor]

3. Definire le tre entità come illustrato di seguito. Salvare il modello, quindi compilare il progetto per assicurarsi che sia stato configurato correttamente. La configurazione dell'applicazione per l'uso di Core Data è stata completata, ma l'app non usa ancora Core Data.

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | Attributo  |  Tipo   |
    |----------- |  ------ |
    | id         | Stringa  |
    | complete   | Booleano |
    | text       | Stringa  |
    | ms_version | Stringa  |

    **MS_TableOperations**

    | Attributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Valore integer 64  |
    | properties | Dati binari |
    | itemId     | Stringa      |
    | table      | Stringa      |

    **MS_TableOperationErrors**

    | Attributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Stringa      |
    | properties | Dati binari |

## <a name="setup-sync"></a> Inizializzazione e uso della tabella di sincronizzazione e del contesto di sincronizzazione

1. Per iniziare a memorizzare i dati nella cache in modalità offline, sostituire l'uso di **MSTable** con **MSSyncTable** per accedere al servizio mobile. A differenza di una tabella **MSTable** normale, una tabella di sincronizzazione è simile a una tabella locale che permette anche di eseguire il push di modifiche apportate localmente in una tabella remota e di eseguire il pull di queste modifiche in locale. In **QSTodoService.h** rimuovere la definizione della proprietà **table**:

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. In **QSTodoService.m** rimuovere le due righe seguenti in **init**:

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. Ancora in **QSTodoService.m** inizializzare quindi il contesto di sincronizzazione in **MSClient** con l'implementazione dell'archivio dati basato su Core Data creata in precedenza. Il contesto è responsabile del rilevamento degli elementi modificati localmente e dell'invio di questi elementi al server quando viene avviata un'operazione push. Per inizializzare il contesto è necessaria un'origine dati (l'implementazione **MSCoreDataStore** del protocollo), oltre a un'implementazione facoltativa di **MSSyncContextDelegate**. Inserire queste righe immediatamente sopra le due righe inserite in precedenza.

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. Aggiornare le operazioni in **QSTodoService.m** in modo da usare la tabella di sincronizzazione invece della tabella normale. Sostituire prima di tutto **refreshDataOnSuccess** con l'implementazione seguente. I dati verranno recuperati dal servizio. È quindi necessario aggiornare il servizio in modo che usi una tabella di sincronizzazione, richiedere alla tabella di sincronizzazione di eseguire il pull dei soli elementi corrispondenti ai criteri e iniziare a caricare i dati dalla tabella di sincronizzazione locale nella proprietà **items** del servizio. Questo codice permette a  **refreshDataOnSuccess** di eseguire il pull dei dati dalla tabella remota nella tabella locale (di sincronizzazione). È in genere consigliabile eseguire il pull solo di un sottoinsieme della tabella, in modo da non sovraccaricare il client con informazioni che potenzialmente non necessarie.

    Per questa operazione e per le operazioni rimanenti successive è necessario eseguire il wrapping delle chiamate ai blocchi di completamento in una chiamata **dispatch_async** per il thread principale. Quando si inizializza il contesto di sincronizzazione, non viene passato alcun parametro di callback. Il framework crea quindi una coda seriale predefinita, che invia i risultati di tutte le operazioni syncTable in un thread in background. Quando si modificano i componenti dell'interfaccia utente, è necessario restituire il codice al thread dell'interfaccia utente.

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. Sostituire quindi **addItem** in **QSTodoService.m** come indicato di seguito. Questa modifica comporta l'accodamento dell'operazione, in modo che venga eseguito il push delle modifiche al servizio remoto per permetterne la visualizzazione a qualsiasi utente:

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. Aggiornare **completeItem** in **QSTodoService.m** come indicato di seguito. A differenza di **MSTable**, il blocco di completamento dell'operazione **update** per **MSSyncTable** non include alcun elemento aggiornato. Con **MSTable**, il server modifica l'elemento in fase di aggiornamento e tale modifica si riflette sul client. Con **MSSyncTable** gli elementi aggiornati non vengono modificati e il blocco di completamento non include alcun parametro.

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. Aggiungere la dichiarazione di operazione seguente di **syncData** a **QSTodoService.h**:

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. In **QSTodoListViewController.m** sostituire la chiamata a **[self refresh]** alla fine dell'operazione **viewDidLoad** con il codice seguente:

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. È ora possibile testare l'app offline. Aggiungere alcuni elementi all'app, quindi passare al portale di gestione di Azure e verificare la scheda **Dati** relativa all'app. Come si può notare, non sono stati ancora aggiunti elementi.

12. Eseguire quindi il movimento di aggiornamento nell'app, trascinandola dall'alto verso il basso. Tornare al portale di gestione di Azure e verificare di nuovo la scheda **Dati**. Come si può notare, i dati sono stati salvati nel cloud. È anche possibile chiudere l'app dopo l'aggiunta di un elemento o dopo la modifica di un elemento, se la funzionalità per la modifica di elementi è abilitata nell'app. Al riavvio dell'app verrà eseguita la sincronizzazione con il server e le modifiche verranno salvate.

13. Quando il client apporta alcune modifiche agli elementi in modalità locale, le modifiche vengono archiviate nel contesto di sincronizzazione per essere inviate al server. Un'operazione *push* invia le modifiche rilevate al server remoto, ma non vengono effettuate chiamate push al server. Tuttavia, *prima dell'esecuzione di un'operazione, viene in genere eseguito il push al server di eventuali operazioni in sospeso*, quindi viene comunque eseguita automaticamente un'operazione push per evitare conflitti. Questa è la ragione per cui non vengono effettuate chiamate esplicite a *push* in questa app.

## <a name="test-app"></a>Testare l'app

È infine possibile testare l'applicazione offline. Aggiungere alcuni elementi all'app. Passare quindi al portare ed esaminare i dati oppure usare uno strumento di rete, ad esempio PostMan o Fiddler, per eseguire query direttamente nella tabella.

Come si può notare, gli elementi non sono stati ancora aggiunti al servizio. Eseguire quindi il movimento di aggiornamento nell'app, trascinandola dall'alto verso il basso. Come si può notare, i dati sono stati salvati nel cloud. È anche possibile chiudere l'app dopo l'aggiunta di alcuni elementi. Al riavvio, l'app verrà sincronizzata con il server e le modifiche verranno salvate.

## Passaggi successivi

* [Gestione dei conflitti con il supporto offline per Servizi mobili]

<!-- URLs. -->

[Ottenere l'app di guida introduttiva di esempio]: #get-app
[Scaricare la versione di anteprima di SDK e aggiornare il framework]: #update-app
[Configurare Core Data]: #setup-core-data
[Definizione del modello di Core Data]: #defining-core-data
[Inizializzazione e uso della tabella di sincronizzazione e del contesto di sincronizzazione]: #setup-sync
[Testare l'app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Guida dell'editor di modelli di Core Data]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creazione di una connessione di outlet]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Creare un'interfaccia utente]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Aggiunta di un elemento Segue tra scene in uno storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Aggiunta di una scena a uno storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Scaricare qui la versione di anteprima di SDK]: http://aka.ms/Gc6fex
[Come usare la libreria client di Servizi mobili per iOS]: /it-it/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Esempio introduttivo per iOS offline]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-ios-get-started/
[Introduzione ai dati]: /it-it/documentation/articles/mobile-services-ios-get-started-data/
[Gestione dei conflitti con il supporto offline per Servizi mobili]: /it-it/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
