---
title: Abilitare la sincronizzazione offline per l&quot;app per dispositivi mobili di Azure (iOS)
description: Informazioni su come usare le app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un&quot;applicazione iOS
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: f9f5fd28db7babfe400aeb55ba1df9ac5d8d535b
ms.openlocfilehash: 7aee0f60d331f40514f41c20e09fa2c9d9a21233


---
# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Abilitare la sincronizzazione offline per l'app per dispositivi mobili per iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione descrive la funzionalità di sincronizzazione offline delle app per dispositivi mobili di Azure per iOS. La sincronizzazione offline consente agli utenti finali di interagire con un'app&mdash;visualizzando, aggiungendo e modificando i dati&mdash;anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il back-end remoto.

Se questa è la prima esperienza con le app per dispositivi mobili di Azure, è consigliabile completare prima l'esercitazione [Creare un'app iOS]. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all' [utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## <a name="a-namereview-syncareview-the-client-sync-code"></a><a name="review-sync"></a>Verificare il codice di sincronizzazione del client
Il progetto client scaricato per l'esercitazione [Creare un'app iOS] contiene già il codice che supporta la sincronizzazione offline mediante un database basato sui dati principali locali. Questa sezione è un riepilogo degli elementi già inclusi nel codice dell'esercitazione. Per una panoramica concettuale della funzionalità, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

La funzionalità per la sincronizzazione di dati offline delle app per dispositivi mobili di Azure consente agli utenti finali di interagire con un database locale quando la rete non è accessibile. Per usare queste funzionalità nell'app, è possibile inizializzare il contesto di sincronizzazione di `MSClient` e fare riferimento a un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `MSSyncTable` .

1. In **QSTodoService.m** (Objective-C) o **ToDoTableViewController.swift** (Swift) notare che il tipo del membro `syncTable` è `MSSyncTable`. La sincronizzazione offline usa questa interfaccia della tabella di sincronizzazione anziché `MSTable`. Quando si usa una tabella di sincronizzazione, tutte le operazioni vengono inviate all'archivio locale e vengono sincronizzate con il back-end remoto solo mediante operazioni push e pull esplicite.
   
    Per ottenere un riferimento a una tabella di sincronizzazione, usare il metodo `syncTableWithName` su `MSClient`. Per rimuovere la funzionalità di sincronizzazione offline, usare invece `tableWithName` .
    
2. Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Di seguito è riportato il codice pertinente. 
   
   **Objective-C**:

   Nel metodo `QSTodoService.init` :
   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
   **Swift**:

   Nel metodo `ToDoTableViewController.viewDidLoad` :
   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Verrà creato un archivio locale usando l'interfaccia `MSCoreDataStore`, disponibile in Mobile App SDK. È anche possibile fornire un archivio locale diverso, implementando il protocollo `MSSyncContextDataSource` . Il primo parametro di `MSSyncContext` consente anche di specificare un gestore di conflitto. Poiché è stato passato `nil`, si otterrà il gestore di conflitti predefinito, che non consente l'esecuzione di operazioni in caso di conflitto.

3. A questo punto, si esegue l'operazione effettiva di sincronizzazione e si ottengono i dati dal back-end remoto.
   
   **Objective-C**:

   `syncData` inserisce innanzitutto le nuove modifiche e quindi chiama `pullData` per ottenere dati dal back-end remoto. Il metodo `pullData` , a sua volta, ottiene dati che corrispondono a una query:
   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // push all changes in the sync context, then pull new data
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

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
   **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will just discard our changes and keep the servers copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   } 
   ```

    Nella versione Objective-C, in `syncData`, viene innanzitutto chiamato `pushWithCompletion` nel contesto di sincronizzazione. Questo metodo fa parte di `MSSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle. Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server. Viene quindi chiamato l'helper `pullData`, che chiama `MSSyncTable.pullWithQuery` per recuperare i dati remoti e archiviarli nel database locale.

    Nella versione Swift, non si verifica alcuna chiamata a `pushWithCompletion`. Questo avviene perché l'operazione push non è strettamente necessario. Se nel contesto di sincronizzazione per la tabella che esegue un'operazione push sono presenti modifiche in sospeso, pull effettua sempre prima un'operazione push. Tuttavia, se sono presenti più tabelle di sincronizzazione, è preferibile chiamare in modo esplicito push per garantire la coerenza nelle tabelle correlate.

    Sia nella versione Objective-C che nella versione Swift, il metodo `pullWithQuery` consente di specificare una query per filtrare i record da recuperare. In questo esempio, la query recupera semplicemente tutti i record nella tabella `TodoItem` remota.

    Il secondo parametro di `pullWithQuery` è un ID di query usato per la *sincronizzazione incrementale*. La sincronizzazione incrementale recupera solo i record modificati dopo l'ultima sincronizzazione, usando il timestamp del record `UpdatedAt`, denominato `updatedAt` nell'archivio locale. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `nil` come ID di query. Si noti che questa è una scelta potenzialmente inefficiente, perché in ogni operazione pull verranno recuperati tutti i record.

1. L'app Objective-C esegue la sincronizzazione quando si modificano o si aggiungono dati, quando un utente esegue l'aggiornamento e all'avvio. L'app Swift esegue la sincronizzazione quando un utente esegue l'aggiornamento e all'avvio. 

Poiché l'app esegue la sincronizzazione ogni volta che i dati vengono modificati (Objective-C) oppure a ogni avvio dell'applicazione (Objective-C e Swift), l'app presuppone che l'utente sia online. In un'altra sezione, l'app verrà aggiornata in modo che gli utenti possano apportare modifiche anche offline.

## <a name="a-namereview-core-dataareview-the-core-data-model"></a><a name="review-core-data"></a>Esaminare il modello di Core Data
Quando si usa l'archivio offline Core Data, è necessario definire particolari tabelle e campi all'interno del modello di dati. L'app di esempio include già un modello di dati nel formato corretto. In questa sezione vengono illustrate le tabelle e il relativo uso.

* Aprire **QSDataModel.xcdatamodeld**. Sono presenti le definizioni di quattro tabelle, tre usate dall'SDK e una per gli elementi todo:
  * MS_TableOperations: per tenere traccia degli elementi da sincronizzare con il server
  * MS_TableOperationErrors: per tenere traccia di eventuali errori verificatisi durante la sincronizzazione offline
  * MS_TableConfig: per tenere traccia dell'ora dell'ultimo aggiornamento dell'ultima operazione di sincronizzazione per tutte le operazioni pull
  * TodoItem: per archiviare gli elementi todo. Le colonne di sistema **createdAt**, **updatedAt** e **version** sono proprietà di sistema facoltative.

> [!NOTE]
> Azure Mobile Apps SDK riserva nomi di colonna che iniziano con "**``**". Usare questo prefisso solo per le colonne di sistema. In caso contrario, quando si usa il back-end remoto i nomi di colonna verranno modificati.
> 
> 

* Quando si usa la funzionalità di sincronizzazione offline, è necessario definire le tabelle di sistema come illustrato di seguito.
  
  ### <a name="system-tables"></a>Tabelle di sistema
    **MS_TableOperations**
  
    ![][defining-core-data-tableoperations-entity]
  
  | Attributo | Tipo |
  | --- | --- |
  | id |Valore integer 64 |
  | itemId |String |
  | properties |Dati binari |
  | tabella |String |
  | tableKind |Integer 16 |
  
    <br>**MS_TableOperationErrors**
  
    ![][defining-core-data-tableoperationerrors-entity]
  
  | Attributo | Tipo |
  | --- | --- |
  | id |String |
  | operationId |Valore integer 64 |
  | properties |Dati binari |
  | tableKind |Integer 16 |
  
    <br>**MS_TableConfig**
  
    ![][defining-core-data-tableconfig-entity]
  
  | Attributo | Tipo |
  | --- | --- |
  | id |String |
  | key |String |
  | keyType |Valore integer 64 |
  | tabella |String |
  | value |String |
  
  ### <a name="data-table"></a>Tabella dati
    **TodoItem**
  
  | Attributo | Tipo | Nota |
  | --- | --- | --- |
  | id |Stringa, contrassegnata come obbligatoria |chiave primaria nell'archivio remoto |
  | complete |Boolean |campo elemento ToDo |
  | text |String |campo elemento ToDo |
  | createdAt |Data |(facoltativo) viene mappato alla proprietà di sistema createdAt |
  | updatedAt |Data |(facoltativo) viene mappato alla proprietà di sistema updatedAt |
  | version |String |(facoltativo) viene usato per il rilevamento dei conflitti, viene mappato a version |

## <a name="a-namesetup-syncachange-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>Modificare il comportamento di sincronizzazione dell'app
In questa sezione si procederà alla modifica dell'app in modo che non effettui la sincronizzazione all'avvio né quando vengono inseriti e aggiornati elementi, ma solo quando viene eseguito il movimento di aggiornamento.

**Objective-C**:

1. In **QSTodoListViewController.m** modificare il metodo **viewDidLoad** per rimuovere la chiamata a `[self refresh]` alla fine del metodo. Ora all'avvio dell'app i dati non verranno sincronizzati con il server, ma verrà sincronizzato il contenuto dell'archivio locale.
2. In **QSTodoService.m** modificare la definizione di `addItem` in modo che non esegua la sincronizzazione dopo l'inserimento dell'elemento. Rimuovere il blocco `self syncData` e sostituirlo con quanto segue:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modificare la definizione di `completeItem` come sopra. Rimuovere il blocco per `self syncData` e sostituirlo con quanto segue:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
**Swift**:

1. In `viewDidLoad` in **ToDoTableViewController.swift**, commentare queste due righe per interrompere la sincronizzazione all'avvio. Al momento della stesura di questo articolo, l'app Swift Todo non aggiorna il servizio quando un utente aggiunge o si completa un elemento, ma solo all'avvio.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```
## <a name="a-nametest-appatest-the-app"></a><a name="test-app"></a>Test dell'app
In questa sezione, ci si collegherà a un URL non valido per simulare uno scenario offline. Quando si aggiungono elementi di dati, questi vengono conservati nell'archivio Core Data locale, ma non sincronizzati con il back-end mobile.

1. Modificare l'URL dell'app per dispositivi mobili in **QSTodoService.m** con un URL non valido ed eseguire di nuovo l'app:
   
   **Objective-C** in QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift** in ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Aggiungere alcuni elementi todo. Uscire dal simulatore (o forzare la chiusura dell'app) e riavviare. Verificare che le modifiche siano state conservate.
3. Visualizzare il contenuto della tabella TodoItem remota: 
   * Per un back-end Node.js, passare al [portale di Azure](https://portal.azure.com/) e nel back-end dell'app per dispositivi mobili fare clic su **Tabelle semplici** > **TodoItem** per visualizzare il contenuto della tabella `TodoItem`.  
   * Per il back-end .NET, visualizzare il contenuto della tabella mediante uno strumento SQL, come ad esempio SQL Server Management Studio, o mediante un client REST, ad esempio Fiddler o Postman.
Verificare che i nuovi elementi *non* siano stati sincronizzati con il server:
4. Modificare l'URL ripristinando quello corretto **QSTodoService.m** ed eseguire di nuovo l'applicazione. Eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi. Verrà visualizzato un indicatore di avanzamento.
5. Visualizzare nuovamente i dati di TodoItem. Dovrebbero essere visualizzati gli elementi TodoItems nuovi e modificati.

## <a name="summary"></a>Summary
Per supportare la funzionalità di sincronizzazione offline è stata usata l'interfaccia `MSSyncTable` ed è stato inizializzato `MSClient.syncContext` in un archivio locale. In questo caso l'archivio locale era un database basato su Core Data.

Quando si usa un archivio locale Core Data, è necessario definire svariate tabelle con le [proprietà di sistema corrette](#review-core-data).

Le normali operazioni CRUD per le app per dispositivi mobili di Azure funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server è stato usato il metodo `MSSyncTable.pullWithQuery`.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]
* [Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure] \(nota: il video è relativo ai Servizi mobili, ma il funzionamento della sincronizzazione offline è simile nelle app per dispositivi mobili di Azure\)

<!-- URLs. -->


[Creare un'app iOS]: app-service-mobile-ios-get-started.md
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/



<!--HONumber=Jan17_HO2-->


