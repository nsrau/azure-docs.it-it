<properties
	pageTitle="Come usare la libreria client iOS per Servizi mobili di Azure"
	description="Come usare la libreria client iOS per Servizi mobili"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# Come usare la libreria client iOS per Servizi mobili di Azure

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Questa guida descrive come eseguire scenari comuni usando l'[SDK per iOS ] di Servizi mobili di Azure. Se non si ha familiarità con i Servizi mobili, completare innanzitutto l'esercitazione di [Guida introduttiva per Servizi mobili] per configurare l'account, creare una tabella e creare un servizio mobile.

> [AZURE.NOTE] In questa guida viene usata la versione più recente dell'[SDK per iOS di Servizi mobili](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Se il progetto usa una versione precedente dell'SDK, aggiornare innanzitutto il framework in Xcode.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Installazione e prerequisiti

In questa guida si presuppone che siano stati creati un servizio mobile e una tabella. Per altre informazioni, vedere [Creare una tabella] o riutilizzare la tabella `TodoItem` creata in [Guida introduttiva per Servizi mobili]. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni. In questa guida si presuppone anche che Xcode faccia riferimento a `WindowsAzureMobileServices.framework` e importi `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Procedura: Creare il client di Servizi mobili

Per accedere a un servizio mobile di Azure nel progetto, creare un oggetto client `MSClient`. Sostituire `AppUrl` e `AppKey` rispettivamente con l'URL del servizio mobile e con i valori dashboard della chiave dell'applicazione.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>Procedura: Creare un riferimento alla tabella

Per accedere ai dati per il servizio mobile di Azure o per aggiornarli, creare un riferimento alla tabella. Sostituire `TodoItem` con il nome della tabella.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>Procedura: Eseguire query sui dati

Per creare una query di database, eseguire una query sull'oggetto `MSTable`. La query seguente ottiene tutti gli elementi in `TodoItem` e registra il testo di ciascun elemento.

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>Procedura: Filtrare i dati restituiti

Per filtrare i risultati sono disponibili numerose opzioni.

Per filtrare tramite un predicato, usare `NSPredicate` e `readWithPredicate`. I filtri seguenti hanno restituito i dati per trovare solo gli elementi Todo incompleti.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>Procedura: Usare MSQuery

Per eseguire una query complessa che includa l'ordinamento e il paging, creare un oggetto `MSQuery`, direttamente o tramite un predicato:

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` consente di controllare diversi comportamenti di query, incluso quello riportato di seguito. Eseguire una query `MSQuery` chiamando `readWithCompletion`, come illustrato nell'esempio seguente.
* Specificare l'ordine dei risultati
* Limitare i campi da restituire
* Limitare il numero di record da restituire
* Specificare il conteggio totale nella risposta
* Specificare i parametri della stringa di query personalizzata nella richiesta
* Applicare funzioni aggiuntive


## <a name="sorting"></a>Procedura: Ordinare i dati con MSQuery

Per ordinare i risultati, verrà ora esaminato un esempio. Per ordinare i dati in ordine crescente in base al campo `text` e quindi in ordine decrescente in base al campo `completion`, richiamare `MSQuery` come segue:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

## <a name="paging"></a>Procedura: Restituire i dati in pagine con MSQuery

Servizi mobili limita la quantità di record restituiti in una singola risposta. Per controllare il numero di record presentati agli utenti è necessario implementare un sistema di paging. Il paging viene eseguito tramite le tre proprietà dell'oggetto **MSQuery** seguenti:

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

Nell'esempio seguente una funzione semplice richiede 5 record al server e quindi li aggiunge alla raccolta locale dei record precedentemente caricati:

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

```

## <a name="selecting"></a><a name="parameters"></a>Procedura: Limitare i campi ed espandere i parametri di query di tipo stringa con MSQuery

Per limitare i campi da restituire in una query, specificare i nomi dei campi nella proprietà **selectFields**. In questo modo vengono restituiti solo i campi text e completed:

```
	query.selectFields = @[@"text", @"completed"];
```

Per includere parametri di query di tipo stringa aggiuntivi nella richiesta server, poiché ad esempio sono usati da uno script sul lato server personalizzato, è possibile popolare `query.parameters` come segue:

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>Procedura: Inserire dati

Per inserire una nuova riga di tabella, creare un nuovo elemento `NSDictionary` e richiamare `table insert`. Servizi mobili genera automaticamente nuove colonne in base a `NSDictionary` se [Schema dinamico] non è disabilitato.

Se non viene specificato il valore `id`, il back-end genera automaticamente un nuovo ID univoco. Specificare il proprio `id` per usare indirizzi e-mail, nomi utente o valori personalizzati come ID. La specifica del proprio ID può semplificare l'esecuzione di join e la logica dei database aziendali.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>Procedura: Modificare dati

Per aggiornare una riga esistente, modificare un elemento e chiamare `update`:

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

In alternativa, fornire l'ID di riga e il campo aggiornato:

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Per le operazioni di aggiornamento è necessario che sia impostato almeno l'attributo `id`.

##<a name="deleting"></a>Procedura: Eliminare dati

Per eliminare un elemento, richiamare `delete` con l'elemento:

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

In alternativa, eliminarlo specificando un ID di riga:

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Per le operazioni di eliminazione, è necessario che sia impostato almeno l'attributo `id`.

##<a name="#custom-api"></a>Procedura: Chiamare un'API personalizzata

Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON. Per un esempio di come creare un'API personalizzata nel servizio mobile, vedere [Procedura: definire un endpoint API personalizzato](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]


##<a name="authentication"></a>Procedura: Autenticare gli utenti

Servizi mobili di Azure supporta vari provider di identità. Per un'esercitazione di base, vedere l'articolo relativo all'[autenticazione].

Servizi mobili supporta due flussi di lavoro di autenticazione:

- **Procedura di accesso gestita dal server**: Servizi mobili gestisce il processo di accesso per conto dell'app. Viene visualizzata una pagina di accesso specifica del provider e viene eseguita l'autenticazione con il provider selezionato.

- **Procedura di accesso gestita dal client**: l'_app_ richiede un token al provider di identità e lo presenta a Servizi mobili per l'autenticazione.

Quando l'autenticazione ha esito positivo, si ottiene un oggetto utente con un valore dell'ID utente e il token di autenticazione. Per usare questo ID utente per autorizzare gli utenti, vedere [Autorizzazione sul lato servizio degli utenti in Servizi mobili]. Per limitare l'accesso alle tabelle ai soli utenti autenticati, vedere [Autorizzazioni].

### Accesso gestito dal server

Per aggiungere un accesso gestito dal server al progetto dell'[introduzione per Servizi mobili], seguire la procedura riportata in seguito. È possibile usare un codice simile per altri progetti. Per altre informazioni e per vedere un esempio end-to-end in azione, vedere l'articolo relativo all'[autenticazione].

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### Accesso gestito dal client (Single Sign-On)

È possibile eseguire il processo di accesso all'esterno del client di Servizi mobili per abilitare l'accesso Single Sign-On oppure se l'applicazione contatta direttamente il provider di identità. In questi casi è possibile accedere a Servizi mobili specificando un token ottenuto in modo indipendente da un provider di identità supportato.

Nell'esempio seguente viene utilizzato [Live Connect SDK] per abilitare l'accesso Single Sign-On per le app per iOS. Questo esempio presuppone che si disponga di un'istanza di **LiveConnectClient** denominata `liveClient` nel controller e che l'utente abbia eseguito l'accesso.

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>Procedura: Memorizzare nella cache i token di autenticazione

Per memorizzare nella cache i token nel progetto dell'[introduzione per Servizi mobili], seguire la procedura riportata in seguito. È possibile applicare una procedura simile a qualsiasi progetto.[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="errors"></a>Procedura: Gestire gli errori

Quando viene effettuata una chiamata a un servizio mobile, il blocco di completamento contiene un parametro `NSError *error`. Quando si verifica un errore, il parametro sarà diverso da Nil. In questo caso, è necessario verificare il parametro nel codice e gestire l'errore nel modo appropriato.

Il file [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) definisce le costanti `MSErrorResponseKey`, `MSErrorRequestKey` e `MSErrorServerItemKey` per ottenere più dati relativi all'errore. Il file definisce anche le costanti per ogni codice di errore. Per un esempio sull'uso di queste costanti, vedere [Gestione dei problemi di conflitto] per l'uso di `MSErrorServerItemKey` e `MSErrorPreconditionFailed`.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Guida introduttiva per Servizi mobili]: mobile-services-ios-get-started.md
[introduzione per Servizi mobili]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[autenticazione]: /develop/mobile/tutorials/get-started-with-users-ios
[SDK per iOS ]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Autorizzazioni]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Autorizzazione sul lato servizio degli utenti in Servizi mobili]: mobile-services-javascript-backend-service-side-authorization.md
[Schema dinamico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Creare una tabella]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Gestione dei problemi di conflitto]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0316_2016-->