<properties 
	pageTitle="Come usare la libreria client iOS - Servizi mobili di Azure" 
	description="Informazioni su come usare la libreria client iOS per Servizi mobili di Azure." 
	services="" 
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
	ms.date="10/10/2014" 
	ms.author="krisragh"/>




# Come usare la libreria client iOS per Servizi mobili
<div class="dev-center-tutorial-selector sublanding">
  <a href="/it-it/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/it-it/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/it-it/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/it-it/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/it-it/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

In questa guida viene illustrato come eseguire scenari comuni usando un client iOS per Servizi mobili di Azure. Gli esempi sono scritti in objective-C ed è necessario avere installato [Mobile Services SDK].  Per completare questa esercitazione, è inoltre necessario disporre di [iOS SDK]. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti e la gestione di errori. Se non si ha familiarità con Servizi mobili, provare a completare prima il progetto di [guida introduttiva per Servizi mobili][Introduzione a Servizi mobili]. L'esercitazione relativa alla guida introduttiva è utile per configurare il proprio account e creare il primo servizio mobile.

## Sommario

- [Informazioni su Servizi mobili][]
- [Concetti][]
- [Installazione e prerequisiti][]
- [Procedura: Creare il client di Servizi mobili][]
- [Procedura: Creare un riferimento alla tabella][]
- [Procedura: Eseguire query sui dati da un servizio mobile][]
	- [Filtrare i dati restituiti]
    - [Usare l'oggetto MSQuery][Procedura: usare MSQuery]
	- [Selezionare colonne specifiche]
- [Procedura: Inserire dati in un servizio mobile]
- [Procedura: Modificare dati in un servizio mobile]
- [Procedura: Associare dati all'interfaccia utente]
- [Procedura: Autenticare gli utenti]
- [Procedura: Gestire gli errori]

<!--- [Procedura: Progettare unit test]
- [Procedura: Personalizzare il client]
	- [Personalizzare le intestazioni di richieste]
	- [Personalizzare la serializzazione dei tipi di dati]
- [Passaggi successivi][]-->

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Installazione e prerequisiti

In questa guida si presuppone che siano stati creati un servizio mobile e una tabella.  Per altre informazioni, vedere [Creare una tabella] o riusare la tabella `ToDoItem` creata nell'esercitazione [Introduzione a Servizi mobili]. Negli esempi illustrati in questo argomento viene usata una tabella denominata `ToDoItem` e costituita dalle colonne seguenti:

+ `id`
+ `text`
+ `complete`
+ `duration`


Se è la prima volta che si crea l'applicazione per iOS, assicurarsi di aggiungere `WindowsAzureMobileServices.framework` all'impostazione [**Link Binary With Libraries**](https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html) dell'applicazione. Durante questo passaggio, fare clic su "Add Other...", passare al percorso del pacchetto Windows Azure Mobile Services SDK scaricato e selezionarlo.

È inoltre necessario aggiungere il riferimento seguente nei file appropriati o nel file con estensione pch dell'applicazione.

	#import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

<h2><a name="create-client"></a>Procedura: Creare il client di Servizi mobili</h2>

Il codice seguente crea l'oggetto client del servizio mobile usato per accedere al servizio mobile.

	MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

Nel codice precedente sostituire `MobileServiceUrl` e `AppKey` con l'URL e la chiave applicazione del servizio mobile. Per specificare queste impostazioni per il servizio mobile, selezionare il servizio mobile nel portale di gestione di Azure, quindi fare clic su **Dashboard**.

È inoltre possibile creare il client da un oggetto **NSURL** che costituisce l'URL del servizio, come illustrato di seguito:

	MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];

<h2><a name="table-reference"></a>Procedura: Creare un riferimento alla tabella</h2>

Prima di poter accedere ai dati dal servizio mobile, è necessario recuperare un riferimento alla tabella dalla quale si desidera eseguire query, aggiornare o eliminare elementi. Nell'esempio seguente, `ToDoItem` è il nome della tabella:

	MSTable *table = [client tableWithName:@"ToDoItem"];


<h2><a name="querying"></a>Procedura: Eseguire query sui dati da un servizio mobile</h2>

Quando è disponibile un oggetto MSTable, è possibile creare la query.  La query semplice illustrata di seguito consente di recuperare tutti gli elementi nella tabella ToDoItem.

	[table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

Si noti che in questo caso il testo dell'attività viene semplicemente scritto nel log.

Nel callback vengono forniti i parametri seguenti:

+ _items_: matrice **NSArray** dei record corrispondenti alla query.
+ _totalCount_: numero totale di elementi in tutte le pagine della query, non solo di quelli restituiti nella pagina corrente. Questo valore è impostato su -1 a meno che nella richiesta non venga richiesto in modo esplicito il numero totale. Per altre informazioni, vedere [Restituzione dei dati in pagine].
+ _error_: eventuali errori che si sono verificati. In caso contrario `nil`.

### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

Per filtrare i risultati sono disponibili varie opzioni.

Nella maggior parte dei casi, per filtrare i risultati verrà usato un predicato NSPredicate.

	[table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

Il predicato seguente restituisce solo gli elementi incompleti nella tabella ToDoItem:

	NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	[table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

È possibile recuperare un singolo record in base al relativo ID.

	[table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
		}
	}];

Si noti che in questo caso i parametri di callback sono leggermente diversi.  Anziché recuperare una matrice di risultati e un numero facoltativo, viene restituito solo un record.

### <a name="query-object"></a>Uso dell'oggetto MSQuery

Usare l'oggetto **MSQuery** quando occorre una query più complessa rispetto al semplice filtro delle righe, ad esempio per modificare l'ordinamento dei risultati o limitare il numero di record di dati restituiti. Nei due esempi riportati di seguito viene illustrato come creare un'istanza dell'oggetto MSQuery:

    MSQuery *query = [table query];

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

L'oggetto MSQuery consente di controllare i comportamenti della query seguenti:

* Specificare l'ordine in cui vengono restituiti i risultati.
* Limitare i campi restituiti.
* Limitare il numero di record restituiti.
* Specificare se includere o meno il conteggio totale nella risposta.
* Specificare i parametri della stringa di query personalizzati nella richiesta.

È inoltre possibile definire ulteriormente una query applicando una o più funzioni. Una volta definita, la query viene eseguita chiamando la funzione **readWithCompletion**.

#### <a name="sorting"></a>Ordinamento dei dati restituiti

Le funzioni seguenti vengono usate per specificare i campi per l'ordinamento:

	-(void) orderByAscending:(NSString *)field
	-(void) orderByDescending:(NSString *)field

Questa query consente di ordinare i risultati prima in base alla durata e quindi in base al completamento o meno dell'attività:

	[query orderByAscending:@"duration"];
	[query orderByAscending:@"complete"];
	[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		//code to parse results here
	}];

#### <a name="paging"></a>Restituzione dei dati in pagine

Servizi mobili limita la quantità di record restituiti in una singola risposta. Per controllare il numero di record presentati agli utenti è necessario implementare un sistema di paging.  Il paging viene eseguito tramite le tre proprietà seguenti dell'oggetto **MSQuery**:

+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`


Nell'esempio seguente una funzione semplice richiede 20 record al server e quindi li aggiunge alla raccolta locale dei record precedentemente caricati:

	- (bool) loadResults() {
		MSQuery *query = [table query];

		query.includeTotalCount = YES;
		query.fetchLimit = 20;
		query.fetchOffset = self.loadedIte
	ms.count;

		[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
			if(!error) {
				// Add the items to our local copy
				[self.loadedItems addObjectsFromArray:items];

				// Set a flag to keep track if there are any additional records we need to load
				self.moreResults = (self.loadedIte
	ms.count < totalCount);
			}
		}];
	}

#### <a name="selecting"></a>Limitazione dei campi restituiti

Per limitare i campi restituiti dalla query, è sufficiente specificare i nomi dei campi desiderati nella proprietà **selectFields**. Nell'esempio seguente vengono restituiti solo i campi text e completed:

	query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>Specifica di parametri QueryString aggiuntivi

La libreria client consente di includere parametri QueryString aggiuntivi nella richiesta al server. Questi parametri potrebbero essere richiesti dagli script del lato server. Nell'esempio di codice seguente vengono aggiunti due parametri QueryString alla richiesta:

	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};

Questi parametri vengono aggiunti all'URI della come `myKey1=value1&myKey2=value2`.
Per altre informazioni, vedere [Come accedere ai parametri personalizzati].

<h2><a name="inserting"></a>Procedura: Inserire dati in un servizio mobile</h2>

Per inserire una nuova riga nella tabella, è necessario creare un nuovo [oggetto NSDictionary] e passarlo alla funzione insert. Il codice seguente consente di inserire un nuovo elemento Todo nella tabella:

	NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Servizi mobili supporta valori di stringa univoci personalizzati per l'ID della tabella. In tal modo alle applicazioni è consentito usare valori personalizzati come indirizzi di posta elettronica o nomi utente per la colonna ID di una tabella di Servizi mobili. Se ad esempio si vuole identificare ogni record con un indirizzo di posta elettronica, è possibile usare l'oggetto JSON seguente.

	NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Se al momento dell'inserimento di nuovi record in una tabella non viene specificato un valore ID di stringa, Servizi mobili genera un valore univoco per l'ID.

Il supporto di ID di stringa comporta i seguenti vantaggi per gli sviluppatori

+ È possibile generare ID senza generare un round trip del database.
+ L'unione di record da tabelle o database diversi risulta semplificata.
+ L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Per impostare i valori di ID, è inoltre possibile usare script del server. L'esempio di script seguente genera un GUID personalizzato e lo assegna all'ID di un nuovo record. Si tratta di un valore di ID simile a quello che verrebbe generato da Servizi mobili se non venisse passato un valore per l'ID di un record.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Il valore di ID specificato da un'applicazione verrà archiviato da Servizi mobili così com'è. Sono inclusi spazi vuoti iniziali o finali. Lo spazio vuoto non verrà rimosso dal valore.

Il valore per `id` deve essere univoco e non deve includere caratteri appartenenti ai set seguenti:

+ Caratteri di controllo: [0x0000-0x001F] e [0x007F-0x009F]. Per altre informazioni, vedere la pagina relativa ai [codici di controllo ASCII C0 e C1].
+  Caratteri stampabili: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Gli ID "." e ".."

In alternativa è possibile usare ID di tipo integer per le tabelle. Per usare un ID integer, è necessario creare la tabella con il comando `mobile table create` e l'opzione `--integerId`. Questo comando viene usato con l'interfaccia della riga di comando (CLI) per Azure. Per altre informazioni sull'uso dell'interfaccia della riga di comando, vedere [Comandi per la gestione delle tabelle di Servizi mobili].

Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sui campi dell'oggetto nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico].

<h2><a name="modifying"></a>Procedura: Modificare dati in un servizio mobile</h2>

È possibile aggiornare un oggetto esistente modificando un elemento restituito da una query precedente e quindi chiamando la funzione **update**.

	NSMutableDictionary *item = [self.results.item objectAtIndex:0];
	[item setObject:@YES forKey:@"complete"];
	[table update:item completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];

Quando si effettuano gli aggiornamenti, è sufficiente specificare il campo da aggiornare, unitamente all'ID della riga, come illustrato nell'esempio seguente:

	[table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @YES} completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];


Per eliminare un elemento dalla tabella, è sufficiente passarlo al metodo Delete, come illustrato di seguito:

	[table delete:item completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

È inoltre possibile eliminare un record selezionando direttamente il relativo ID, come illustrato nell'esempio seguente:

	[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

Si noti che per le operazioni di aggiornamento ed eliminazione, è necessario che sia impostato almeno l'attributo `id`.

<h2><a name="authentication"></a>Procedura: Autenticare gli utenti</h2>

In Servizi mobili è possibile usare i provider di identità seguenti per autenticare gli utenti:

- Facebook
- Google
- Account Microsoft
- Twitter
- Azure Active Directory

Per altre informazioni sulla configurazione di un provider di identità, vedere [Introduzione all'autenticazione].

Servizi mobili supporta i due flussi di lavoro di autenticazione seguenti:

- In una procedura di accesso gestita dal server, Servizi mobili gestisce il processo di accesso per conto dell'app. Una pagina di accesso specifica del provider viene visualizzata dalla libreria client e Servizi mobili procede all'autenticazione con il provider selezionato.

- In una procedura di accesso gestita dal client, l'app deve richiedere un token al provider di identità e quindi presentare il token a Servizi mobili per l'autenticazione.

Quando l'autenticazione ha esito positivo, viene restituito un oggetto utente che contiene il valore ID utente assegnato e il token di autenticazione. È possibile usare questo ID utente in script del server per convalidare o modificare le richieste. Per altre informazioni, vedere [Usare gli script per autorizzare gli utenti]. Il token stesso può essere memorizzato nella cache in modo sicuro per gli accessi successivi.

È inoltre possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. Per altre informazioni, vedere [Autorizzazioni].

### Accesso gestito dal server

Nell'esempio seguente viene illustrato come eseguire l'accesso mediante un account Microsoft. Il codice viene chiamato nel metodo ViewDidLoad del controller o attivato manualmente da un'istanza della classe UIButton. Verrà quindi visualizzata un'interfaccia utente standard per l'accesso al provider di identità.

	[client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
		completion:^(MSUser *user, NSError *error) {
			NSString *msg;
			if(error) {
				msg = [@"An error occured: " stringByAppendingString:error.description];
			} else {
				msg = [@"You are logged in as " stringByAppendingString:user.userId];
			}

			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
								  message:msg
								  delegate:nil
								  cancelButtonTitle:@"OK"
								  otherButtonTitles: nil];
			[alert show];
	}];

Nota: se si usa un provider di identità diverso dall'account Microsoft, sostituire il valore passato al metodo login riportato in precedenza con uno dei seguenti: `facebook`, `twitter`, `google` o `windowsazureactivedirectory`.

È inoltre possibile ottenere un riferimento a MSLoginController e visualizzarlo tramite:

	-(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### Accesso gestito dal client (Single Sign-On)

In alcuni casi, il processo di accesso viene eseguito all'esterno del client di Servizi mobili, ad esempio per abilitare una funzionalità Single Sign-On o se l'app deve contattare direttamente il provider di identità per ottenere informazioni sull'utente. In questi casi, è possibile eseguire l'accesso a Servizi mobili fornendo un token ottenuto in modo indipendente da un provider di identità supportato.

Nell'esempio seguente viene usato [Live Connect SDK] per abilitare l'accesso Single Sign-On per le app per iOS.

	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
			self.loggedInUser = user;
			NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
				message:msg
				delegate:nil
				cancelButtonTitle:@"OK"
				otherButtonTitles: nil];
			[alert show];
	}];

Questo codice presuppone che sia già stata creata in precedenza un'istanza **LiveConnectClient** denominata `liveClient` nel controller e che l'utente abbia eseguito l'accesso.

###<a name="caching-tokens"></a>Procedura: Memorizzare nella cache i token di autenticazione

Per evitare agli utenti di doversi autenticare ogni volta che eseguono l'applicazione, è possibile memorizzare nella cache l'identità dell'utente corrente dopo l'accesso. Sarà quindi possibile usare queste informazioni per creare direttamente l'utente e ignorare il processo di accesso.  A questo scopo è necessario archiviare l'ID utente e il token di autenticazione in locale. Nell'esempio seguente il token è memorizzato nella cache in modo sicuro nel sistema [KeyChain]:

	- (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
	{
		NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
		[query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
		[query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];

		if(isSearch) {
			// Use the proper search constants, return only the attributes of the first match.
			[query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
		}

		return query;
	}

	- (IBAction)loginUser:(id)sender {
		NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
		CFDictionaryRef cfresult;

		OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
		if (status == noErr) {
			NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;

			//create an MSUser object
			MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
			NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
			user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
			[self.todoService.client setCurrentUser:user];

		} else if (status == errSecItemNotFound) {
			//we need to log the user in
			[self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
				completion:^(MSUser *user, NSError *error) {
					NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
					UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
											message:msg delegate:nil
											cancelButtonTitle:@"OK"
											otherButtonTitles: nil];
					[alert show];

					//save the user id and token to the KeyChain
					NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client
															andIsSearch:NO];
					[newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
					[newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding]
                                                    forKey:(__bridge id)kSecValueData];

					OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
					if(status != errSecSuccess) {
						//handle error as needed
						NSAssert(NO, @"Error caching password.");
					}
			}];
		}

> [AZURE.NOTE] I token sono dati sensibili, pertanto è necessario archiviarli crittografati per proteggerli in caso di smarrimento o furto del dispositivo.

Quando si usa un token memorizzato nella cache, l'utente non deve eseguire di nuovo l'accesso fintanto che il token non è scaduto. Quando un utente tenta di eseguire l'accesso con un token scaduto, viene restituita una risposta con codice di errore 401 (Non autorizzato). A questo punto, l'utente deve eseguire di nuovo l'accesso per ottenere un nuovo token, che può, a sua volta, essere memorizzato di nuovo nella cache. Per evitare di scrivere codice che gestisce i token scaduti ogni volta che l'app chiama il servizio mobile, è possibile usare i filtri  che consentono di intercettare le chiamate al servizio mobile e le risposte ricevute da questo. Il codice nel filtro testa le risposte per rilevare codici di errore 401, attiva il processo di accesso qualora il token sia scaduto e quindi invia di nuovo la richiesta che ha generato l'errore 401. Per informazioni dettagliate, vedere il post di blog relativo alla [gestione dei token scaduti].

<h2><a name="errors"></a>Procedura: Gestire gli errori</h2>

Quando viene effettuata una chiamata a un servizio mobile, il blocco di completamento contiene un parametro `NSError *error`. Quando si verifica un errore, al parametro viene restituito un valore non Null. In questo caso, è necessario verificare il parametro nel codice e gestire l'errore nel modo appropriato.

Quando si verifica un errore, per ottenere maggiori informazioni è possibile includere il file MSError.h nel codice:

    #import <WindowsAzureMobileServices/MSError.h>

Questo file definisce le costanti seguenti che possono essere usate per accedere ai dati aggiuntivi da `[error userInfo]`:

+ **MSErrorResponseKey**: dati della risposta HTTP associati all'errore
* **MSErrorRequestKey**: dati della richiesta HTTP associati all'errore

Viene inoltre definita una costante per ogni codice di errore. Nel file MSError.h è possibile visualizzare una spiegazione di questi codici.

Per un esempio che illustra come eseguire la convalida e la gestione degli errori, vedere [Convalidare e modificare i dati in Servizi mobili mediante script del server]. In questo argomento la convalida lato server è implementata tramite script del server. Quando vengono inviati dati non validi, viene restituita una risposta e errore che viene quindi gestita dal client.

<!--
<h2><a name="#unit-testing"></a>Procedura: Progettare unit test</h2>

_(Optional) Questa sezione mostra come scrivere unit test quando si usa la libreria client (info da Yavor)._

<h2><a name="#customizing"></a>Procedura: Personalizzare il client</h2>

_(Optional) Questa sezione mostra come inviare comportamenti personalizzati del client._

###<a name="custom-headers"></a>Procedura: Personalizzare le intestazioni di richieste

_(Optional) Questa sezione mostra come inviare intestazioni di richieste personalizzate._

Per altre informazioni vedere il nuovo argomento sull'elaborazione delle intestazioni sul lato server.

###<a name="custom-serialization"></a>Procedura: Personalizzare la serializzazione

_(Optional) Questa sezione mostra come usare gli attributi per personalizzare il modo in cui vengono serializzati i tipi di dati._

Per altre informazioni vedere il nuovo argomento sull'elaborazione delle intestazioni sul lato server.

## <a name="next-steps"></a>Passaggi successivi
-->

<!-- Anchors. -->

[Informazioni su Servizi mobili]: #what-is
[Concetti]: #concepts
[Installazione e prerequisiti]: #Setup
[Procedura: Creare il client di Servizi mobili]: #create-client
[Procedura: Creare un riferimento alla tabella]: #table-reference
[Procedura: Eseguire query sui dati da un servizio mobile]: #querying
[Filtrare i dati restituiti]: #filtering
[Ordinare i dati restituiti]: #sorting
[Restituire i dati in pagine]: #paging
[Selezionare colonne specifiche]: #selecting
[Procedura: Associare dati all'interfaccia utente]: #binding
[Procedura: Inserire dati in un servizio mobile]: #inserting
[Procedura: Modificare dati in un servizio mobile]: #modifying
[Procedura: Autenticare gli utenti]: #authentication
[Memorizzare nella cache i token di autenticazione]: #caching-tokens
[Procedura: Caricare immagini e file di grandi dimensioni]: #blobs
[Procedura: Gestire gli errori]: #errors
[Procedura: Progettare unit test]: #unit-testing
[Procedura: Personalizzare il client]: #customizing
[Personalizzare le intestazioni di richieste]: #custom-headers
[Personalizzare la serializzazione dei tipi di dati]: #custom-serialization
[Passaggi successivi]: #next-steps
[Procedura: usare MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-ios
[Convalidare e modificare i dati in Servizi mobili mediante script del server]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Gestione dei token scaduti]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Autorizzazioni]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Usare gli script per autorizzare gli utenti]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schema dinamico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Come accedere ai parametri personalizzati]: /it-it/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Creare una tabella]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[Oggetti NSDictionary]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[Codici di controllo ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI per la gestione delle tabelle di Servizi mobili]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables



<!--HONumber=42-->
