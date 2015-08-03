<properties
	pageTitle="Introduzione alla sincronizzazione dei dati offline in Servizi mobili (iOS) | Mobile Dev Center"
	description="Informazioni su come usare Servizi mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'applicazione per iOS"
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

# Introduzione alla sincronizzazione dei dati offline in Servizi mobili

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

La sincronizzazione offline consente di visualizzare, aggiungere o modificare i dati in un'app mobile anche se non è presente alcuna connessione di rete. Questa esercitazione descrive la capacità dell'app di memorizzare automaticamente le modifiche apportate in un database locale offline e di sincronizzare le modifiche ogni volta che torna online.

La sincronizzazione offline presenta diversi vantaggi:

* Migliora la velocità di risposta dell'app memorizzando i dati del server nella cache locale del dispositivo
* Rende le app resilienti rispetto alla connettività di rete intermittente
* Consente di creare e modificare i dati anche in situazioni di connettività limitata
* Esegue la sincronizzazione dei dati su più dispositivi
* Rileva i conflitti quando lo stesso record viene modificato da due dispositivi

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile iscriversi per accedere a una versione di valutazione di Azure e [ottenere servizi mobili gratuiti che potranno essere usati anche dopo il termine del periodo di valutazione](http://azure.microsoft.com/pricing/details/mobile-services/). Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target="_blank").

Questa esercitazione è basata sull'esercitazione [Guida introduttiva per Servizi mobili], che deve essere completata per prima. È consigliabile esaminare innanzitutto il codice relativo alla sincronizzazione offline già presente nella Guida introduttiva.

## <a name="review-sync"></a>Esaminare il codice di sincronizzazione di Servizi mobili

La sincronizzazione offline di Servizi mobili di Azure consente agli utenti finali di interagire con un database locale quando la rete non è accessibile. Per usare queste funzionalità nell'app, è possibile inizializzare il contesto di sincronizzazione di `MSClient` e fare riferimento a un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `MSSyncTable`.

* In **QSTodoService.m** notare che il tipo del membro `syncTable` è `MSSyncTable`. La sincronizzazione offline usa questo membro anziché `MSTable`. Quando si usa una tabella di sincronizzazione, tutte le operazioni vengono inviate all'archivio locale e vengono sincronizzate con il servizio remoto solo mediante operazioni push e pull esplicite.

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

Per ottenere un riferimento a una tabella di sincronizzazione, usare il metodo `syncTableWithName`. Per rimuovere la funzionalità di sincronizzazione offline, usare invece `tableWithName`.

* In **QSTodoService.m**, prima di eseguire le operazioni su tabella, l'archivio locale viene inizializzato in `QSTodoService.init`:

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

In questo modo viene creato un archivio locale mediante l'interfaccia `MSCoreDataStore`. È anche possibile fornire un archivio locale diverso, implementando il protocollo `MSSyncContextDataSource`.

Il primo parametro di `initWithDelegate` specifica un gestore di conflitti, ma poiché è stato passato `nil`, il gestore predefinito di conflitti genera un errore per ogni conflitto. Per informazioni dettagliate sull'implementazione di un gestore di conflitti personalizzato, vedere [Gestione dei conflitti con il supporto offline per Servizi mobili].

* In **QSTodoService.m** `syncData` inserisce innanzitutto le nuove modifiche e quindi chiama `pullData` per ottenere dati dal servizio remoto. In `syncData` come prima cosa viene chiamato `pushWithCompletion` nel contesto di sincronizzazione. Questo metodo fa parte di `MSSyncContext` invece che della tabella di sincronizzazione perché esegue il push delle modifiche in tutte le tabelle. Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni creation, update o delete) vengono inviati al server. Alla fine di `syncData` viene chiamato l'helper `pullData`.

In questo esempio l'operazione push non è strettamente necessaria. Se nel contesto di sincronizzazione per la tabella che esegue un'operazione push sono presenti modifiche in sospeso, pull esegue sempre prima un'operazione push. Tuttavia, se si dispone di più di una tabella di sincronizzazione, è necessario chiamare push in modo esplicito affinché le tabelle siano coerenti tra loro.

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* Successivamente, in **QSTodoService.m**, `pullData` ottiene nuovi dati corrispondenti a una query. `pullData` chiama `MSSyncTable.pullWithQuery` per recuperare i dati remoti e memorizzarli in locale. `pullWithQuery` consente anche di specificare una query per filtrare i record che si desidera recuperare. In questo esempio, la query recupera semplicemente tutti i record nella tabella `TodoItem` remota.

Il secondo parametro di `pullWithQuery` è un ID di query usato per la _sincronizzazione incrementale_. La sincronizzazione incrementale recupera solo i record modificati dopo l'ultima sincronizzazione, usando il timestamp del record `UpdatedAt`, denominato `ms_updatedAt` nell'archivio locale. L'ID di query è una stringa descrittiva univoca per ogni query logica presente nell'app. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `nil` come ID di query. Questa operazione non è efficace poiché consente di recuperare tutti i record in ogni operazione pull.

```
      -(void)pullData:(QSCompletionBlock)completion
      {
          MSQuery *query = [self.syncTable query];

          // Pulls data from the remote server into the local table.
          // We're pulling all items and filtering in the view
          // query ID is used for incremental sync
          [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
              [self logErrorIfNotNil:error];

              // Let the caller know that we have finished
              if (completion != nil) {
                  dispatch_async(dispatch_get_main_queue(), completion);
              }
          }];
      }
```


>[AZURE.NOTE]Per rimuovere i record dall'archivio locale del dispositivo quando sono stati eliminati dal database del servizio mobile, è necessario abilitare l'[eliminazione temporanea]. In alternativa, l'app deve periodicamente chiamare `MSSyncTable.purgeWithQuery` per ripulire l'archivio locale.


* In **QSTodoService.m** i metodi `addItem` e `completeItem` chiamano `syncData` dopo la modifica dei dati. In **QSTodoListViewController.m** il metodo `refresh` chiama anche `syncData` in modo che l'interfaccia utente visualizzi i dati più recenti ad ogni aggiornamento e avvio (`init` chiama `refresh`).

Poiché l'applicazione chiama `syncData` ad ogni modifica dei dati, l'app presuppone che l'utente sia online ogni volta che i dati nell'app vengono modificati.

## <a name="review-core-data"></a>Esaminare il modello Core Data

Quando si usa l'archivio offline Core Data, è necessario definire particolari tabelle e campi all'interno del modello di dati. L'app di esempio include già un modello di dati nel formato corretto. Questa sezione illustra le tabelle e il relativo uso.

- Aprire **QSDataModel.xcdatamodeld**. Sono presenti le definizioni di quattro tabelle, tre usate dall'SDK e una per gli elementi todo:

      * MS_TableOperations: per tenere traccia degli elementi da sincronizzare con il server
      * MS_TableOperationErrors: per tenere traccia degli errori che si verificano durante la sincronizzazione offline
      * MS_TableConfig: per tenere traccia dell'ora dell'ultimo aggiornamento dell'ultima operazione di sincronizzazione per tutte le operazioni pull
      * TodoItem: per archiviare gli elementi todo. Le colonne di sistema **ms_createdAt**, **ms_updatedAt** e **ms_version** sono proprietà di sistema facoltative.

>[AZURE.NOTE]Mobile Services SDK riserva i nomi di colonna che iniziano con "**`ms_`**". Non usare questo prefisso in un valore diverso da quello delle colonne di sistema. In caso contrario, quando si usa il servizio remoto vengono modificati i nomi di colonna.

- Quando si usa la funzionalità di sincronizzazione offline, è necessario definire le tabelle di sistema come illustrato di seguito.

    ### Tabelle di sistema

    #### MS_TableOperations

    | Attributo | Tipo |
    |-------------- |   ------    |
    | id (obbligatorio) | Valore integer 64 |
    | itemId | String |
    | properties | Dati binari |
    | tabella | String |
    | tableKind | Integer 16 |

    #### MS_TableOperationErrors

    | Attributo | Tipo |
    |-------------- | ----------  |
    | id (obbligatorio) | Stringa |
    | operationId | Valore integer 64 |
    | properties | Dati binari |
    | tableKind | Integer 16 |

    #### MS_TableConfig


    | Attributo | Tipo |
    |-------------- | ----------  |
    | id (obbligatorio) | Stringa |
    | key | String |
    | keyType | Valore integer 64 |
    | tabella | String |
    | value | String |

    ### Tabella dati

    #### TodoItem

    | Attributo | Tipo | Nota | 
    |-------------- |  ------ | -------------------------------------------------------|
    | id (obbligatorio) | Stringa | chiave primaria nell'archivio remoto (obbligatoria) |
    | complete | Boolean | campo elemento ToDo |
    | text | String | campo elemento ToDo |
    | ms_createdAt | Data | (optional) maps to __createdAt system property | | ms_updatedAt | Date | (optional) maps to __updatedAt system property | | ms_version | String | (optional) used to detect conflicts, maps to __version |



## <a name="setup-sync"></a>Modificare il comportamento di sincronizzazione dell'app

In questa sezione si procederà alla modifica dell'app in modo che non esegua la sincronizzazione all'avvio né quando vengono inseriti e aggiornati elementi, ma solo quando viene eseguito il movimento di aggiornamento.

* In **QSTodoListViewController.m** modificare `viewDidLoad` per rimuovere la chiamata a `[self refresh]` alla fine del metodo. Ora all'avvio dell'app i dati non verranno sincronizzati con il server, ma solo in locale.

* In **QSTodoService.m** modificare `addItem` in modo che non esegua la sincronizzazione dopo l'inserimento dell'elemento. Rimuovere il blocco `self syncData` e sostituirlo con quanto segue:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* Analogamente, sempre in **QSTodoService.m**, in `completeItem` rimuovere il blocco per `self syncData` e sostituirlo con il seguente:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>Testare l'app

In questa sezione si disattiverà il Wi-Fi nel simulatore per creare uno scenario offline. Quando si aggiungono elementi di dati, questi vengono conservati nell'archivio Core Data locale, ma non sincronizzati con il servizio mobile.

1. Disattivare la connessione Internet sul Mac. La disattivazione del WiFi solo nel simulatore iOS potrebbe non avere effetto, perché il simulatore potrebbe usare ancora la connessione Internet del Mac host. Disattivare quindi Internet per il computer. In questo modo viene simulato uno scenario offline.

2. Aggiungere alcuni elementi todo o completare alcuni elementi. Uscire dal simulatore (o forzare la chiusura dell'app) e riavviare. Verificare che le modifiche siano state conservate. Si noti che gli elementi di dati vengono comunque visualizzati perché conservati nell'archivio Core Data locale.

3. Visualizzare i contenuti della tabella TodoItem remota. Verificare che i nuovi elementi _non_ siano stati sincronizzati con il server.

   - Per il back-end JavaScript, accedere al portale di gestione e fare clic sulla scheda Dati per visualizzare i contenuti della tabella `TodoItem`.
   - Per il back-end .NET, visualizzare il contenuto della tabella mediante uno strumento SQL, ad esempio SQL Server Management Studio, o mediante un client REST, ad esempio Fiddler o Postman.

4. Attivare il Wi-Fi nel simulatore iOS. Successivamente, eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi. Verranno visualizzati un indicatore di avanzamento e il testo "Syncing...".

5. Visualizzare nuovamente i dati di TodoItem. Dovrebbero essere visualizzati gli elementi TodoItems nuovi e modificati.

## Riepilogo

Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `MSSyncTable` ed è stato inizializzato `MSClient.syncContext` in un archivio locale. In questo caso l'archivio locale era un database basato su Core Data.

Quando si usa un archivio locale Core Data, è necessario definire diverse tabelle con le [proprietà di sistema corrette][Review the Core Data model]. Le normali operazioni per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server, usare `MSSyncTable.pullWithQuery` e `MSClient.syncContext.pushWithCompletion`:

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## Passaggi successivi

* [Gestione dei conflitti con il supporto offline per Servizi mobili]

* [Uso dell'eliminazione temporanea in Servizi mobili][Soft Delete]

## Risorse aggiuntive

* [Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]

* [Azure Friday: App con supporto offline in Servizi mobili di Azure] (nota: le demo sono per Windows, la descrizione delle funzionalità si applica a tutte le piattaforme)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Gestione dei conflitti con il supporto offline per Servizi mobili]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[eliminazione temporanea]: mobile-services-using-soft-delete.md

[Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: App con supporto offline in Servizi mobili di Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Guida introduttiva per Servizi mobili]: mobile-services-ios-get-started.md

<!---HONumber=July15_HO4-->