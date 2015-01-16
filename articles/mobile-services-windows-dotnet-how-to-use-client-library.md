
<properties urlDisplayName=".NET Client Library" pageTitle="Uso della libreria client .NET per Servizi mobili" metaKeywords="Servizi mobili di Azure, client .NET per Servizi mobili, client .NET" description="Informazioni su come usare un client .NET per Servizi mobili di Azure." metaCanonical="" services="" documentationCenter="Mobile" title="How to use a .NET client for Azure Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="glenga" />





# Come usare un client .NET per Servizi mobili di Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/it-it/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework" class="current">.NET Framework</a>
  	<a href="/it-it/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/it-it/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/it-it/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/it-it/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


In questa guida viene illustrato come eseguire scenari comuni usando un client .NET per Servizi mobili di Azure in app di Windows Store e Windows Phone. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti e la gestione di errori. Se non si ha familiarità con Servizi mobili, è consigliabile completare prima le esercitazioni relative alla guida introduttiva a Servizi mobili ([esercitazione basata sulla guida introduttiva per Windows Store]/[esercitazione basata sulla guida introduttiva per Windows Phone]) e all'introduzione ai dati in .NET ([esercitazione relativa ai dati in Windows Store]/[esercitazione relativa ai dati in Windows Phone]). Per completare l'esercitazione relativa alla guida introduttiva, che consente di configurare il proprio account e di creare il primo servizio mobile, è necessario disporre di [Mobile Services SDK].


## Sommario

- [Informazioni su Servizi mobili]
- [Concetti]
- [Procedura: Creare il client di Servizi mobili]
- [Procedura: Creare un riferimento alla tabella]
- [Procedura: Eseguire query sui dati da un servizio mobile]
	- [Filtrare i dati restituiti]
    - [Ordinare i dati restituiti]
	- [Restituire i dati in pagine]
	- [Selezionare colonne specifiche]
	- [Cercare dati in base all'ID]
- [Procedura: Inserire dati in un servizio mobile]
- [Procedura: Modificare dati in un servizio mobile]
- [Procedura: Eliminare dati in un servizio mobile]
- [Procedura: Chiamare un'API personalizzata]
- [Procedura: Usare la concorrenza ottimistica]
- [Procedura: Associare dati all'interfaccia utente in un servizio mobile]
- [Procedura: Autenticare gli utenti]
- [Procedura: Gestire gli errori]
- [Procedura: Usare dati non tipizzati]
- [Procedura: Progettare unit test]
- [Procedura: Personalizzare il client]
	- [Personalizzare le intestazioni di richieste]
	- [Personalizzare la serializzazione]
- [Passaggi successivi]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>Installazione e prerequisiti</h2>

Si presuppone che siano stati creati un servizio mobile e una tabella. Per altre informazioni, vedere [Creare una tabella](http://go.microsoft.com/fwlink/?LinkId=298592). Nel codice utilizzato in questo argomento, la tabella è denominata `TodoItem` e si presenta con le colonne seguenti: `Id`, `Text` e `Complete`.

Il tipo .NET tipizzato corrispondente sul lato client è il seguente:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Quando è abilitato lo schema dinamico, in Servizi mobili di Azure vengono generate automaticamente nuove colonne basate sull'oggetto nelle richieste di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico](http://go.microsoft.com/fwlink/?LinkId=296271).

<h2><a name="create-client"></a>Procedura: Creare il client di Servizi mobili</h2>

Il codice seguente consente di creare l'oggetto `MobileServiceClient` usato per accedere al servizio mobile.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

Nel codice riportato sopra sostituire `AppUrl` e `AppKey` con l'URL del servizio mobile e la chiave dell'applicazione, in quest'ordine. Entrambi gli elementi sono disponibili nel portale di gestione di Azure selezionando il servizio mobile e quindi facendo clic su "Dashboard".

<h2><a name="instantiating"></a>Procedura: Creare un riferimento alla tabella</h2>

 Tutto il codice che accede ai dati nella tabella di Servizi mobili o li modifica chiama funzioni sull'oggetto "MobileServiceTable". Per ottenere un riferimento alla tabella, chiamare la funzione [GetTable](http://msdn.microsoft.com/it-it/library/windowsazure/jj554275.aspx) su un'istanza dell'oggetto `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Questo è il modello tipizzato di serializzazione. Più avanti in questo argomento verrà illustrato il <a href="#untyped">modello di serializzazione non tipizzato</a> .

<h2><a name="querying"></a>Procedura: Eseguire query sui dati da un servizio mobile</h2>

Questa sezione descrive come eseguire query nel servizio mobile. Nelle sottosezioni vengono descritti aspetti diversi come ordinamento, filtri e paging.

>[WACOM.NOTE]Per impostazione predefinita, viene usata una dimensione di pagina basata sul server, per evitare la restituzione di tutte le righe. In questo modo, le richieste predefinite di set di dati di grandi dimensioni non hanno conseguenze negative sul servizio. Per restituire più di 50 righe, usare il metodo `Take` descritto in [Restituire i dati in pagine].  

### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

Nel codice seguente viene illustrato come filtrare i dati includendo una clausola `Where` in una query. Restituisce tutti gli elementi da `todoTable` per i quali la proprietà `Complete` è uguale a `false`. La funzione `Where` applica un predicato di filtro di riga alla query sulla tabella.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

È possibile visualizzare l'URI della richiesta inviata al servizio mobile usando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler]. Nell'URI della richiesta riportato di seguito si noterà inoltre che viene modificata la stringa di query stessa:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
Normalmente, questa richiesta verrebbe convertita approssimativamente nella query SQL seguente sul lato server:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

La funzione passata al metodo `Where` può avere un numero di condizioni arbitrario. Ad esempio, la riga seguente:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Viene convertita approssimativamente (per la stessa richiesta) in

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

L'istruzione `where` riportata sopra individuerà elementi con stato `Complete` impostato su false con `Text` diverso da null.

Sarebbe stato anche possibile usare più righe:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

I due metodi si equivalgono e possono essere usati in modo intercambiabile.  La prima opzione, che prevede la concatenazione di più predicati in una query, è più compatta ed è consigliata.

La clausola `where` supporta operazioni che vengono convertite nel subset OData di Servizi mobili. Sono inclusi gli operatori relazionali (==, !=, <, <=, >, >=), gli operatori aritmetici (+, -, /, *, %), l'approssimazione dei numeri (Math.Floor, Math.Ceiling), funzioni di stringa (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), proprietà relative alla data (Year, Month, Day, Hour, Minute, Second), proprietà di accesso di un oggetto ed espressioni che combinano tutti questi elementi.

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti

Nel codice seguente viene illustrato come ordinare i dati includendo una funzione `OrderBy` o `OrderByDescending` nella query. L'operazione restituisce elementi della tabella `todoTable` in ordine crescente in base al campo `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Procedura: Restituire i dati in pagine

 Per impostazione predefinita, il server restituisce solo le prime 50 righe. È possibile aumentare il numero di righe restituite mediante una chiamata al metodo [Take]. Usare `Take` insieme al metodo [Skip] per richiedere una "pagina" specifica dell'intero dataset restituito dalla query. La query seguente, se eseguita, restituisce le prime tre voci della tabella.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

La query modificata riportata di seguito ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati le cui dimensioni corrispondono a tre voci.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

È anche possibile usare il metodo [IncludeTotalCount] per assicurarsi che la query consenta di ottenere il conteggio totale di <i>tutti</i> i record che sarebbero stati restituiti ignorando qualsiasi clausola di limite/paging specificata:

	query = query.IncludeTotalCount();

Nella situazione illustrata il passaggio di valori di paging hardcoded ai metodi "Take" e "Skip" è stato semplificato. In un'app reale è possibile usare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive.

### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

È possibile specificare il set di proprietà da includere nei risultati aggiungendo alla query una clausola `Select`. Ad esempio, il codice seguente illustra come selezionare un solo campo e come selezionare e formattare più campi:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Tutte le funzioni descritte in precedenza sono additive, di conseguenza è possibile ripetere la chiamata per influire maggiormente sulla query. Un altro esempio:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Procedura: Cercare dati in base all'ID

Per cercare oggetti dal database caratterizzati da un particolare ID, è possibile usare la funzione `LookupAsync`.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

<a name="inserting"></a>Procedura: Inserire dati in un servizio mobile

<div class="dev-callout"><strong>Nota</strong> <p>Per eseguire operazioni di inserimento, ricerca, eliminazione o aggiornamento su un tipo, è necessario creare un membro denominato <strong>Id</strong>. La classe di esempio <strong>TodoItem</strong> ha infatti un membro denominato <strong>Id</strong>. Un valore ID è valido solo se è sempre presente nelle operazioni di aggiornamento ed eliminazione.</p> </div>

Il codice seguente illustra come inserire nuove righe in una tabella. Il parametro contiene i dati da inserire come oggetto .NET.

	await todoTable.InsertAsync(todoItem);

Se nell'elemento `todoItem` passato alla chiamata `todoTable.InsertAsync` non è incluso un valore ID univoco personalizzato, verrà generato un valore per l'ID dal server impostato nell'oggetto `todoItem` restituito al client.

Servizi mobili supporta valori di stringa univoci personalizzati per l'ID della tabella. In tal modo alle applicazioni è consentito usare valori personalizzati come indirizzi di posta elettronica o nomi utente per la colonna ID di una tabella di Servizi mobili. Se al momento dell'inserimento di nuovi record in una tabella non viene specificato un valore ID di stringa, Servizi mobili genera un valore univoco per l'ID.

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


Per inserire dati non tipizzati, è possibile usare Json.NET come illustrato di seguito.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Di seguito è riportato un esempio con un indirizzo di posta elettronica come ID di stringa univoco.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


<h2><a name="modifying"></a>Procedura: Modificare dati in un servizio mobile</h2>

Il codice seguente illustra come aggiornare con nuove informazioni un'istanza esistente con lo stesso ID. Il parametro contiene i dati da aggiornare come oggetto .NET.

	await todoTable.UpdateAsync(todoItem);


Per inserire dati non tipizzati, è possibile usare Json.NET come illustrato di seguito. Si noti che durante un'operazione di aggiornamento è necessario specificare un ID per consentire al servizio mobile di identificare l'istanza da aggiornare. È possibile ottenere l'ID dal risultato della chiamata "InsertAsync".

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Se si tenta di aggiornare un elemento senza specificare il valore "Id", il servizio non sarà in grado di individuare l'istanza da aggiornare e quindi Mobile Services SDK genererà un'eccezione "ArgumentException".


<h2><a name="deleting"></a>Procedura: Eliminare dati in un servizio mobile</h2>

Il codice seguente illustra come eliminare un'istanza esistente. L'istanza è identificata dal campo "Id" impostato in "todoItem".

	await todoTable.DeleteAsync(todoItem);

Per eliminare dati non tipizzati, è possibile usare Json.NET come illustrato di seguito. Si noti che per una richiesta di eliminazione è necessario specificare un ID per consentire al servizio mobile di identificare l'istanza da eliminare. Per una richiesta di eliminazione è necessario solo l'ID: altre proprietà non vengono passate al servizio e, in caso contrario, vengono ignorate. Anche il risultato di una chiamata `DeleteAsync` equivale normalmente a `null`. È possibile ottenere l'ID da passare dal risultato della chiamata `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Se si prova a eliminare un elemento prima di impostare il campo "Id", il servizio non è in grado di individuare l'istanza da eliminare e di conseguenza genera un'eccezione "MobileServiceInvalidOperationException". Analogamente, se si prova a eliminare un elemento non tipizzato prima di impostare il campo "Id", il servizio genera un'eccezione "MobileServiceInvalidOperationException".

## <a name="#custom-api"></a>Procedura: Chiamare un'API personalizzata

Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'uso di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON. Per un esempio completo, incluse informazioni per creare un'API personalizzata nel servizio mobile, vedere [Chiamare un'API personalizzata dal client].

Per chiamare un'API personalizzata, è sufficiente chiamare uno degli overload del metodo [InvokeApiAsync] sul client. Ad esempio, la riga di codice seguente invia una richiesta POST all'API **completeAll** sul servizio mobile:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Si noti che questa è una chiamata tipizzata al metodo, che richiede che il tipo restituito **MarkAllResult** sia definito. Sono supportati sia i metodi tipizzati, sia quelli non tipizzati. Questo esempio è abbastanza semplice in quanto è tipizzato, non invia payload, non ha parametri di query e non modifica le intestazioni della richiesta. Per esempi più realistici e una discussione completa sul metodo [InvokeApiAsync], vedere l'articolo relativo all'[API personalizzata negli SDK del client di Servizi mobili].


## <a name="optimisticconcurrency"></a>Procedura: Usare la concorrenza ottimistica

In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Il controllo della concorrenza ottimistica presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse. Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione.

Servizi mobili supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento, con la colonna di proprietà di sistema `__version` definita per ogni tabella creata da Servizi mobili. Ogni volta che un record viene aggiornato, Servizi mobili imposta la proprietà `__version` per quel record su un nuovo valore. Durante ogni richiesta di aggiornamento, la proprietà `__version` del record inclusa nella richiesta viene confrontata con la stessa proprietà relativa al record sul server. Se la versione passata con la richiesta non corrisponde a quella del server, la libreria client .NET di Servizi mobili genera un'eccezione `MobileServicePreconditionFailedException<T>`. Il tipo incluso nell'eccezione corrisponde al record del server e contiene la versione del record presente sul server. L'applicazione può quindi usare questa informazione per decidere se eseguire nuovamente la richiesta di aggiornamento con il valore `__version` corretto nel server per effettuare il commit delle modifiche.  

Per attivare la concorrenza ottimistica, l'applicazione definisce una colonna sulla classe di tabella per la proprietà di sistema `__version`. La definizione seguente ne è un esempio.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


Le applicazioni che usano tabelle non tipizzate attivano la concorrenza ottimistica impostando il flag `Version` per `SystemProperties` della tabella, come mostrato di seguito.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


Nel codice seguente viene illustrato come risolvere un conflitto di scrittura, qualora venga rilevato. Il valore `__version` corretto deve essere incluso nella chiamata `UpdateAsync()` per eseguire il commit di una risoluzione.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}


Per un esempio più completo dell'uso della concorrenza ottimistica per Servizi mobili, vedere l'[esercitazione relativa alla concorrenza ottimistica].


<h2><a name="binding"></a>Procedura: Associare dati all'interfaccia utente in un servizio mobile</h2>

In questa sezione viene illustrato come visualizzare gli oggetti dati restituiti usando elementi dell'interfaccia utente. Per eseguire una query sugli elementi incompleti in `todoTable` e visualizzarli in un semplice elenco, è possibile eseguire il codice di esempio seguente per associare l'origine dell'elenco a una query. Mediante `MobileServiceCollection` è possibile creare una raccolta di associazione compatibile con Servizi mobili.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Alcuni controlli di Windows Runtime supportano un'interfaccia denominata [ISupportIncrementalLoading](http://msdn.microsoft.com/it-it/library/windows/apps/Hh701916). Questa interfaccia consente ai controlli di richiedere dati aggiuntivi nello scorrimento verso il basso. Per questa interfaccia per le app di Windows Store è disponibile un supporto incorporato tramite `MobileServiceIncrementalLoadingCollection`, che gestisce automaticamente le chiamate dai controlli. Per usare `MobileServiceIncrementalLoadingCollection` nelle app di Windows Store, eseguire le operazioni seguenti:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Per usare la nuova raccolta in Windows Phone, usare i metodi di estensione `ToCollection` su `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Per caricare effettivamente i dati, chiamare `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Quando si usa la raccolta creata tramite la chiamata a `ToCollectionAsync` o `ToCollection`, si ottiene una raccolta che può essere associata ai controlli dell'interfaccia utente. Questa raccolta supporta il paging, ossia consente a un controllo di chiedere e ottenere dalla raccolta di "caricare più elementi". A quel punto, il codice utente non è coinvolto e il controllo avvierà il flusso. Poiché tuttavia la raccolta carica dati dalla rete, si suppone che talvolta il caricamento avrà esito negativo. Per gestire tali errori, è possibile eseguire l'override del metodo `OnException` in `MobileServiceIncrementalLoadingCollection` per gestire le eccezioni derivanti da chiamate a `LoadMoreItemsAsync` eseguite dai controlli.

Infine, si supponga che la tabella sia costituita da molti campi, ma si desideri visualizzarne solo alcuni nel controllo. È possibile seguire le indicazioni riportate nella sezione <a href="#selecting"Selezionare colonne specifiche"</a>  precedente per scegliere specifiche colonne da visualizzare nell'interfaccia utente.

<h2><a name="authentication"></a>Procedura: Autenticare gli utenti</h2>

Servizi mobili supporta l'autenticazione e l'autorizzazione di utenti delle app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile usare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per altre informazioni, vedere l'esercitazione "Introduzione all'autenticazione" ([Windows Store][autenticazione di Windows Store]/[Windows Phone][autenticazione di Windows Phone])

Sono supportati due flussi di autenticazione: un _flusso server_ e un _flusso client_. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, poiché si basa su SDK specifici del provider e del dispositivo.

<h3>Flusso server</h3>
Per consentire a Servizi mobili di gestire il processo di autenticazione nell'app di Windows Store o per Windows Phone, è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio mobile è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider. Per altre informazioni, vedere l'esercitazione "Introduzione all'autenticazione" ([Windows Store][autenticazione di Windows Store]/[Windows Phone][autenticazione di Windows Phone]).

Dopo aver effettuato la registrazione del provider di identità, è sufficiente chiamare il metodo [LoginAsync method] con il valore del provider [MobileServiceAuthenticationProvider]. Ad esempio, con il codice seguente viene avviato un accesso al flusso server mediante Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Se si usa un provider di identità diverso da Facebook, sostituire il valore di [MobileServiceAuthenticationProvider] con il nome del provider.

In questo caso, Servizi mobili gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione di Servizi mobili una volta eseguito correttamente l'accesso con il provider di identità. Il metodo [LoginAsync] restituisce un utente [MobileServiceUser], che fornisce sia un elemento [userId] dell'utente autenticato sia un elemento [MobileServiceAuthenticationToken] sotto forma di token Web JSON (JWT). È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza. Per altre informazioni, vedere [Memorizzazione nella cache del token di autenticazione].

<div class="dev-callout"><b>App di Windows Store</b>
<p>Quando si usa il provider di accesso con account Microsoft per autenticare gli utenti dell'app di Windows Store, è inoltre necessario registrare il pacchetto dell'app con Servizi mobili. Quando si registrano le informazioni del pacchetto dell'app di Windows Store con Servizi mobili, il client è in grado di riutilizzare le credenziali di accesso dell'account Microsoft per un ambiente Single Sign-On. In caso contrario, gli utenti che accedono tramite un account Microsoft dovranno specificare le credenziali di accesso ogni volta che viene chiamato il metodo di accesso. Per altre informazioni sulla registrazione del pacchetto dell'app di Windows Store, vedere <a href="/it-it/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Registrazione del pacchetto dell'app di Windows Store per l'autenticazione Microsoft</a>. Dopo la registrazione delle informazioni del pacchetto con Servizi mobili, per riutilizzare le credenziali è necessario chiamare il metodo <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> specificando il valore <strong>true</strong> per il parametro <em>useSingleSignOn</em>.</p>
</div>

<h3>Flusso client</h3>

L'app può inoltre contattare il provider di identità in modo indipendente e fornire il token restituito a Servizi mobili per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

Nella forma più semplice, è possibile usare il flusso client come illustrato in questo frammento di codice per Facebook o Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Se si utilizza un account Microsoft, effettuare l'accesso come segue:

	// Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
	user = await client
		.LoginWithMicrosoftAccountAsync(authentication_token_value);

Per un esempio di uso di un account Microsoft per consentire l'uso di un unico accesso, vedere l'esercitazione "Autenticare un'app con Single Sign-On ([Windows Store](/it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/it-it/develop/mobile/tutorials/single-sign-on-wp8/)).

<h3><a name="caching"></a>Memorizzazione nella cache del token di autenticazione</h3>
In alcuni casi, è possibile evitare la chiamata al metodo di accesso dopo la prima autenticazione dell'utente. Con [PasswordVault], le app di Windows Store memorizzano nella cache l'identità dell'utente corrente al primo accesso e a ogni accesso successivo viene verificata la presenza dell'identità dell'utente nella cache. Se la cache è vuota, è comunque necessario richiedere all'utente di ripetere la procedura di accesso.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


Nel caso delle app di Windows Phone, è possibile crittografare i dati e memorizzarli nella cache usando la classe [ProtectedData] e archiviare i dati sensibili in uno spazio di memorizzazione isolato.

<h2><a name="errors"></a>Procedura: Gestire gli errori</h2>

In Servizi mobili è possibile rilevare, convalidare e risolvere gli errori in diversi modi.

Ad esempio, gli script del server vengono registrati in un servizio mobile e possono essere usati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. Si consideri ad esempio la definizione e la registrazione di uno script del server per la convalida e la modifica dei dati:

	function insert(item, user, request)
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Questo script sul lato server convalida la lunghezza dei dati di stringa inviati al servizio mobile e rifiuta le stringhe troppo lunghe, in questo caso quelle più lunghe di 10 caratteri.

Ora che il servizio mobile convalida i dati e invia risposte di errore sul lato server, è possibile aggiornare l'app .NET per gestire le risposte di errore risultanti dalla convalida.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

<h2><a name="untyped"></a>Procedura: Usare dati non tipizzati</h2>

 Il client .NET è progettato per gli scenari fortemente tipizzati. In alcune situazioni una tipizzazione meno rigida può risultare più pratica, come nel caso in cui sia necessario gestire oggetti con uno schema aperto. Questo scenario viene abilitato come segue. Nelle query non verrà usato LINQ, ma il formato di trasmissione.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Si ottengono valori JSON utilizzabili come contenitore delle proprietà. Per altre informazioni su JToken e Json.NET, vedere [Json.NET](http://json.codeplex.com/).

<h2><a name="unit-testing"></a>Procedura: Progettare unit test</h2>

Il valore restituito da `MobileServiceClient.GetTable` e dalle query sono interfacce. Queste interfacce sono facilmente "imitabili" a scopo di test, quindi è possibile creare una tabella `MyMockTable: IMobileServiceTable<TodoItem>` che implementa la logica del test.

<h2><a name="customizing"></a>Procedura: Personalizzare il client</h2>

### <a name="headers"></a>Procedura: Personalizzare le intestazioni di richieste

È possibile allegare un'intestazione personalizzata a tutte le richieste in uscita oppure modificare i codici di stato delle risposte. A tale scopo, configurare un gestore DelegatingHandler nel modo seguente:

	public async Task CallClientWithHandler()
	{
		MobileServiceClient client = new MobileServiceClient(
			"AppUrl",
			"AppKey" ,
			new MyHandler()
			);
		IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
		var newItem = new TodoItem { Text = "Hello world", Complete = false };
		await table.InsertAsync(newItem);
	}

	public class MyHandler : DelegatingHandler
	{
		protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		{
			request.Headers.Add("x-my-header", "my value");
			var response = awaitbase.SendAsync(request, cancellationToken);
			response.StatusCode = HttpStatusCode.ServiceUnavailable;
			return response;
		}
	}


### <a name="serialization"></a>Procedura: Personalizzare la serializzazione

La classe [MobileServiceClient](http://msdn.microsoft.com/it-it/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) espone una proprietà `SerializerSettings` di tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Con questa proprietà è possibile impostare numerose proprietà di Json.NET come, ad esempio, la proprietà che consente di convertire tutte le proprietà in lettere minuscole:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

<h2><a name="nextsteps"></a>Passaggi successivi</h2>

Dopo aver completato questo argomento di riferimento per i concetti e le procedure, è possibile eseguire importanti attività in Servizi mobili:

* [Introduzione a Servizi mobili]
  <br/>Informazioni sulle nozioni di base per l'uso di Servizi mobili.

* [Introduzione ai dati]
  <br/>Altre informazioni sull'archiviazione e l'esecuzione di query sui dati tramite Servizi mobili.

* [Introduzione all'autenticazione]
  <br/>Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

* [Convalidare e modificare dati mediante script]
  <br/>Altre informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

* [Usare il paging per ridefinire le query]
  <br/>Altre informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

* [Autorizzare gli utenti con gli script]
  <br/>Informazioni sul valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato, che verrà usato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. -->
[Informazioni su Servizi mobili]: #what-is
[Concetti]: #concepts
[Procedura: Creare il client di Servizi mobili]: #create-client
[Procedura: Creare un riferimento alla tabella]: #instantiating
[Procedura: Eseguire query sui dati da un servizio mobile]: #querying
[Filtrare i dati restituiti]: #filtering
[Ordinare i dati restituiti]: #sorting
[Restituire i dati in pagine]: #paging
[Selezionare colonne specifiche]: #selecting
[Cercare dati in base all'ID]: #lookingup
[Procedura: Associare dati all'interfaccia utente in un servizio mobile]: #binding
[Procedura: Inserire dati in un servizio mobile]: #inserting
[Procedura: Modificare dati in un servizio mobile]: #modifying
[Procedura: Eliminare dati in un servizio mobile]: #deleting
[Procedura: Usare la concorrenza ottimistica]: #optimisticconcurrency
[Procedura: Autenticare gli utenti]: #authentication
[Procedura: Gestire gli errori]: #errors
[Procedura: Progettare unit test]: #unit-testing
[Procedura: Eseguire query sui dati da un servizio mobile]: #querying
[Procedura: Personalizzare il client]: #customizing
[Procedura: Usare dati non tipizzati]: #untyped
[Personalizzare le intestazioni di richieste]: #headers
[Personalizzare la serializzazione]: #serialization
[Passaggi successivi]: #nextsteps
[Memorizzazione nella cache del token di autenticazione]: #caching
[Procedura: Chiamare un'API personalizzata]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Esercitazione basata sulla guida introduttiva per Windows Store]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started/
[Esercitazione basata sulla guida introduttiva per Windows Phone]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-wp8/
[Esercitazione relativa ai dati in Windows Store]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-data-dotnet/
[Esercitazione relativa ai dati in Windows Phone]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-data-wp8/
[Autenticazione di Windows Store]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-users-dotnet/
[Autenticazione di Windows Phone]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-users-wp8/
[PasswordVault]: http://msdn.microsoft.com/it-it/library/windows/apps/windows.security.credentials.passwordvault.aspx
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[ProtectedData]: http://msdn.microsoft.com/it-it/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Mobile Services SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-dotnet/
[Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-dotnet
[Convalidare e modificare dati mediante script]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Usare il paging per ridefinire le query]: /it-it/develop/mobile/tutorials/add-paging-to-data-dotnet
[Autorizzare gli utenti con gli script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Metodo LoginAsync]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[Codici di controllo ASCII C0 e C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI per la gestione delle tabelle di Servizi mobili]: http://www.windowsazure.com/it-it/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Esercitazione relativa alla concorrenza ottimistica]: http://www.windowsazure.com/it-it/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/

[IncludeTotalCount]: http://msdn.microsoft.com/it-it/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/it-it/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/it-it/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personalizzata negli SDK del client di Servizi mobili]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[Chiamare un'API personalizzata dal client]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
