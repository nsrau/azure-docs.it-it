<properties
	pageTitle="Abilitare la sincronizzazione offline per l'app per dispositivi mobili di Azure (iOS)"
	description="Informazioni su come usare le app per dispositivi mobili del servizio app per memorizzare nella cache e sincronizzare i dati offline in un'applicazione iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="krisragh"/>

# Abilitare la sincronizzazione offline per l'app per dispositivi mobili per iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## Panoramica

Questa esercitazione descrive la funzionalità di sincronizzazione offline delle app per dispositivi mobili di Azure per iOS. La sincronizzazione offline consente agli utenti finali di interagire con un'app, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il back-end remoto.

Se questa è la prima esperienza con le app per dispositivi mobili di Azure, è consigliabile completare prima l'esercitazione [Creare un'app iOS].

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

##<a name="review"></a>Verificare la configurazione del progetto server (facoltativo)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## <a name="review-sync"></a>Verificare il codice di sincronizzazione del client 

Il progetto client scaricato per l'esercitazione [Creare un'app iOS] contiene già il codice che supporta la sincronizzazione offline mediante un database basato sui dati principali locali. Questa sezione è un riepilogo degli elementi già inclusi nel codice dell'esercitazione. Per una panoramica concettuale della funzionalità, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

La funzionalità per la sincronizzazione di dati offline delle app per dispositivi mobili di Azure consente agli utenti finali di interagire con un database locale quando la rete non è accessibile. Per usare queste funzionalità nell'app, è possibile inizializzare il contesto di sincronizzazione di `MSClient` e fare riferimento a un archivio locale. Quindi, fare riferimento alla tabella tramite l'interfaccia di `MSSyncTable`.

1. In **QSTodoService.m** notare che il tipo del membro `syncTable` è `MSSyncTable`. La sincronizzazione offline usa questa interfaccia della tabella di sincronizzazione anziché `MSTable`. Quando si usa una tabella di sincronizzazione, tutte le operazioni vengono inviate all'archivio locale e vengono sincronizzate con il back-end remoto solo mediante operazioni push e pull esplicite.

    Per ottenere un riferimento a una tabella di sincronizzazione, usare il metodo `syncTableWithName`. Per rimuovere la funzionalità di sincronizzazione offline, usare invece `tableWithName`.

2. Prima di poter eseguire qualsiasi operazione su tabella, è necessario inizializzare l'archivio locale. Questo è il codice pertinente nel metodo `QSTodoService.init`:

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    Verrà creato un archivio locale usando l'interfaccia `MSCoreDataStore`, disponibile in Mobile App SDK. È anche possibile fornire un archivio locale diverso, implementando il protocollo `MSSyncContextDataSource`.

    Il primo parametro di `initWithDelegate` consente di specificare un gestore di conflitto. Poiché è stato passato `nil`, si otterrà il gestore di conflitti predefinito, che non consente l'esecuzione di operazioni in caso di conflitto.

	<!-- For details on how to implement a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services]. -->

3. I metodi `pullData` e `syncData` eseguono l'effettiva operazione di sincronizzazione: `syncData` inserisce innanzitutto le nuove modifiche, quindi chiama `pullData` per ottenere dati dal servizio remoto.

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    Il metodo `pullData`, a sua volta, ottiene dati che corrispondono a una query:

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

    In `syncData` come prima cosa viene chiamato `pushWithCompletion` nel contesto di sincronizzazione. Questo metodo fa parte di `MSSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle. Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server. Viene quindi chiamato l'helper `pullData`, che chiama `MSSyncTable.pullWithQuery` per recuperare i dati remoti e archiviarli nel database locale.

    Si noti che in questo esempio l'operazione push non è strettamente necessaria. Se nel contesto di sincronizzazione per la tabella che esegue un'operazione push sono presenti modifiche in sospeso, pull effettua sempre prima un'operazione push. Tuttavia, se sono presenti più tabelle di sincronizzazione, è preferibile chiamare in modo esplicito push per garantire la coerenza nelle tabelle correlate.

    Il metodo `pullWithQuery` consente di specificare una query per filtrare i record da recuperare. In questo esempio, la query recupera semplicemente tutti i record nella tabella `TodoItem` remota.

    Il secondo parametro di `pullWithQuery` è un ID di query usato per la *sincronizzazione incrementale*. La sincronizzazione incrementale recupera solo i record modificati dopo l'ultima sincronizzazione, usando il timestamp del record `UpdatedAt` (denominato `ms_updatedAt` nell'archivio locale). L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Per rifiutare esplicitamente la sincronizzazione incrementale, passare `nil` come ID di query. Si noti che questa è una scelta potenzialmente inefficiente, perché in ogni operazione pull verranno recuperati tutti i record.

	<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to purge the local store.
 -->

5. Nella classe `QSTodoService` il metodo `syncData` viene chiamato dopo le operazioni che modificano i dati, `addItem` e `completeItem`. Viene anche chiamato da `QSTodoListViewController.refresh`, in modo che l'utente ottenga i dati più recenti ogni volta che esegue il movimento di aggiornamento. L'app esegue anche una sincronizzazione all'avvio, in quanto `QSTodoListViewController.init` chiama `refresh`.

    Poiché ogni volta che vengono modificati i dati viene chiamato `syncData`, quest'app presuppone che l'utente sia online quando modifica i dati. In un'altra sezione, l'app verrà aggiornata in modo che gli utenti possano apportare modifiche anche offline.

## <a name="review-core-data"></a>Esaminare il modello di Core Data

Quando si usa l'archivio offline Core Data, è necessario definire particolari tabelle e campi all'interno del modello di dati. L'app di esempio include già un modello di dati nel formato corretto. In questa sezione vengono illustrate le tabelle e il relativo uso.

- Aprire **QSDataModel.xcdatamodeld**. Sono presenti le definizioni di quattro tabelle, tre usate dall'SDK e una per gli elementi todo:
      * MS\_TableOperations: per tenere traccia degli elementi da sincronizzare con il server
      * MS\_TableOperationErrors: per tenere traccia di eventuali errori verificatisi durante la sincronizzazione offline
      * MS\_TableConfig: per tenere traccia dell'ora dell'ultimo aggiornamento dell'ultima operazione di sincronizzazione per tutte le operazioni pull
      * TodoItem: per archiviare gli elementi todo. Le colonne di sistema **ms\_createdAt**, **ms\_updatedAt** e **ms\_version** sono proprietà di sistema facoltative.

>[AZURE.NOTE]Azure Mobile Apps SDK riserva nomi di colonna che iniziano con "**`ms_`**". Usare questo prefisso solo per le colonne di sistema. In caso contrario, quando si usa il back-end remoto i nomi di colonna verranno modificati.

- Quando si usa la funzionalità di sincronizzazione offline, è necessario definire le tabelle di sistema come illustrato di seguito.

    ### Tabelle di sistema

    **MS\_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | Attributo | Tipo |
    |----------- |   ------    |
    | id | Valore integer 64 |
    | itemId | String |
    | properties | Dati binari |
    | tabella | String |
    | tableKind | Valore integer 16 |

    <br>**MS\_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | Attributo | Tipo |
    |----------- |   ------    |
    | id | String |
    | operationId | Valore integer 64 |
    | properties | Dati binari |
    | tableKind | Valore integer 16 |

    <br>**MS\_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | Attributo | Tipo |
    |----------- |   ------    |
    | id | Valore integer 64 |
    | key | String |
    | keyType | Valore integer 64 |
    | tabella | String |
    | value | String |

    ### Tabella dati

    ![][defining-core-data-todoitem-entity]

    **TodoItem**


    | Attributo | Tipo | Nota |
    |-----------   |  ------ | -------------------------------------------------------|
    | id | Stringa, contrassegnata come obbligatoria | chiave primaria nell'archivio remoto |
    | complete | Boolean | campo elemento ToDo |
    | text | String | campo elemento ToDo |
    | ms\_createdAt | Date | (optional) maps to \_\_createdAt system property | | ms\_updatedAt | Date | (optional) maps to \_\_updatedAt system property | | ms\_version | String | (optional) used to detect conflicts, maps to \_\_version |


## <a name="setup-sync"></a>Modificare il comportamento di sincronizzazione dell'app

In questa sezione si procederà alla modifica dell'app in modo che non effettui la sincronizzazione all'avvio né quando vengono inseriti e aggiornati elementi, ma solo quando viene eseguito il movimento di aggiornamento.

1. In **QSTodoListViewController.m** modificare il metodo **viewDidLoad** per rimuovere la chiamata a `[self refresh]` alla fine del metodo. Ora all'avvio dell'app i dati non verranno sincronizzati con il server, ma verrà sincronizzato il contenuto dell'archivio locale.

2. In **QSTodoService.m** modificare la definizione di `addItem` in modo che non esegua la sincronizzazione dopo l'inserimento dell'elemento. Rimuovere il blocco `self syncData` e sostituirlo con quanto segue:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modificare la definizione di `completeItem` come sopra. Rimuovere il blocco per `self syncData` e sostituirlo con quanto segue:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>Test dell'app


In questa sezione si disattiverà il Wi-Fi nel simulatore per creare uno scenario offline. Quando si aggiungono elementi di dati, questi vengono conservati nell'archivio Core Data locale, ma non sincronizzati con il back-end mobile.

1. Disattivare il Wi-Fi nel simulatore iOS.

2. Aggiungere alcuni elementi todo o completare alcuni elementi. Uscire dal simulatore (o forzare la chiusura dell'app) e riavviare. Verificare che le modifiche siano state conservate.

3. Visualizzare il contenuto della tabella TodoItem remota:
   - Per il back-end JavaScript, accedere al portale di gestione e fare clic sulla scheda Dati per visualizzare il contenuto della tabella `TodoItem`.
   - Per il back-end .NET, visualizzare il contenuto della tabella mediante uno strumento SQL, ad esempio SQL Server Management Studio, o mediante un client REST, ad esempio Fiddler o Postman.

    Verificare che i nuovi elementi *non* siano stati sincronizzati con il server:

4. Attivare il Wi-Fi nel simulatore iOS, quindi eseguire il movimento di aggiornamento trascinando verso il basso l'elenco di elementi. Verranno visualizzati un indicatore di avanzamento e il testo "Syncing...".

5. Visualizzare nuovamente i dati di TodoItem. Dovrebbero essere visualizzati gli elementi TodoItems nuovi e modificati.

## Riepilogo

Per supportare la funzionalità di sincronizzazione offline è stata usata l'interfaccia `MSSyncTable` ed è stato inizializzato `MSClient.syncContext` in un archivio locale. In questo caso l'archivio locale era un database basato su Core Data.

Quando si usa un archivio locale Core Data, è necessario definire svariate tabelle con le [proprietà di sistema corrette](#review-core-data).

Le normali operazioni CRUD per le app per dispositivi mobili di Azure funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `MSSyncTable.pullWithQuery` e `MSClient.syncContext.pushWithCompletion`.

*  Per eseguire il push delle modifiche al server, è stata effettuata la chiamata a `pushWithCompletion`. Questo metodo fa parte di `MSSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle.

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.

* Per eseguire il pull dei dati da una tabella del server all'app, è stata effettuata la chiamata a `MSSyncTable.pullWithQuery`.

    Un'operazione pull effettua sempre un'operazione push all'inizio. Lo scopo è assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti.

    Si noti che è possibile usare `pullWithQuery` per filtrare i dati archiviati nel client, personalizzando il parametro `query`.

* Per abilitare la sincronizzazione incrementale, passare un ID di query a `pullWithQuery`. L'ID di query viene usato per archiviare il timestamp dell'ultimo aggiornamento dai risultati dell'ultima operazione di pull. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Se la query include un parametro, è necessario l'ID di query deve includere lo stesso valore di parametro.

    Se si intende rifiutare esplicitamente la sincronizzazione incrementale, passare `nil` come ID di query. In questo caso, verranno recuperati tutti i record in ogni chiamata a `pullWithQuery`, potenzialmente inefficace.

<!-- * To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to remove records from the local database, in case they have been deleted in the remote service.
 -->

## Risorse aggiuntive

* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]

* [Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure] (nota: il video è relativo ai Servizi mobili, ma il funzionamento della sincronizzazione offline è simile nelle app per dispositivi mobili di Azure)

<!-- URLs. -->


[Creare un'app iOS]: ../app-service-mobile-dotnet-backend-ios-get-started-preview.md
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: ../app-service-mobile-offline-data-sync-preview.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-todoitem-entity.png

[Cloud Cover: Sincronizzazione offline in Servizi mobili di Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
 

<!---HONumber=August15_HO8-->