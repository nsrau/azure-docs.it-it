<properties
	pageTitle="Come utilizzare iOS SDK per App Mobile di Azure"
	description="Come utilizzare iOS SDK per App Mobile di Azure"
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
	ms.date="09/28/2015"
	ms.author="krisragh"/>

# Come usare la libreria client iOS per le app mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-client-library.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questa guida descrive come eseguire scenari comuni usando le più recenti app per dispositivi mobili di Azure [SDK per iOS ](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Se si ha familiarità con le App per dispositivi mobili di Azure, completare innanzitutto [Azure Mobile App Quick Start] per creare un back-end, creare una tabella e scaricare un progetto Xcode iOS preesistente. In questa Guida, l'attenzione è posta sul lato client iOS SDK. Per ulteriori informazioni sul SDK .NET sul lato server per il back-end, vedere [Utilizzare back-end .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

##<a name="Setup"></a>Installazione e prerequisiti

In questa guida si presuppone che siano stati creati un backend e una tabella. In questa guida si presuppone che la tabella abbia lo stesso schema delle tabelle presenti in tali esercitazioni. In questa guida si presuppone inoltre che nel codice, si faccia riferimento a `WindowsAzureMobileServices.framework` e si importi `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Procedura: creare Client

Per accedere a un back-end di applicazioni per dispositivi mobili di Azure nel progetto, creare un `MSClient`. Sostituire `AppUrl` con l'URL dell'app. È possibile lasciare `gatewayURLString` e `applicationKey` vuoti. Se si configura un gateway per l'autenticazione, popolare `gatewayURLString` con l'URL del gateway.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" gatewayURLString:@"" applicationKey:@""];
```

##<a name="table-reference"></a>Procedura: Creare un riferimento alla tabella

Per l'accesso o l'aggiornamento dei dati, creare un riferimento alla tabella di back-end. Sostituire `TodoItem` con il nome della tabella.

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

`MSQuery` consente di controllare diversi comportamenti di query, incluso quello riportato di seguito. Eseguire una query `MSQuery` chiamando `readWithCompletion` su di essa, come illustrato nell'esempio successivo. *Specificare l'ordine dei risultati * Limitare i campi da restituire * Limitare il numero di record da restituire * Specificare il conteggio totale nella risposta * Specificare parametri di query di tipo stringa personalizzati nella richiesta * Applicare funzioni aggiuntive


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
[Azure Mobile App Quick Start]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schema dinamico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Gestione dei problemi di conflitto]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=Oct15_HO1-->