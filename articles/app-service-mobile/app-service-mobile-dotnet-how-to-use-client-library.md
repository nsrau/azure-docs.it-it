---
title: Utilizzo della libreria client gestita di App per dispositivi mobili del servizio app (Windows | Microsoft Docs
description: Informazioni su come usare un client .NET per App per dispositivi mobili del servizio app di Azure con le app Windows e Xamarin.
services: app-service\mobile
documentationcenter: ''
author: adrianhall
manager: erikre
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha

---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Come usare il client gestito per App per dispositivi mobili di Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Overview
Questa guida illustra come eseguire scenari comuni usando la libreria client gestita per App per dispositivi mobili del servizio app di Azure in app Windows e Xamarin. Se non si ha familiarità con App per dispositivi mobili, si consiglia di completare prima l'esercitazione [sull'introduzione ad App per dispositivi mobili][1]. In questa Guida, l'attenzione è posta sull’SDK gestito sul lato client. Per altre informazioni sugli SDK lato server per App per dispositivi mobili, vedere la documentazione per [.NET Server SDK][2] o [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Documentazione di riferimento
La documentazione di riferimento per l'SDK per client è disponibile nell'articolo di [riferimento al client .NET di App per dispositivi mobili di Azure][4].
È anche possibile trovare alcuni esempi per client nel [repository GitHub degli esempi di Azure][5].

## <a name="supported-platforms"></a>Piattaforme supportate
La piattaforma .NET supporta le seguenti piattaforme:

* Versioni Android di Xamarin per API da 19 a 24 (KitKat tramite Nougat)
* Versioni iOS di Xamarin per le versioni iOS 8.0 e successive
* Piattaforma UWP (Universal Windows Platform)
* Windows Phone 8.1
* Windows Phone 8.0, ad eccezione delle applicazioni Silverlight

L'autenticazione "flusso server" usa una visualizzazione Web per l'interfaccia utente presentata.  Se il dispositivo non è in grado di presentare una interfaccia utente con visualizzazione Web, sono necessari altri metodi di autenticazione.  Questo SDK non è quindi adatto per i dispositivi di tipo controllo o con restrizioni simili.

## <a name="<a-name="setup"></a>setup-and-prerequisites"></a><a name="setup"></a>Installazione e prerequisiti
Si presuppone che si sia già creato e pubblicato il progetto di back-end di App per dispositivi mobili, che include almeno una tabella.  Nel codice usato in questo argomento la tabella è denominata `TodoItem` e si presenta con le colonne seguenti: `Id`, `Text` e `Complete`. Si tratta della stessa tabella creata durante l'[esercitazione introduttiva ad App per dispositivi mobili].

Il tipo sul lato client tipizzato in C# è la classe seguente:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

Il valore di [JsonPropertyAttribute][6] viene usato per definire il mapping di *PropertyName* tra il tipo di client e la tabella.

Per informazioni sulla creazione di tabelle nel back-end di App per dispositivi mobili, vedere l'argomento [.NET Server SDK][7] o [Node.js Server SDK][8]. Se il back-end delle app per dispositivi mobili è stato creato nel portale di Azure mediante l'esercitazione introduttiva, è anche possibile usare l'impostazione **Tabelle semplici** del [portale di Azure].

### <a name="how-to:-install-the-managed-client-sdk-package"></a>Procedura: Installare il pacchetto SDK client gestito
Per installare il pacchetto SDK per App per dispositivi mobili da [NuGet][9], attenersi a uno dei metodi seguenti:

* **Visual Studio** Fare clic sul progetto con il pulsante destro del mouse, scegliere **Gestisci pacchetti NuGet**, cercare il pacchetto `Microsoft.Azure.Mobile.Client` e fare clic su **Installa**.
* **Xamarin Studio** Fare clic sul progetto con il pulsante destro del mouse, scegliere **Aggiungi** > **Aggiungi pacchetti NuGet**, cercare il pacchetto `Microsoft.Azure.Mobile.Client ` e fare clic su **Aggiungi pacchetto**.

Nel file dell'attività principale aggiungere l'istruzione **using** seguente:

    using Microsoft.WindowsAzure.MobileServices;

### <a name="<a-name="symbolsource"></a>how-to:-work-with-debug-symbols-in-visual-studio"></a><a name="symbolsource"></a>Procedura: Usare i simboli di debug in Visual Studio
I simboli per lo spazio dei nomi di Microsoft.Azure.Mobile sono disponibili in [SymbolSource][10].  Per l'integrazione di SymbolSource in Visual Studio, vedere le [istruzioni di SymbolSource][11].

## <a name="<a-name="create-client"></a>create-the-mobile-apps-client"></a><a name="create-client"></a>Creare il client delle App per dispositivi mobili
Il codice seguente consente di creare l'oggetto [MobileServiceClient][12] utilizzato per accedere al back-end dell'app per dispositivi mobili.

    var client = new MobileServiceClient("MOBILE_APP_URL");

Nel codice precedente sostituire `MOBILE_APP_URL` con l'URL del back-end dell'App per dispositivi mobili, che si trova nel pannello relativo al back-end dell'App per dispositivi mobili nel [portale di Azure]. L'oggetto MobileServiceClient deve essere un singleton.

## <a name="work-with-tables"></a>Usare le tabelle
Nella sezione seguente viene illustrato come cercare e recuperare i record e modificare i dati all'interno della tabella.  Vengono trattati gli argomenti seguenti:

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
* [Modifica delle dimensioni di pagina](#pagesize)

### <a name="<a-name="instantiating"></a>how-to:-create-a-table-reference"></a><a name="instantiating"></a>Procedura: Creare un riferimento alla tabella
Tutti i codici che accedono o modificano i dati nella tabella del back-end chiamano funzioni sull'oggetto `MobileServiceTable` . Ottenere un riferimento alla tabella chiamando il metodo [GetTable] , nel modo seguente:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

L'oggetto restituito usa il modello di serializzazione tipizzato. Viene supportato anche un modello di serializzazione non tipizzato. L'esempio seguente [crea un riferimento a una tabella non tipizzata]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Nelle query non tipizzate, è necessario specificare la stringa di query OData sottostante.

### <a name="<a-name="querying"></a>how-to:-query-data-from-your-mobile-app"></a><a name="querying"></a>Procedura: Eseguire query sui dati dall'app per dispositivi mobili
Questa sezione descrive come eseguire query nel back-end di App per dispositivi mobili, incluse le funzionalità seguenti:

* [Filtrare i dati restituiti](#filtering)
* [Ordinare i dati restituiti](#sorting)
* [Restituire i dati in pagine](#paging)
* [Selezionare colonne specifiche](#selecting)
* [Cercare dati in base all'ID](#lookingup)

> [!NOTE]
> Viene usata una dimensione di pagina basata sul server, per evitare la restituzione di tutte le righe.  Grazie al paging le richieste predefinite di set di dati di grandi dimensioni non hanno conseguenze negative sul servizio.  Per restituire più di 50 righe, usare il metodo `Skip` e `Take`, come descritto in [Restituire i dati in pagine].
> 
> 

### <a name="<a-name="filtering"></a>how-to:-filter-returned-data"></a><a name="filtering"></a>Procedura: Filtrare i dati restituiti
Il codice seguente illustra come filtrare i dati includendo una clausola `Where` in una query. Restituisce tutti gli elementi da `todoTable` per i quali la proprietà `Complete` è uguale a `false`. La funzione [Where] applica un predicato di filtro di riga alla query sulla tabella.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

È possibile visualizzare l'URI della richiesta inviata al back-end usando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o [Fiddler]. Nell'URI della richiesta notare che la stringa di query è modificata:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Questa richiesta OData viene convertita in una query SQL da un SDK del server:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

La funzione passata al metodo `Where` può avere un numero di condizioni arbitrario.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

Questo esempio viene convertito in una query SQL dall'SDK del server:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Questa query può anche essere suddivisa in più clausole:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

I due metodi si equivalgono e possono essere utilizzati in modo intercambiabile.  La prima opzione&mdash;che prevede la concatenazione di più predicati in una query&mdash;è più compatta ed è consigliata.

La clausola `Where` supporta operazioni che vengono convertite nel subset OData. Alcune operazioni sono:

* Operatori relazionali (==, !=, <, <=, >, >=)
* Operatori aritmetici (+, -, /, *, %)
* Approssimazione dei numeri (Math.Floor, Math.Ceiling)
* Funzioni di stringa (Length, Substring, Replace, IndexOf, StartsWith, EndsWith)
* Proprietà relative alla data (Year, Month, Day, Hour, Minute, Second)
* Proprietà di accesso di un oggetto
* Espressioni che combinano queste operazioni.

Per determinare gli elementi supportati dall'SDK del server, vedere la [documentazione relativa a OData v3].

### <a name="<a-name="sorting"></a>how-to:-sort-returned-data"></a><a name="sorting"></a>Procedura: Ordinare i dati restituiti
Il codice seguente illustra come ordinare i dati includendo una funzione [OrderBy] o [OrderByDescending] nella query. L'operazione restituisce elementi della tabella `todoTable` in ordine crescente in base al campo `Text`.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

### <a name="<a-name="paging"></a>how-to:-return-data-in-pages"></a><a name="paging"></a>Procedura: Restituire i dati in pagine
Per impostazione predefinita, il server restituisce solo le prime 50 righe. È possibile aumentare il numero di righe restituite mediante una chiamata al metodo [Take] . Usare `Take` insieme al metodo [Skip] per richiedere una "pagina" specifica dell'intero set di dati restituito dalla query. La query seguente, se eseguita, restituisce le prime tre voci della tabella.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

La query modificata seguente ignora i primi tre risultati e restituisce i tre risultati successivi. La query produce la seconda "pagina" di dati, la cui dimensione corrisponde a tre voci.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

Il metodo [IncludeTotalCount] richiede il conteggio totale di *tutti* i record che sarebbero stati restituiti ignorando qualsiasi clausola di limite/paging specificata:

    query = query.IncludeTotalCount();

In un'app reale è possibile usare query simili all'esempio precedente con un controllo pager o un'interfaccia utente paragonabile per passare da una pagina all'altra.

> [!NOTE]
> Per ignorare il limite di 50 righe in un back-end dell'app per dispositivi mobili, è necessario anche applicare l'attributo [EnableQueryAttribute] al metodo pubblico GET e specificare il comportamento di paging. Se applicato al metodo, l'attributo seguente imposta il numero massimo di righe restituite su 1000:
> 
> [EnableQuery(MaxTop=1000)]
> 
> 

### <a name="<a-name="selecting"></a>how-to:-select-specific-columns"></a><a name="selecting"></a>Procedura: Selezionare colonne specifiche
È possibile specificare il set di proprietà da includere nei risultati aggiungendo alla query una clausola [Select] . Ad esempio, nel codice seguente viene illustrato come selezionare un solo campo e come selezionare e formattare più campi:

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

Tutte le funzioni descritte in precedenza sono di tipo additivo ed è quindi possibile usare la concatenazione. Ogni chiamata concatenata incide ulteriormente sulla query. Un altro esempio:

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="<a-name="lookingup"></a>how-to:-look-up-data-by-id"></a><a name="lookingup"></a>Procedura: Cercare dati in base all'ID
Per cercare oggetti dal database caratterizzati da un particolare ID, è possibile utilizzare la funzione [LookupAsync] .

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="<a-name="untypedqueries"></a>how-to:-execute-untyped-queries"></a><a name="untypedqueries"></a>Procedura: eseguire query non tipizzate
Quando si esegue una query usando un oggetto di tabella non tipizzata, è necessario specificare esplicitamente la stringa di query OData eseguendo una chiamata a [ReadAsync], come nell'esempio seguente:

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Si ottengono valori JSON utilizzabili come contenitore delle proprietà. Per altre informazioni su JToken e Newtonsoft Json.NET, visitare il sito [Json.NET] .

### <a name="<a-name="inserting"></a>how-to:-insert-data-into-a-mobile-app-backend"></a><a name="inserting"></a>Procedura: Inserire dati in un back-end di App per dispositivi mobili
Tutti i tipi di client devono contenere un membro denominato **Id**, che per impostazione predefinita è una stringa. Questo **Id** è necessario per eseguire le operazioni CRUD e per la sincronizzazione offline. Il codice seguente illustra come usare il metodo [InsertAsync] per inserire nuove righe in una tabella. Il parametro contiene i dati da inserire come oggetto .NET.

    await todoTable.InsertAsync(todoItem);

Se nell'elemento `todoItem` non è incluso un valore ID univoco personalizzato durante un inserimento, il server genera un GUID.
È possibile recuperare l'ID generato controllando l'oggetto al termine della chiamata.

Per inserire dati non tipizzati, è possibile usare Json.NET:

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Di seguito è riportato un esempio con un indirizzo di posta elettronica come ID di stringa univoco:

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Uso di valori ID
App per dispositivi mobili supporta valori di stringa univoci personalizzati per la colonna **id** della tabella. Un valore di stringa consente alle applicazioni di usare valori personalizzati come indirizzi e-mail o nomi utente per l'ID.  Gli ID stringa offrono i seguenti vantaggi:

* Gli ID vengono generati senza creare un round trip al database.
* L'unione di record da tabelle o database diversi risulta semplificata.
* L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Quando un valore ID di stringa non è impostato su un record inserito, App per dispositivi mobili genera un valore univoco per l'ID. È possibile usare il metodo [Guid.NewGuid] per generare valori ID personalizzati sul client o nel back-end.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

### <a name="<a-name="modifying"></a>how-to:-modify-data-in-a-mobile-app-backend"></a><a name="modifying"></a>Procedura: Modificare dati in un back-end di App per dispositivi mobili
Il codice seguente illustra come usare il metodo [UpdateAsync] per aggiornare un record esistente con lo stesso ID con le nuove informazioni. Il parametro contiene i dati da aggiornare come oggetto .NET.

    await todoTable.UpdateAsync(todoItem);

Per eliminare dati non tipizzati, è possibile usare [Json.NET] come illustrato di seguito:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Quando si esegue un aggiornamento è necessario specificare un campo `id` . Il back-end usa il campo `id` per identificare la riga da aggiornare. È possibile ottenere il campo `id` dal risultato della chiamata `InsertAsync`. Quando si tenta di aggiornare un elemento senza specificare il valore `ArgumentException`, viene generata un'eccezione `id`.

### <a name="<a-name="deleting"></a>how-to:-delete-data-in-a-mobile-app-backend"></a><a name="deleting"></a>Procedura: Eliminare dati in un back-end di App per dispositivi mobili
Il codice seguente illustra come usare il metodo [DeleteAsync] per eliminare un'istanza esistente. L'istanza è identificata dal campo `id` impostato in `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Per eliminare dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Quando si esegue una richiesta di eliminazione, è necessario specificare un ID. Altre proprietà non vengono passate al servizio o vengono ignorate nel servizio. Il risultato di una chiamata `DeleteAsync` equivale in genere a `null`. È possibile ottenere l'ID da passare dal risultato della chiamata `InsertAsync` . Quando si tenta di eliminare un elemento senza specificare il campo `id`, viene generata un'eccezione `MobileServiceInvalidOperationException`.

### <a name="<a-name="optimisticconcurrency"></a>how-to:-use-optimistic-concurrency-for-conflict-resolution"></a><a name="optimisticconcurrency"></a>Procedura: Usare la concorrenza ottimistica per la risoluzione dei conflitti
È possibile che due o più client scrivano modifiche nello stesso elemento contemporaneamente. Se non viene rilevato un conflitto, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti. **controllo della concorrenza ottimistica** presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse.  Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione.

App per dispositivi mobili supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento, usando la colonna di proprietà di sistema `version` definita per ogni tabella nel back-end di App per dispositivi mobili. Ogni volta che un record viene aggiornato, App per dispositivi mobili imposta la proprietà `version` per quel record su un nuovo valore. Durante ogni richiesta di aggiornamento, la proprietà `version` del record inclusa nella richiesta viene confrontata con la stessa proprietà relativa al record sul server. Se la versione passata con la richiesta non corrisponde a quella del back-end, la libreria client genera un'eccezione `MobileServicePreconditionFailedException<T>` . Il tipo incluso nell'eccezione corrisponde al record del back-end contenente la versione dei server del record. L'applicazione può quindi usare questa informazione per decidere se eseguire nuovamente la richiesta di aggiornamento con il valore `version` corretto dal back-end per effettuare il commit delle modifiche.

Per abilitare la concorrenza ottimistica, definire una colonna sulla classe di tabella per la proprietà di sistema `version` . Ad esempio:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Le applicazioni che usano tabelle non tipizzate abilitano la concorrenza ottimistica impostando il flag `Version` per `SystemProperties` della tabella, come mostrato di seguito.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Oltre ad abilitare la concorrenza ottimistica, è necessario individuare l'eccezione `MobileServicePreconditionFailedException<T>` nel codice quando si esegue una chiamata a [UpdateAsync].  Risolvere il conflitto applicando il valore corretto `version` al record aggiornato ed eseguire una chiamata a [UpdateAsync] con il record risolto. Il codice seguente illustra come risolvere un conflitto di scrittura, qualora venga rilevato.

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
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
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

Per altre informazioni, vedere l'argomento [Sincronizzazione di dati offline nelle App per dispositivi mobili di Azure] .

### <a name="<a-name="binding"></a>how-to:-bind-mobile-apps-data-to-a-windows-user-interface"></a><a name="binding"></a>Procedura: Associare dati di App per dispositivi mobili a un'interfaccia utente Windows
Questa sezione illustra come visualizzare gli oggetti dati restituiti usando elementi dell'interfaccia utente in un'app Windows.  L'esempio di codice seguente associa all'origine dell'elenco una query per la ricerca di elementi non completati. [MobileServiceCollection] crea una raccolta di associazione compatibile con App per dispositivi mobili.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Alcuni controlli del runtime gestito supportano un'interfaccia denominata [ISupportIncrementalLoading]. Questa interfaccia consente ai controlli di richiedere dati aggiuntivi nello scorrimento verso il basso. Per questa interfaccia per le app di Windows universale è disponibile un supporto incorporato tramite [MobileServiceIncrementalLoadingCollection], che gestisce automaticamente le chiamate dai controlli. Usare `MobileServiceIncrementalLoadingCollection` nelle app di Windows come indicato di seguito:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Per usare la nuova raccolta in app di Windows Phone 8 e "Silverlight", usare i metodi di estensione `ToCollection` su `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Per caricare i dati, chiamare `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Quando si usa la raccolta creata tramite la chiamata a `ToCollectionAsync` o `ToCollection`, si ottiene una raccolta che può essere associata ai controlli dell'interfaccia utente.  Questa raccolta è compatibile con il paging.  Poiché la raccolta sta caricando i dati dalla rete, talvolta il caricamento ha esito negativo. Per risolvere questi errori, eseguire l'override del metodo `OnException` in `MobileServiceIncrementalLoadingCollection` per gestire le eccezioni derivanti da chiamate a `LoadMoreItemsAsync`.

Si supponga che la tabella sia costituita da molti campi, ma che si desideri visualizzarne solo alcuni nel controllo. È possibile seguire le indicazioni riportate nella sezione "[Selezionare colonne specifiche](#selecting)" precedente per scegliere specifiche colonne da visualizzare nell'interfaccia utente.

### <a name="<a-name="pagesize"></a>change-the-page-size"></a><a name="pagesize"></a>Modificare le dimensioni di pagina
Per impostazione predefinita, App per dispositivi mobili di Azure restituisce un massimo di 50 elementi per ogni richiesta.  È possibile modificare la dimensione del paging aumentando le dimensioni massime della pagina nel client e nel server.  Per aumentare le dimensioni richieste, specificare `PullOptions` quando si usa `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

Se il valore di `PageSize` è impostato in modo da essere uguale a o maggiore di 100 all'interno del server, verranno restituiti al massimo 100 elementi.

## <a name="<a-name="#offlinesync"></a>work-with-offline-tables"></a><a name="#offlinesync"></a>Usare le tabelle offline
Le tabelle offline usano un archivio SQLite locale per archiviare dati da usare in modalità offline.  Tutte le operazioni delle tabelle vengono eseguite nell'archivio SQLite locale anziché nell'archivio sul server remoto.  Per creare una tabella offline è necessario innanzitutto preparare il progetto:

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione > **Gestisci pacchetti NuGet per la soluzione**, quindi cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** per tutti i progetti della soluzione.
2. (Facoltativo) Per supportare i dispositivi Windows, installare uno dei pacchetti di runtime SQLite seguenti:
   
   * **Windows 8.1 Runtime:** installare [SQLite per Windows 8.1][3].
   * **Windows Phone 8.1:** installare [SQLite per Windows Phone 8.1][4].
   * **Piattaforma UWP (Universal Windows Platform)** Installare [SQLite per Universal Windows Universal][5].
3. (Facoltativo). Per i dispositivi Windows fare clic su **Riferimenti** > **Aggiungi riferimento**, espandere la cartella **Windows** > **Estensioni**, quindi abilitare **SQLite for Windows Runtime** SDK con **Visual C++ 2013 Runtime for Windows** SDK.
    I nomi degli SDK di SQLite sono leggermente diversi per ogni piattaforma Windows.

Prima di poter creare un riferimento alla tabella è necessario preparare l'archivio locale:

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

L'inizializzazione dell'archivio di solito viene effettuata immediatamente dopo aver creato il client.  **OfflineDbPath** deve essere un nome di file appropriato per l'uso in tutte le piattaforme supportate.  Se il percorso è completo (ovvero inizia con una barra), viene usato il percorso.  Se il percorso non è completo, il file viene posizionato in un percorso che dipende dalla piattaforma.

* Per i dispositivi iOS e Android il percorso predefinito è la cartella "File personali".
* Per i dispositivi Windows il percorso predefinito è la cartella "AppData" specifica dell'applicazione.

È possibile ottenere un riferimento alla tabella usando il metodo `GetSyncTable<>`:

    var table = client.GetSyncTable<TodoItem>();

Per usare una tabella offline non è necessario eseguire l'autenticazione.  È necessario eseguire l'autenticazione solo quando si comunica con il servizio back-end.

### <a name="<a-name="syncoffline"></a>syncing-an-offline-table"></a><a name="syncoffline"></a>Sincronizzazione di una tabella offline
Per impostazione predefinita le tabelle offline non sono sincronizzate con il back-end.  La sincronizzazione è suddivisa in due parti.  È possibile inserire le modifiche separatamente rispetto al download di nuovi elementi.  Ecco un metodo di sincronizzazione tipico:

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
                "allTodoItems",
                this.todoTable.CreateQuery());
        }
        catch (MobileServicePushFailedException exc)
        {
            if (exc.PushResult != null)
            {
                syncErrors = exc.PushResult.Errors;
            }
        }

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
        if (syncErrors != null)
        {
            foreach (var error in syncErrors)
            {
                if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                {
                    //Update failed, reverting to server's copy.
                    await error.CancelAndUpdateItemAsync(error.Result);
                }
                else
                {
                    // Discard local change.
                    await error.CancelAndDiscardItemAsync();
                }

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Se il primo argomento di `PullAsync` è null, non viene usata la sincronizzazione incrementale.  Ogni operazione di sincronizzazione recupera tutti i record.

L'SDK esegue un `PushAsync()` implicito prima di estrarre i record.

La gestione dei conflitti viene eseguita su un metodo `PullAsync()`.  È possibile gestire i conflitti allo stesso modo delle tabelle in linea.  Il conflitto viene generato quando viene chiamato `PullAsync()` al posto di durante l'inserimento, l'aggiornamento o l'eliminazione. Se si verificano più conflitti, vengono aggregati in un singolo MobileServicePushFailedException.  Gestire separatamente ogni errore.

## <a name="<a-name="#customapi"></a>work-with-a-custom-api"></a><a name="#customapi"></a>Usare un'API personalizzata
Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON.

Per chiamare un'API personalizzata, è sufficiente chiamare uno dei metodi [InvokeApiAsync] sul client. Ad esempio, la riga di codice seguente invia una richiesta POST all'API **completeAll** sul back-end:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Questo formato è una chiamata tipizzata al metodo, che richiede che il tipo restituito **MarkAllResult** sia definito. Sono supportati sia i metodi tipizzati, sia quelli non tipizzati.

## <a name="<a-name="authentication"></a>authenticate-users"></a><a name="authentication"></a>Autenticare gli utenti
App per dispositivi mobili supporta l'autenticazione e l'autorizzazione di utenti di app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili].

Sono supportati due flussi di autenticazione: flusso *gestito dal client* e flusso *gestito dal server*. Il flusso gestito dal server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso gestito dal client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, poiché si basa su SDK specifici del provider e del dispositivo.

> [!NOTE]
> Nelle app di produzione è consigliabile usare un flusso gestito dal client.
> 
> 

Per configurare l'autenticazione, è necessario registrare l'app con uno o più provider di identità.  Il provider di identità genera un ID client e un segreto client per l'app.  Questi valori vengono quindi impostati nel back-end per abilitare l'autenticazione/l'autorizzazione del Servizio app di Azure.  Per altre informazioni, seguire le istruzioni dettagliate dell'esercitazione [Aggiungere l'autenticazione all'app di Windows].

Questo articolo descrive gli argomenti seguenti:

* [Autenticazione gestita dal client](#clientflow)
* [Autenticazione gestita dal server](#serverflow)
* [Memorizzazione nella cache del token di autenticazione](#caching)

### <a name="<a-name="clientflow"></a>client-managed-authentication"></a><a name="clientflow"></a>Autenticazione gestita dal client
L'app può contattare il provider di identità in modo indipendente e quindi fornire il token restituito durante l'accesso con il back-end. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi. L'autenticazione del flusso client è preferibile rispetto all'uso di un flusso server, perché l'SDK del provider di identità garantisce un'esperienza utente più naturale e consente una maggiore personalizzazione.

Vengono forniti esempi per i modelli di autenticazione del flusso client seguenti:

* [Active Directory Authentication Library](#adal)
* [Facebook o Google](#client-facebook)
* [Live SDK](#client-livesdk)

#### <a name="<a-name="adal"></a>authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Autenticare gli utenti con Active Directory Authentication Library
È possibile usare Active Directory Authentication Library (ADAL) per avviare l'autenticazione degli utenti dal client usando l'autenticazione di Azure Active Directory.

1. Configurare il back-end dell'app per dispositivi mobili per l'accesso ad Azure Active Directory seguendo l'esercitazione [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory] . Assicurarsi di completare il passaggio facoltativo di registrazione di un'applicazione client nativa.
2. In Visual Studio o Xamarin Studio aprire il progetto e aggiungere un riferimento al pacchetto NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory` . Includere nella ricerca le versioni non definitive.
3. Aggiungere il codice seguente all'applicazione, in base alla piattaforma usata. Apportare le sostituzioni seguenti:
   
   * Sostituire **INSERT-AUTHORITY-HERE** con il nome del tenant in cui è stato eseguito il provisioning dell'applicazione. Il formato deve essere https://login.windows.net/contoso.onmicrosoft.com. È possibile copiare questo valore dalla scheda Dominio di Azure Active Directory nel [portale di Azure classico].
   * Sostituire **INSERT-RESOURCE-ID-HERE** con l'ID client per il back-end dell'app per dispositivi mobili. L'ID client è disponibile nella scheda **Avanzate** in **Impostazioni di Azure Active Directory** nel portale.
   * Sostituire **INSERT-CLIENT-ID-HERE** con l'ID client copiato dall'applicazione client nativa.
   * Sostituire **INSERT-REDIRECT-URI-HERE** con l'endpoint */.auth/login/done* del sito, usando lo schema HTTPS. Questo valore deve essere simile a *https://contoso.azurewebsites.net/.auth/login/done*.
     
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
                   AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                       new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                   JObject payload = new JObject();
                   payload["access_token"] = ar.AccessToken;
                   user = await App.MobileService.LoginAsync(
                       MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
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
               AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                   new Uri(redirectUri), new PlatformParameters(view));
               JObject payload = new JObject();
               payload["access_token"] = ar.AccessToken;
               user = await client.LoginAsync(
                   MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
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
               AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                   new Uri(redirectUri), new PlatformParameters(this));
               JObject payload = new JObject();
               payload["access_token"] = ar.AccessToken;
               user = await client.LoginAsync(
                   MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
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

#### <a name="<a-name="client-facebook"></a>single-sign-on-using-a-token-from-facebook-or-google"></a><a name="client-facebook"></a>Accesso singolo usando un token da Facebook o Google
È possibile usare il flusso client come illustrato in questo frammento di codice per Facebook o Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
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

#### <a name="<a-name="client-livesdk"></a>single-sign-in-using-microsoft-account-with-the-live-sdk"></a><a name="client-livesdk"></a>Accesso singolo usando un account Microsoft con Live SDK
Per autenticare gli utenti, è necessario registrare l'app presso il centro per sviluppatori degli account Microsoft. Configurare i dettagli di registrazione nel back-end dell'app per dispositivi mobili. Per creare la registrazione di un account Microsoft e connetterlo al back-end dell'app per dispositivi mobili, completare i passaggi nell'articolo [Come configurare l'applicazione del servizio app per usare l'account di accesso Microsoft]. Se si dispone di entrambe le versioni dell'app (Windows Store e Windows Phone 8/Silverlight), registrare prima la versione di Windows Store.

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

Per altre informazioni, vedere la documentazione su [Windows Live SDK] .

### <a name="<a-name="serverflow"></a>server-managed-authentication"></a><a name="serverflow"></a>Autenticazione gestita dal server
Dopo avere eseguito la registrazione del provider di identità, chiamare il metodo [LoginAsync] su [MobileServiceClient] con il valore [MobileServiceAuthenticationProvider] del provider. Ad esempio, con il codice seguente viene avviato un accesso al flusso server mediante Facebook.

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

In un flusso server, il Servizio app di Azure gestisce il flusso di autenticazione OAuth visualizzando la pagina di accesso del provider selezionato.  Una volta tornato al provider di identità, il Servizio app di Azure genera un token di autenticazione del servizio app. Il metodo [LoginAsync] restituisce un utente [MobileServiceUser], che fornisce sia un elemento [UserId] dell'utente autenticato sia un elemento [MobileServiceAuthenticationToken] sotto forma di token JSON Web (JWT). È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza. Per ulteriori informazioni, vedere [Memorizzazione nella cache del token di autenticazione](#caching).

### <a name="<a-name="caching"></a>caching-the-authentication-token"></a><a name="caching"></a>Memorizzazione nella cache del token di autenticazione
In alcuni casi, la chiamata al metodo di accesso può essere evitata dopo la prima autenticazione riuscita, archiviando il token di autenticazione del provider.  Le app di Windows Store e UWP possono usare [PasswordVault] per memorizzare nella cache il token di autenticazione corrente dopo un accesso riuscito, come indicato di seguito:

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

Il valore UserId viene archiviato come UserName delle credenziali e il token viene archiviato come Password. Negli avvii successivi è possibile usare **PasswordVault** per verificare le credenziali memorizzate nella cache. L'esempio seguente usa le credenziali memorizzate nella cache quando vengono trovate e, in caso contrario, prova a eseguire di nuovo l'autenticazione con il back-end:

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Quando si disconnette un utente, è necessario rimuovere anche le credenziali archiviate, come indicato di seguito:

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Le app Xamarin usano le API [Xamarin.Auth] per archiviare in modo sicuro le credenziali in un oggetto **Account** . Per un esempio dell'uso di queste API, vedere il file di codice [AuthStore.cs] nell'[esempio di condivisione di foto di ContosoMoments].

Quando si usa l'autenticazione gestita dal client, è anche possibile memorizzare nella cache il token di accesso fornito dal provider, ad esempio Facebook o Twitter. Questo token può essere fornito per richiedere un nuovo token di autenticazione dal back-end, come indicato di seguito:

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

## <a name="<a-name="pushnotifications"></a>push-notifications"></a><a name="pushnotifications"></a>Notifiche push
Gli argomenti seguenti descrivono le notifiche push:

* [Registrarsi per le notifiche push](#register-for-push)
* [Ottenere un SID pacchetto di Windows Store](#package-sid)
* [Registrare modelli multipiattaforma](#register-xplat)

### <a name="<a-name="register-for-push"></a>how-to:-register-for-push-notifications"></a><a name="register-for-push"></a>Procedura: Registrarsi per le notifiche push
Il client di App per dispositivi mobili consente di registrarsi per le notifiche push con Hub di notifica di Azure. Durante la registrazione, si ottiene un handle fornito dal servizio di notifica push specifico della piattaforma. È quindi possibile specificare questo valore con qualsiasi tag al momento della creazione della registrazione. Il codice seguente registra l'app Windows per le notifiche push mediante il Servizio di notifica Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Se si effettua il push a WNS, è necessario [ottenere un SID pacchetto di Windows Store](#package-sid) (vedere sotto).  Per ulteriori informazioni sulle app di Windows, compresa la modalità di registrazione per le registrazioni del modello, vedere [Aggiungere notifiche push all'app].

La richiesta di tag dal client non è supportata.  Le richieste di tag vengono eliminate automaticamente dalla registrazione.
Se si desidera registrare il dispositivo con tag, creare un'API personalizzata che usa l'API di hub di notifica per eseguire la registrazione automaticamente.  [Eseguire una chiamata all'API personalizzata](#customapi) invece che al metodo `RegisterNativeAsync()`.

### <a name="<a-name="package-sid"></a>how-to:-obtain-a-windows-store-package-sid"></a><a name="package-sid"></a>Procedura: ottenere un SID pacchetto di Windows Store
Per abilitare le notifiche push nelle app di Windows Store è necessario un SID pacchetto.  Per ricevere un SID pacchetto, registrare l'applicazione in Windows Store.

Per ottenere questo valore:

1. In Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse sul progetto app di Windows Store, quindi scegliere **Store** > **Associa applicazione a Store**.
2. Nella procedura guidata fare clic su **Avanti**, effettuare l'accesso con l'account Microsoft, immettere un nome per l'app in **Riserva un nuovo nome dell'app** e quindi fare clic su **Reserva**.
3. Dopo la creazione della registrazione dell'app, selezionare il nome dell'app, fare clic su **Avanti** e quindi su **Associa**.
4. Accedere al [Windows Dev Center] con l'account Microsoft. In **App personali**fare clic sulla registrazione dell'app creata.
5. Fare clic su **Gestione dell'app** > **Identità dell'app**, e poi scorrere fino a trovare il **SID pacchetto**.

In molti casi il SID pacchetto viene considerato come un URI ed è necessario usare *ms-app: / /* come schema. Prendere nota della versione del SID pacchetto formato tramite la concatenazione di questo valore come prefisso.

Le app di Xamarin richiedono codice aggiuntivo per registrare un'app in esecuzione sulle piattaforme Android o iOS. Per altre informazioni, vedere l'argomento relativo alla piattaforma in uso:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

### <a name="<a-name="register-xplat"></a>how-to:-register-push-templates-to-send-cross-platform-notifications"></a><a name="register-xplat"></a>Procedura: registrare modelli push per inviare notifiche multipiattaforma
Per registrare i modelli, usare il metodo `RegisterAsync()` con i modelli, come indicato di seguito:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

I modelli sono di tipo `JObject` e possono contenere più modelli nel formato JSON seguente:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

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

Tutti i tag vengono eliminati durante la registrazione per motivi di sicurezza. Per aggiungere tag alle istallazione o modelli all'interno delle istallazioni, vedere [Lavorare con l'SDK del server back-end .NET per App per dispositivi mobili di Azure].

Per inviare notifiche usando questi modelli registrati, vedere l'argomento relativo alle [API di Hub di notifica].

## <a name="<a-name="misc"></a>miscellaneous-topics"></a><a name="misc"></a>Argomenti vari
### <a name="<a-name="errors"></a>how-to:-handle-errors"></a><a name="errors"></a>Procedura: Gestire gli errori
Quando si verifica un errore nel back-end, l'SDK del client genera `MobileServiceInvalidOperationException`.  Nell'esempio seguente viene illustrato come gestire un'eccezione che viene restituita dal back-end:

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

Un altro esempio di gestione delle condizioni di errore è disponibile nell' [esempio di file di App per dispositivi mobili]. L'esempio [LoggingHandler] fornisce un gestore di delegato di registrazione (seguente) per registrare le richieste inoltrate al back-end.

### <a name="<a-name="headers"></a>how-to:-customize-request-headers"></a><a name="headers"></a>Procedura: Personalizzare le intestazioni delle richieste
Per supportare lo scenario specifico dell'app, potrebbe essere necessario personalizzare la comunicazione con il back-end di App per dispositivi mobili. È possibile ad esempio aggiungere un'intestazione personalizzata a tutte le richieste in uscita oppure modificare i codici di stato delle risposte. È possibile usare un [DelegatingHandler]personalizzato, come illustrato nell'esempio seguente:

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


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Aggiungere l'autenticazione all'app di Servizi mobili]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md
[Aggiungere notifiche push all'app]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Come configurare l'applicazione del servizio app per usare l'account di accesso Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Come configurare il servizio app per l'account di accesso di Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[crea un riferimento a una tabella non tipizzata]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[portale di Azure]: https://portal.azure.com/
[portale di Azure classico]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API di Hub di notifica]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[esempio di file di App per dispositivi mobili]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[documentazione relativa a OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[esempio di condivisione di foto di ContosoMoments]: https://github.com/azure-appservice-samples/ContosoMoments


<!--HONumber=Oct16_HO2-->


