---
title: Abilitare la sincronizzazione offline con le app per dispositivi mobili per iOS | Documentazione Microsoft
description: Informazioni su come usare le app per dispositivi mobili del servizio app di Azure per memorizzare i dati nella cache e sincronizzarli offline nelle app iOS.
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
ms.sourcegitcommit: dc5f98fd548512801c705f942e30df5e6b95d542
ms.openlocfilehash: 3271db005133bd7849b8a33dd7fa8f11bf5a29c2
ms.lasthandoff: 01/31/2017


---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Sincronizzare offline le app per dispositivi mobili iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come eseguire la sincronizzazione offline con la funzionalità App per dispositivi mobili di Servizio app di Azure per iOS. La sincronizzazione offline consente agli utenti finali di usare un'app per dispositivi mobili per visualizzare, aggiungere o modificare dati anche in assenza di una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo viene connesso nuovamente alla rete, le modifiche vengono sincronizzate con il back-end remoto.

Se questa è la prima esperienza con la funzionalità App per dispositivi mobili, è consigliabile completare prima l'esercitazione [Creare un'app iOS]. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto i pacchetti di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all' [utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili].

## <a name="review-sync"></a>Verificare il codice di sincronizzazione del client
Il progetto client scaricato per l'esercitazione [Creare un'app iOS] contiene già il codice che supporta la sincronizzazione offline mediante un database basato sui dati principali locali. Questa sezione riepiloga gli elementi già inclusi nel codice dell'esercitazione. Per una panoramica concettuale della funzionalità, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili].

La funzionalità di sincronizzazione dei dati offline di App per dispositivi mobili consente agli utenti finali di interagire con un database locale quando la rete non è disponibile. Per usare queste funzionalità nell'app, è possibile inizializzare il contesto di sincronizzazione di `MSClient` e fare riferimento a un archivio locale. Fare quindi riferimento alla tabella tramite l'interfaccia **MSSyncTable**.

In **QSTodoService.m** (Objective-C) o **ToDoTableViewController.swift** (Swift) si noti che il tipo del membro **syncTable** è **MSSyncTable**. La sincronizzazione offline usa questa interfaccia della tabella di sincronizzazione al posto di **MSTable**. Quando si usa una tabella di sincronizzazione, tutte le operazioni vengono inviate all'archivio locale e vengono sincronizzate con il back-end remoto solo mediante operazioni push e pull esplicite.

 Per ottenere un riferimento a una tabella di sincronizzazione, usare il metodo **syncTableWithName** su `MSClient`. Per rimuovere la funzionalità di sincronizzazione offline, usare invece **tableWithName**.

Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Di seguito è riportato il codice pertinente.

* **Objective-C**: Nel metodo **QSTodoService.init**:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**: Nel metodo **ToDoTableViewController.viewDidLoad**:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Questo metodo crea un archivio locale usando l'interfaccia `MSCoreDataStore`, disponibile in Mobile Apps SDK. È anche possibile fornire un archivio locale differente, implementando il protocollo `MSSyncContextDataSource`. Il primo parametro di **MSSyncContext** viene usato per specificare un gestore di conflitti. Poiché è stato passato `nil`, si otterrà il gestore di conflitti predefinito, che non consente l'esecuzione di operazioni in caso di conflitto.

A questo punto, si esegue l'operazione effettiva di sincronizzazione e si ottengono i dati dal back-end remoto.

* **Objective-C**: `syncData` effettua innanzitutto il push delle nuove modifiche e quindi chiama il metodo **pullData** per ottenere i dati dal back-end remoto. A sua volta, il metodo **pullData** ottiene nuovi dati che corrispondono a una query:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
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

              // We will discard our changes and keep the server's copy for simplicity
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

Nella versione Objective-C, in `syncData`, viene innanzitutto chiamato il metodo **pushWithCompletion** nel contesto di sincronizzazione. Questo metodo fa parte di `MSSyncContext` (e non della tabella di sincronizzazione) perché effettua il push delle modifiche in tutte le tabelle. Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) vengono inviati al server. Viene quindi chiamato l'helper **pullData**, che chiama **MSSyncTable.pullWithQuery** per recuperare i dati remoti e memorizzarli nel database locale.

Nella versione Swift, poiché l'operazione push non è strettamente necessaria, non vi è alcuna chiamata a **pushWithCompletion**. Se nel contesto di sincronizzazione per la tabella che esegue un'operazione push sono presenti modifiche in sospeso, pull effettua sempre prima un'operazione push. Tuttavia, se sono presenti più tabelle di sincronizzazione, è preferibile chiamare in modo esplicito il push per garantire la coerenza tra le tabelle correlate.

Sia nella versione Objective-C che nella versione Swift, è possibile usare il metodo **pullWithQuery** per specificare una query per filtrare i record da recuperare. In questo esempio, la query recupera tutti i record nella tabella `TodoItem` remota.

Il secondo parametro di **pullWithQuery** è un ID di query che viene usato per la *sincronizzazione incrementale*. La sincronizzazione incrementale recupera solo i record che sono stati modificati dopo l'ultima sincronizzazione, usando il timestamp `UpdatedAt` del record denominato `updatedAt` nell'archivio locale. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `nil` come ID di query. Questo approccio può potenzialmente non essere efficiente, perché recupera tutti i record ad ogni operazione pull.

L'app Objective-C esegue la sincronizzazione quando si modificano o si aggiungono dati, quando un utente esegue l'aggiornamento e all'avvio.

L'app Swift esegue la sincronizzazione quando l'utente esegue l'aggiornamento e all'avvio.

Poiché l'app esegue la sincronizzazione ogni volta che i dati vengono modificati (Objective-C) oppure a ogni avvio dell'applicazione (Objective-C e Swift), l'app presuppone che l'utente sia online. In un'altra sezione, l'app verrà aggiornata in modo che gli utenti possano apportare modifiche anche quando sono offline.

## <a name="review-core-data"></a>Esaminare il modello di Core Data
Quando si usa l'archivio offline Core Data, è necessario definire particolari tabelle e campi all'interno del modello di dati. L'app di esempio include già un modello di dati nel formato corretto. Questa sezione illustra le tabelle e il relativo uso.

Aprire **QSDataModel.xcdatamodeld**. Qui sono definite quattro tabelle, tre usate dall'SDK e una per gli elementi attività:
  * MS_TableOperations: tiene traccia degli elementi da sincronizzare con il server.
  * MS_TableOperationErrors: tiene traccia di eventuali errori che si verificano durante la sincronizzazione offline.
  * MS_TableConfig: tiene traccia dell'ora dell'ultimo aggiornamento dell'ultima operazione di sincronizzazione per tutte le operazioni pull.
  * TodoItem: archivia gli elementi attività. Le colonne di sistema **createdAt**, **updatedAt** e **version** sono proprietà di sistema facoltative.

> [!NOTE]
> Mobile Apps SDK si riserva i nomi di colonna che iniziano con "**``**". Usare questo prefisso solo per le colonne di sistema. In caso contrario, quando si usa il back-end remoto, i nomi di colonna vengono modificati.
>
>

Quando si usa la funzionalità di sincronizzazione offline, definire le tre tabelle di sistema e la tabella dati.

### <a name="system-tables"></a>Tabelle di sistema

**MS_TableOperations**  

![Attributi della tabella MS_TableOperations][defining-core-data-tableoperations-entity]

| Attributo | Tipo |
| --- | --- |
| id | Valore integer 64 |
| itemId | String |
| properties | Dati binari |
| tabella | String |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![Attributi della tabella MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Attributo | Tipo |
| --- | --- |
| id |String |
| operationId |Valore integer 64 |
| properties |Dati binari |
| tableKind |Integer 16 |

 **MS_TableConfig**

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
| id | Stringa, contrassegnata come obbligatoria |chiave primaria nell'archivio remoto |
| complete | Boolean | campo elemento ToDo |
| text |String |campo elemento ToDo |
| createdAt | Data | (facoltativo) viene mappato alla proprietà di sistema **createdAt** |
| updatedAt | Data | (facoltativo) viene mappato alla proprietà di sistema **updatedAt** |
| version | string | (facoltativo) viene usato per il rilevamento dei conflitti, viene mappato a version |

## <a name="setup-sync"></a>Modificare il comportamento di sincronizzazione dell'app
In questa sezione si modifica l'app in modo che non esegua la sincronizzazione all'avvio o quando si inseriscono e si aggiornano elementi, bensì solo quando si seleziona il pulsante di aggiornamento.

**Objective-C**:

1. In **QSTodoListViewController.m** modificare il metodo **viewDidLoad** per rimuovere la chiamata a `[self refresh]` alla fine del metodo. A questo punto i dati non vengono sincronizzati con il server all'avvio dell'app. Sono invece sincronizzati con il contenuto dell'archivio locale.
2. In **QSTodoService.m** modificare la definizione di `addItem` in modo che non esegua la sincronizzazione dopo l'inserimento dell'elemento. Rimuovere il blocco `self syncData` e sostituirlo con quanto segue:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modificare la definizione di `completeItem` come indicato in precedenza. Rimuovere il blocco per `self syncData` e sostituirlo con il codice seguente:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

In `viewDidLoad` in **ToDoTableViewController.swift** impostare un commento per queste due righe per interrompere la sincronizzazione all'avvio dell'app. Al momento della stesura di questo articolo, l'app Swift Todo non aggiorna il servizio quando un utente aggiunge o completa un elemento. Aggiorna il servizio solo all'avvio.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Test dell'app
In questa sezione, ci si collega a un URL non valido per simulare uno scenario offline. Quando si aggiungono elementi di dati, questi vengono conservati nell'archivio Core Data locale, ma non vengono sincronizzati con il back-end dell'app per dispositivi mobili.

1. Modificare l'URL dell'app per dispositivi mobili in **QSTodoService.m** con un URL non valido ed eseguire di nuovo l'app:

   **Objective-C**: In QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**: In ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Aggiungere alcuni elementi attività. Uscire dal simulatore (o forzare la chiusura dell'app) e riavviare. Verificare che le modifiche siano state conservate.

3. Visualizzare il contenuto della tabella **TodoItem** remota:
   * Per un back-end Node.js, passare al [portale di Azure](https://portal.azure.com/) e nel back-end dell'app per dispositivi mobili fare clic su **Tabelle semplici** > **TodoItem**.  
   * Per il back-end .NET, usare uno strumento SQL, quale ad esempio SQL Server Management Studio, oppure un client REST, quale ad esempio Fiddler o Postman.  

4. Verificare che i nuovi elementi *non* siano stati sincronizzati con il server.

5. Ripristinare l'URL corretto in **QSTodoService.m** ed eseguire di nuovo l'applicazione.

6. Eseguire il movimento di aggiornamento spostando verso il basso l'elenco di elementi.  
Verrà visualizzato un indicatore di avanzamento.

7. Visualizzare nuovamente i dati di **TodoItem**. Gli elementi attività nuovi e modificati dovrebbero essere a questo punto visualizzati.

## <a name="summary"></a>Riepilogo
Per supportare la funzionalità di sincronizzazione offline è stata usata l'interfaccia `MSSyncTable` ed è stato inizializzato `MSClient.syncContext` con un archivio locale. In questo caso l'archivio locale era un database basato su Core Data.

Quando si usa un archivio locale Core Data, è necessario definire varie tabelle con le [proprietà di sistema corrette](#review-core-data).

Le normali operazioni CRUD (create, read, update, delete) per le app per dispositivi mobili funzionano come se l'app fosse connessa alla rete, ma tutte le operazioni si verificano nell'archivio locale.

Quando l'archivio locale è stato sincronizzato con il server, è stato usato il metodo **MSSyncTable.pullWithQuery**.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Sincronizzazione di dati offline nelle app per dispositivi mobili]
* [Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure] \(Il video è relativo a Servizi mobili, ma la sincronizzazione offline delle app per dispositivi mobili funziona in modo analogo.\)

<!-- URLs. -->


[Creare un'app iOS]: app-service-mobile-ios-get-started.md
[Sincronizzazione di dati offline nelle app per dispositivi mobili]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

