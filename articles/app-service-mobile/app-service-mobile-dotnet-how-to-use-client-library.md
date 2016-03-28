<properties
	pageTitle="Utilizzo della libreria client gestita di App per dispositivi mobili del servizio app (Windows | Xamarin) | Microsoft Azure"
	description="Informazioni su come usare un client .NET per App per dispositivi mobili del servizio app di Azure con le app Windows e Xamarin."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="glenga"/>

# Come usare il client gestito per App per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##Panoramica

Questa guida illustra come eseguire scenari comuni usando la libreria client gestita per App per dispositivi mobili del servizio app di Azure in app Windows e Xamarin. Se non si ha familiarità con le app per dispositivi mobili, si consiglia di completare prima l'esercitazione di [introduzione alle app per dispositivi mobili]. In questa Guida, l'attenzione è posta sull’SDK gestito sul lato client. Per altre informazioni sugli SDK lato server per le app per dispositivi mobili, consultare la documentazione sull'uso dell' [SDK del server .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) o [dell'SDK del server Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentazione di riferimento

La documentazione di riferimento per il client SDK è disponibile qui: [Riferimento al client .NET di app per dispositivi mobili di Azure]. È anche possibile trovare alcuni esempi di client nel [repository GitHub degli esempi di Azure].

##<a name="setup"></a>Installazione e prerequisiti

Si presuppone che si abbia già creato e pubblicato il progetto di back-end di App per dispositivi mobili, che include a una tabella. Nel codice usato in questo argomento la tabella è denominata `TodoItem` e si presenta con le colonne seguenti: `Id`, `Text` e `Complete`. Si tratta della stessa tabella creata durante l'esercitazione di [introduzione alle app per dispositivi mobili]

Il tipo sul lato client tipizzato in C# è il seguente:

	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Si noti che il valore di [JsonPropertyAttribute] viene usato per definire il mapping di *PropertyName* tra il tipo di client e la tabella.

Per informazioni sulla creazione di nuove tabelle nel backend delle app per dispositivi mobili, vedere l'articolo relativo all'uso dell'[SDK del server .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) o dell'[SDK del server Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-dynamicschema). Se il back-end delle app per dispositivi mobili è stato creato nel portale di Azure mediante la Guida introduttiva, è inoltre possibile usare l'impostazione **Tabelle semplici** del [portale di Azure].

###<a name="symbolsource"></a>Procedura: Usare i simboli di debug in Visual Studio

I simboli per lo spazio dei nomi Microsoft.Azure.Mobile sono disponibili in [SymbolSource]. Per l'integrazione di SymbolSource in Visual Studio, vedere le [istruzioni di SymbolSource].

##<a name="create-client"></a>Creare il client dell'app per dispositivi mobili

Il codice seguente consente di creare l'oggetto [MobileServiceClient] usato per accedere al back-end delle app per dispositivi mobili.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

Nel codice precedente sostituire `MOBILE_APP_URL` con l'URL del back-end dell’App per dispositivi mobili, che si trova nel pannello per il backend dell’App per dispositivi mobili nel [portale di Azure]. È normale e consigliabile che l'istanza client sia un Singleton.

## Usare le tabelle

Nella sezione seguente viene illustrato come cercare e recuperare i record e modificare i dati all'interno della tabella. Vengono trattati gli argomenti seguenti:

* [Creare un riferimento alla tabella](#instantiating)
* [Eseguire query sui dati](#querying)
* [Filtrare i dati restituiti](#filtering)
* [Ordinare i dati restituiti](#sorting)
* [Restituire i dati in pagine](#paging)
* [Selezionare colonne specifiche](#selecting)
* [Cercare un record per Id](#lookingup)
* [Gestione delle query non tipizzate](#untypedqueries)
* [Inserimento dei dati](#inserting)
* [Aggiornamento dei dati](#updating)
* [Eliminazione dei dati](#deleting)
* [Risoluzione dei conflitti e concorrenza ottimistica](#optimisticconcurrency)
* [Associazione a un'interfaccia utente di Windows](#binding)

###<a name="instantiating"></a>Procedura: Creare un riferimento alla tabella

Tutti i codici che accedono o modificano i dati nella tabella del back-end chiamano funzioni sull'oggetto `MobileServiceTable`. Per ottenere un riferimento alla tabella, chiamare il metodo [GetTable] su un'istanza dell'oggetto `MobileServiceClient`, come di seguito illustrato:

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Questo è il modello tipizzato di serializzazione. Viene supportato anche un modello di serializzazione non tipizzato. Quanto segue consente di [creare un riferimento a una tabella non tipizzata]\:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Nelle query non tipizzate, è necessario specificare la stringa di query OData sottostante.

###<a name="querying"></a>Procedura: Eseguire query sui dati dall'app per dispositivi mobili

Questa sezione descrive come eseguire query nel back-end di App per dispositivi mobili, incluse le funzionalità seguenti:

- [Filtrare i dati restituiti]
- [Ordinare i dati restituiti]
- [Restituire i dati in pagine]
- [Selezionare colonne specifiche]
- [Cercare dati in base all'ID]

>[AZURE.NOTE] Viene usata una dimensione di pagina basata sul server, per evitare la restituzione di tutte le righe. In questo modo, le richieste predefinite di set di dati di grandi dimensioni non hanno conseguenze negative sul servizio. Per restituire più di 50 righe, usare il metodo `Take` descritto in [Restituire i dati in pagine].

###<a name="filtering"></a>Procedura: Filtrare i dati restituiti

Il codice seguente illustra come filtrare i dati includendo una clausola `Where` in una query. Restituisce tutti gli elementi da `todoTable` per i quali la proprietà `Complete` è uguale a `false`. La funzione [Where] applica un predicato di filtro di riga alla query sulla tabella.

	// This query filters out completed TodoItems and items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

È possibile visualizzare l'URI della richiesta inviata al back-end usando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler]. Nell'URI della richiesta riportato di seguito notare che la stringa di query è modificata:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Questa richiesta OData viene convertita in una query SQL da un SDK del server simile al seguente:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

La funzione passata al metodo `Where` può avere un numero di condizioni arbitrario. Ad esempio, la riga seguente:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Viene convertita in una query SQL da un SDK del server simile al seguente:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

L'istruzione `WHERE` riportata sopra trova elementi con stato `Complete` impostato su false con `Text` diverso da null.

Questa query può anche essere suddivisa in più clausole:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

I due metodi si equivalgono e possono essere utilizzati in modo intercambiabile. La prima opzione, che prevede la concatenazione di più predicati in una query, è più compatta ed è consigliata.

La clausola `Where` supporta operazioni che vengono convertite nel subset OData. Sono inclusi gli operatori relazionali (==, !=, <, <=, >, >=), gli operatori aritmetici (+, -, /, *, %), l'approssimazione dei numeri (Math.Floor, Math.Ceiling), funzioni di stringa (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), proprietà relative alla data (Year, Month, Day, Hour, Minute, Second), proprietà di accesso di un oggetto ed espressioni che combinano tutti questi elementi. Per determinare gli elementi supportati dall'SDK del server, consultare la [documentazione di OData v3].

###<a name="sorting"></a>Procedura: Ordinare i dati restituiti

Nel codice seguente viene illustrato come ordinare i dati includendo una funzione [OrderBy] o [OrderByDescending] nella query. L'operazione restituisce elementi della tabella `todoTable` in ordine crescente in base al campo `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Procedura: Restituire i dati in pagine

Per impostazione predefinita, il server restituisce solo le prime 50 righe. È possibile aumentare il numero di righe restituite mediante una chiamata al metodo [Take]. Usare `Take` insieme al metodo [Skip] per richiedere una "pagina" specifica dell'intero set di dati restituito dalla query. La query seguente, se eseguita, restituisce le prime tre voci della tabella.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

La query modificata riportata di seguito ignora i primi tre risultati e restituisce i tre risultati successivi. In sostanza, si tratta della seconda "pagina" di dati, pagina la cui dimensione corrisponde a tre voci.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

È anche possibile usare il metodo [IncludeTotalCount] per assicurarsi che la query consenta di ottenere il conteggio totale di <i>tutti</i> i record che sarebbero stati restituiti ignorando qualsiasi clausola di limite/paging specificata:

	query = query.IncludeTotalCount();

Nella situazione illustrata il passaggio di valori di paging hardcoded ai metodi `Take` e `Skip` è stato semplificato. In un'app reale è possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive.

>[AZURE.NOTE]Per ignorare il limite di 50 righe in un back-end di App per dispositivi mobili, è necessario anche applicare l'attributo [EnableQueryAttribute] al metodo pubblico GET e specificare il comportamento di paging. Se applicato al metodo, l'attributo seguente imposta il numero massimo di righe restituite su 1000:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

È possibile specificare il set di proprietà da includere nei risultati aggiungendo alla query una clausola [Select]. Ad esempio, nel codice seguente viene illustrato come selezionare un solo campo e come selezionare e formattare più campi:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Tutte le funzioni descritte in precedenza sono additive, di conseguenza è possibile ripetere la chiamata per influire maggiormente sulla query. Un altro esempio:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Procedura: Cercare dati in base all'ID

Per cercare oggetti dal database caratterizzati da un particolare ID, è possibile utilizzare la funzione [LookupAsync].

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Procedura: eseguire query non tipizzate

Quando si esegue una query usando un oggetto di tabella non tipizzata, è necessario specificare esplicitamente la stringa di query OData eseguendo una chiamata a [ReadAsync], come nell'esempio seguente:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Si ottengono valori JSON utilizzabili come contenitore delle proprietà. Per altre informazioni su JToken e Newtonsoft Json.NET, visitare il sito [Json.NET].

### <a name="inserting"></a>Procedura: Inserire dati in un back-end di App per dispositivi mobili

Tutti i tipi di client devono contenere un membro denominato **Id**, che per impostazione predefinita è una stringa. Questo **Id** è necessario per eseguire le operazioni CRUD e per gli offline. Il codice seguente illustra come usare il metodo [InsertAsync] per inserire nuove righe in una tabella. Il parametro contiene i dati da inserire come oggetto .NET.

	await todoTable.InsertAsync(todoItem);

Se nell'elemento `todoItem` passato alla chiamata `todoTable.InsertAsync` non è incluso un valore ID univoco personalizzato, viene generato dal server un valore per l'ID che viene impostato nell'oggetto `todoItem` restituito al client.

Per inserire dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito.

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

###Uso di valori ID

App per dispositivi mobili supporta valori di stringa univoci personalizzati per la colonna **id** della tabella. Ciò consente alle applicazioni di usare valori personalizzati come indirizzi e-mail o nomi utente per l'ID.

Gli ID stringa offrono i seguenti vantaggi:

* È possibile generare ID senza generare un round trip del database.
* L'unione di record da tabelle o database diversi risulta semplificata.
* L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Quando un valore ID di stringa non è impostato su un record inserito, App per dispositivi mobili genera un valore univoco per l'ID. È possibile usare il metodo [Guid.NewGuid] per generare valori ID personalizzati sul client o nel back-end.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Procedura: Modificare dati in un back-end di App per dispositivi mobili

Il codice seguente illustra come usare il metodo [UpdateAsync] per aggiornare un record esistente con lo stesso ID con le nuove informazioni. Il parametro contiene i dati da aggiornare come oggetto .NET.

	await todoTable.UpdateAsync(todoItem);

Per inserire dati non tipizzati, è possibile usare [Json.NET] come illustrato di seguito:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Quando si effettua un aggiornamento è necessario specificare un campo `id`. Questo è il modo in cui il back-end identifica l'istanza da aggiornare. È possibile ottenere il campo `id` dal risultato della chiamata `InsertAsync`. Quando si tenta di aggiornare un elemento senza specificare il valore `id`, viene generato un `ArgumentException`.

###<a name="deleting"></a>Procedura: Eliminare dati in un back-end di App per dispositivi mobili

Il codice seguente illustra come usare il metodo[DeleteAsync] per eliminare un'istanza esistente. L'istanza è identificata dal campo `id` impostato in `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Per eliminare dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Si noti che quando si effettua una richiesta di eliminazione, è necessario specificare un ID. Altre proprietà non vengono passate al servizio o vengono ignorate nel servizio. Il risultato di una chiamata `DeleteAsync` equivale in genere a `null`. È possibile ottenere l'ID da passare dal risultato della chiamata `InsertAsync`. Quando si tenta di eliminare un elemento senza specificare il campo `id`, viene generato un `MobileServiceInvalidOperationException`.

###<a name="optimisticconcurrency"></a>Procedura: Usare la concorrenza ottimistica per la risoluzione dei conflitti

È possibile che due o più client scrivano modifiche nello stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Il *controllo della concorrenza ottimistica* presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse. Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione.

App per dispositivi mobili supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento, usando la colonna di proprietà di sistema `version` definita per ogni tabella nel back-end di App per dispositivi mobili. Ogni volta che un record viene aggiornato, App per dispositivi mobili imposta la proprietà `version` per quel record su un nuovo valore. Durante ogni richiesta di aggiornamento, la proprietà `\version` del record inclusa nella richiesta viene confrontata con la stessa proprietà relativa al record sul server. Se la versione passata con la richiesta non corrisponde a quella del back-end, la libreria client genera un'eccezione `MobileServicePreconditionFailedException<T>`. Il tipo incluso nell'eccezione corrisponde al record del back-end contenente la versione dei server del record. L'applicazione può quindi usare questa informazione per decidere se eseguire nuovamente la richiesta di aggiornamento con il valore `version` corretto dal back-end per effettuare il commit delle modifiche.

Per abilitare la concorrenza ottimistica, definire una colonna sulla classe di tabella per la proprietà di sistema `version`, ad esempio:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public byte[] Version { set; get; }
    }


Le applicazioni che usano tabelle non tipizzate abilitano la concorrenza ottimistica impostando il flag `Version` per `SystemProperties` della tabella, come mostrato di seguito.

	//Enable optimistic concurrency by retrieving version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Oltre ad attivare la concorrenza ottimistica, è necessario individuare l'eccezione `MobileServicePreconditionFailedException<T>` nel codice quando si esegue una chiamata a [UpdateAsync]. Risolvere il conflitto applicando il corretto `version` al record aggiornato ed eseguire una chiamata a [UpdateAsync] con il record risolto. Il codice seguente illustra come risolvere un conflitto di scrittura, qualora venga rilevato.

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
	    MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Per altre informazioni, vedere l'argomento [Sincronizzazione di dati offline in App per dispositivi mobili di Azure].

###<a name="binding"></a>Procedura: Associare dati di App per dispositivi mobili a un'interfaccia utente Windows

Questa sezione illustra come visualizzare gli oggetti dati restituiti usando elementi dell'interfaccia utente in un'app Windows. È possibile eseguire il codice di esempio seguente per associare l'origine dell'elenco a una query degli elementi non completati in `todoTable` e visualizzarli in un elenco molto semplice. [MobileServiceCollection] crea una raccolta di associazione compatibile con le app per dispositivi mobili.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Alcuni controlli del runtime gestito supportano un'interfaccia denominata [ISupportIncrementalLoading]. Questa interfaccia consente ai controlli di richiedere dati aggiuntivi nello scorrimento verso il basso. Per questa interfaccia per le app di Windows universale è disponibile un supporto incorporato tramite [MobileServiceIncrementalLoadingCollection] che gestisce automaticamente le chiamate dai controlli. Per usare `MobileServiceIncrementalLoadingCollection` nelle app di Windows, seguire questa procedura:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;

Per usare la nuova raccolta in app di Windows Phone 8 e "Silverlight", usare i metodi di estensione `ToCollection` su `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Per caricare effettivamente i dati, effettuare una chiamata a `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Quando si usa la raccolta creata tramite la chiamata a `ToCollectionAsync` o `ToCollection`, si ottiene una raccolta che può essere associata ai controlli dell'interfaccia utente. Questa raccolta supporta il paging, ossia consente a un controllo di chiedere e ottenere dalla raccolta di "caricare più elementi". A quel punto, il codice utente non è coinvolto e il controllo avvierà il flusso. Poiché tuttavia la raccolta carica dati dalla rete, si suppone che talvolta il caricamento avrà esito negativo. Per gestire tali errori, è possibile eseguire l'override del metodo `OnException` in `MobileServiceIncrementalLoadingCollection` per gestire le eccezioni derivanti da chiamate a `LoadMoreItemsAsync` eseguite dai controlli.

Infine, si supponga che la tabella sia costituita da molti campi, ma si desideri visualizzarne solo alcuni nel controllo. È possibile seguire le indicazioni riportate nella sezione "[Selezionare colonne specifiche](#selecting)" precedente per scegliere specifiche colonne da visualizzare nell'interfaccia utente.

##<a name="#customapi"></a>Usare un'API personalizzata

Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

Per chiamare un'API personalizzata, è sufficiente chiamare uno degli overload del metodo [InvokeApiAsync] sul client. Ad esempio, la riga di codice seguente invia una richiesta POST all'API **completeAll** sul back-end:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Si noti che questa è una chiamata tipizzata al metodo, che richiede che il tipo restituito **MarkAllResult** sia definito. Sono supportati sia i metodi tipizzati, sia quelli non tipizzati.


##<a name="authentication"></a>Autenticare gli utenti

App per dispositivi mobili supporta l'autenticazione e l'autorizzazione di utenti di app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili].

Sono supportati due flussi di autenticazione, un _flusso server_ e un _flusso client_. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, poiché si basa su SDK specifici del provider e del dispositivo.

In entrambi i casi è necessario effettuare la registrazione dell'app con il provider di identità. Il provider di identità fornirà un ID client e un segreto client. È quindi necessario configurare l'autenticazione o l'autorizzazione del servizio app di Azure con l'ID client e il segreto client forniti dal provider di identità. Per altre informazioni, seguire le istruzioni dettagliate dell'esercitazione [Aggiungere l'autenticazione all'app].

###<a name="serverflow"></a>Flusso server
Dopo aver effettuato la registrazione del provider di identità, è sufficiente eseguire una chiamata a MobileServiceCleint.[LoginAsync method] con il valore [MobileServiceAuthenticationProvider] del provider. Ad esempio, con il codice seguente viene avviato un accesso al flusso server mediante Facebook.

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

In un flusso server, il servizio app di Azure gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione del servizio app una volta eseguito correttamente l'accesso con il provider di identità. [LoginAsync method] restituisce [MobileServiceUser], che fornisce sia il valore [UserId] dell'utente autenticato sia [MobileServiceAuthenticationToken] come token Web JSON (JWT). È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza. Per ulteriori informazioni, vedere [Memorizzazione nella cache del token di autenticazione](#caching).

###Flusso client

L'app può anche contattare il provider di identità in modo indipendente e quindi fornire il token restituito al servizio app per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

####Accesso singolo utilizzando un token da Facebook o Google

Nella forma più semplice, è possibile utilizzare il flusso client come illustrato in questo frammento di codice per Facebook o Google.

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
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = string.Format("You are now logged in - {0}", user.UserId);
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

####Accesso singolo utilizzando un account Microsoft con Live SDK

Per poter autenticare gli utenti, è necessario registrare l'app presso il centro per sviluppatori degli account Microsoft. È quindi necessario connettere questa registrazione al back-end dell'app per i dispositivi mobili. Completare i passaggi nell'argomento [Registrare un'app per l'uso delle credenziali d'accesso di un account Microsoft] per creare una registrazione di account Microsoft e connettersi al back-end dell'app per i dispositivi mobili. Se si dispone di entrambe le versioni dell'app (Windows Store e Windows Phone 8/Silverlight), registrare prima la versione di Windows Store.

Il codice seguente esegue l'autenticazione usando Live SDK e usa il token restituito per effettuare l'accesso al back-end dell'app per i dispositivi mobili.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Consultare la documentazione per altre informazioni su [Windows Live SDK].

###<a name="caching"></a>Memorizzazione nella cache del token di autenticazione
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


Nel caso delle app per Windows Phone, è possibile crittografare i dati e memorizzarli nella cache utilizzando la classe [ProtectedData] e archiviare i dati sensibili in uno spazio di memorizzazione isolato.

-->

### <a name="adal"></a>Autenticare gli utenti con Active Directory Authentication Library

È possibile usare Active Directory Authentication Library (ADAL) per far accedere gli utenti all'applicazione tramite Azure Active Directory. Questa procedura è spesso preferibile all'uso dei metodi `loginAsync()`, perché garantisce un'esperienza utente più naturale e consente una maggiore personalizzazione.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso ad Azure Active Directory seguendo l'esercitazione relativa alla [configurazione del servizio app per usare l'account di accesso di Active Directory]. Assicurarsi di completare il passaggio facoltativo di registrazione di un'applicazione client nativa.

2. In Visual Studio o Xamarin Studio aprire il progetto e aggiungere un riferimento al pacchetto NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Includere le versioni preliminari nella ricerca.

3. Aggiungere il codice seguente all'applicazione, in base alla piattaforma usata. Apportare le sostituzioni seguenti:

* Sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato eseguito il provisioning dell'applicazione. Il formato deve essere https://login.windows.net/contoso.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di Azure classico].

* Sostituire **INSERT-RESOURCE-ID-HERE** con l'ID client per il back-end dell'app per dispositivi mobili. Questo è disponibile nella scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.

* Sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.

* Sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint _/.auth/login/done_ del sito, usando lo schema HTTPS. Questo valore dovrebbe essere simile a \__https://contoso.azurewebsites.net/.auth/login/done_.

Il codice necessario per ogni piattaforma è riportato di seguito:

**Windows:**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
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

**Xamarin.iOS**

    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }

**Xamarin.Android**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }

##<a name="pushnotifications">Notifiche push

I seguenti argomenti descrivono le notifiche push:

* [Registrarsi per le notifiche push](#register-for-push)
* [Ottenere un SID pacchetto di Windows Store](#package-sid)
* [Registrare modelli multipiattaforma](#register-xplat)

###<a name="register-for-push"></a>Procedura: Registrarsi per le notifiche push

Il client di App per dispositivi mobili consente di registrarsi per le notifiche push con Hub di notifica di Azure. Durante la registrazione, si ottiene un handle fornito dal servizio di notifica push specifico della piattaforma. È quindi possibile specificare questo valore con qualsiasi tag al momento della creazione della registrazione. Il codice seguente registra l'app Windows per le notifiche push mediante il Servizio di notifica Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Se si effettua il push a WNS, è necessario ottenere un SID pacchetto di Windows Store (vedere sotto). Si noti che in questo esempio due tag sono inclusi con la registrazione. Per ulteriori informazioni sulle app di Windows, compresa la modalità di registrazione per le registrazioni del modello, vedere [Aggiungere notifiche push all'app].

Si noti che la richiesta di tag dal client non è supportata. Le richieste di tag vengono eliminate automaticamente dalla registrazione. Se si desidera registrare il dispositivo con tag, creare un'API personalizzata che usa l'API di hub di notifica per eseguire la registrazione automaticamente. [Eseguire una chiamata all'API personalizzata](#customapi) anziché al metodo `RegisterNativeAsync()`.

###<a name="package-sid"></a>Procedura: ottenere un SID pacchetto di Windows Store

Per abilitare le notifiche push nelle app di Windows Store è necessario un SID pacchetto. Il SID pacchetto viene usato anche per altri elementi, ad esempio il Single Sign-In di Windows. Per ricevere un SID pacchetto è necessario registrare l'applicazione in Windows Store.

Per ottenere questo valore:

1. In Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse sul progetto app di Windows Store, quindi scegliere **Store** > **Associa applicazione a Store**.
2. Nella procedura guidata fare clic su **Avanti**, accedere con l'account Microsoft, immettere un nome per l'app in **Riserva nuovo nome applicazione** e quindi fare clic su **Riserva**.
3. Dopo la creazione della registrazione dell'app, selezionare il nuovo nome dell'app, fare clic su **Avanti** e quindi su **Associa**. Le informazioni di registrazione a Windows Store necessarie verranno aggiunte al manifesto dell'applicazione.
4. Accedere al [Windows Dev Center] con l'account Microsoft. In **App personali ** fare clic sulla registrazione dell'app appena creata.
5. Fare clic su **Gestione dell’app** > **Identità dell’app**, e poi scorrere fino a trovare il **SID pacchetto**.

In molti casi il SID pacchetto questo viene considerato come un URI e sarà necessario usare _ms-app: / /_ come schema. Prendere nota della versione del SID pacchetto formato tramite la concatenazione di questo valore come prefisso.

Le app Xamarin richiedono codice aggiuntivo per poter registrare un’app in esecuzione su iOS o Android, rispettivamente, con i servizi APNS (Apple Push Notification Service) e GCM (Google Cloud Messaging). Per altre informazioni, vedere l'argomento relativo alla piattaforma in uso:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Procedura: registrare modelli push per inviare notifiche multipiattaforma

Per registrare i modelli, usare il metodo `RegisterAsync()` con i modelli, come indicato di seguito:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

I modelli sono di tipo JObject e possono contenere più modelli nel formato JSON seguente:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

Il metodo **RegisterAsync()** accetta anche riquadri secondari:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Si noti che tutti i tag verranno eliminati immediatamente per la sicurezza. Per aggiungere tag all'istallazione o modelli all'interno di istallazioni, vedere [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure].

Per inviare notifiche usando questi modelli registrati, vedere le [API degli hub di notifica].

##<a name="misc"></a>Argomenti vari

###<a name="errors"></a>Procedura: Gestire gli errori

Quando si verifica un errore nel back-end, il client SDK genererà `MobileServiceInvalidOperationException`. Nell'esempio seguente viene illustrato come gestire un'eccezione che viene restituita dal back-end:

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
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

Un altro esempio di gestione delle condizioni di errore è disponibile nell'[esempio di file per le app per dispositivi mobili] - l'esempio [LoggingHandler] fornisce un gestore dei delegati di registrazione (vedere sotto) per registrare le richieste inoltrate al back-end. Ciò consente di eseguire il debug di applicazioni Xamarin in modo semplice anziché basarsi su Fiddler.

###<a name="headers"></a>Procedura: Personalizzare le intestazioni delle richieste

Per supportare lo scenario specifico dell'app, potrebbe essere necessario personalizzare la comunicazione con il back-end di App per dispositivi mobili. È possibile ad esempio aggiungere un'intestazione personalizzata a tutte le richieste in uscita oppure modificare i codici di stato delle risposte. A questo scopo, è possibile specificare un gestore [DelegatingHandler] personalizzato, come illustrato nell'esempio seguente:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->
[Filtrare i dati restituiti]: #filtering
[Ordinare i dati restituiti]: #sorting
[Restituire i dati in pagine]: #paging
[Selezionare colonne specifiche]: #selecting
[Cercare dati in base all'ID]: #lookingup

<!-- Images. -->

<!-- Internal URLs. -->
[introduzione alle app per dispositivi mobili]: app-service-mobile-windows-store-dotnet-get-started.md
[Aggiungere l'autenticazione all'app]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Aggiungere l'autenticazione all'app di Servizi mobili]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Work with .NET backend SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[How to use the Node.js backend SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[How to: Define a table controller]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[Define Tables using a Dynamic Schema]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Sincronizzazione di dati offline in App per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md
[Aggiungere notifiche push all'app]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registrare un'app per l'uso delle credenziali d'accesso di un account Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[configurazione del servizio app per usare l'account di accesso di Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[Riferimento al client .NET di app per dispositivi mobili di Azure]: https://msdn.microsoft.com/it-IT/library/azure/mt419521(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/it-IT/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx
[MobileServiceCollection]: https://msdn.microsoft.com/it-IT/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/it-IT/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/it-IT/library/azure/jj554275(v=azure.10).aspx
[creare un riferimento a una tabella non tipizzata]: https://msdn.microsoft.com/it-IT/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/it-IT/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/it-IT/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/it-IT/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/it-IT/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/it-IT/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/it-IT/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/it-IT/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/it-IT/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/it-IT/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/it-IT/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/it-IT/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/it-IT/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/it-IT/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/it-IT/library/azure/dn250579(v=azure.10).aspx
[portale di Azure]: https://portal.azure.com/
[portale di Azure classico]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/it-IT/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/it-IT/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/it-IT/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API degli hub di notifica]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[esempio di file per le app per dispositivi mobili]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63
[repository GitHub degli esempi di Azure]: https://github.com/Azure-Samples

<!-- External URLs -->
[JsonPropertyAttribute]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[documentazione di OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[SymbolSource]: http://www.symbolsource.org/
[istruzioni di SymbolSource]: http://www.symbolsource.org/Public/Wiki/Using

<!---HONumber=AcomDC_0316_2016-->