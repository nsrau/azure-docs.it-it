<properties linkid="mobile-services-how-to-xamarin-client" urlDisplayName="Xamarin" pageTitle="How to use the Xamarin Component client - Azure Mobile Services feature guide" metaKeywords="Azure Mobile Services, Xamarin, iOS, Android, .NET client" description="Learn how to use the Xamarin Component client for Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to use the Xamarin Component client for Azure Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Come utilizzare il componente client Xamarin per Servizi mobili di Azure

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/it-it/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a>
    <a href="/it-it/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/it-it/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/it-it/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/it-it/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>

In questa guida viene illustrato come eseguire scenari comuni utilizzando il componente client Xamarin per Servizi mobili di Azure in app di Xamarin per iOS e Android. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti e la gestione di errori. Se non si ha familiarità con Servizi mobili, si consiglia di completare prima le esercitazioni relative alla guida introduttiva a Servizi mobili ([Xamarin.iOS][]/[Xamarin.Android][]) e all'introduzione ai dati in .NET ([Xamarin.iOS][1]/[Xamarin.Android][2]). Per completare l'esercitazione relativa alla guida introduttiva, che consente di configurare il proprio account e di creare il primo servizio mobile, è necessario disporre di [Xamarin][3] e di [Mobile Services SDK][].

## Sommario

-   [Informazioni su Servizi mobili][]
-   [Concetti][]
-   [Procedura: Creare il client di Servizi mobili][]
-   [Procedura: Creare un riferimento alla tabella][]
-   [Procedura: Eseguire query sui dati da un servizio mobile][]

    -   [Filtrare i dati restituiti][]
    -   [Ordinare i dati restituiti][]
    -   [Restituire i dati in pagine][]
    -   [Selezionare colonne specifiche][]
    -   [Cercare dati in base all'ID][]
-   [Procedura: Inserire dati in un servizio mobile][]
-   [Procedura: Modificare dati in un servizio mobile][]
-   [Procedura: Eliminare dati in un servizio mobile][]
-   [Procedura: Autenticare gli utenti][]
-   [Procedura: Gestire gli errori][]
-   [Procedura: Utilizzare dati non tipizzati][]
-   [Procedura: Progettare unit test][]
-   [Passaggi successivi][]

[WACOM.INCLUDE [mobile-services-concepts][]]

## <a name="setup"></a><span class="short-header">Installazione</span>Installazione e prerequisiti

Si presuppone che siano stati creati un servizio mobile e una tabella. Per ulteriori informazioni, vedere [Creare una tabella][]. Nel codice usato in questo argomento la tabella è denominata `TodoItem` e si presenta con le colonne seguenti: `id`, `Text` e `Complete`.

Il tipo .NET tipizzato corrispondente sul lato client è il seguente:

    public class TodoItem
    {
        public string id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

Quando è abilitato lo schema dinamico, in Servizi mobili di Azure vengono generate automaticamente nuove colonne basate sull'oggetto nelle richieste di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico][].

## <a name="create-client"></a><span class="short-header">Creare il client di Servizi mobili</span>Procedura: Creare il client di Servizi mobili

Il codice seguente consente di creare l'oggetto `MobileServiceClient` usato per accedere al servizio mobile.

    MobileServiceClient client = new MobileServiceClient( 
        "AppUrl", 
        "AppKey" 
    ); 

Nel codice riportato sopra sostituire `AppUrl` e `AppKey` con l'URL del servizio mobile e la chiave applicazione, nell'ordine specificato. Entrambi gli elementi sono disponibili nel portale di gestione di Azure selezionando il servizio mobile e quindi facendo clic su "Dashboard".

## <a name="instantiating"></a><span class="short-header">Creare un riferimento alla tabella</span>Procedura: Creare un riferimento alla tabella

Tutto il codice che accede ai dati nella tabella di Servizi mobili o li modifica chiama funzioni sull'oggetto `MobileServiceTable`. Per ottenere un riferimento alla tabella, chiamare la funzione [GetTable][] su un'istanza dell'oggetto `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

Questo è il modello tipizzato di serializzazione. Più avanti in questo argomento verrà illustrato il [modello di serializzazione non tipizzato][Procedura: Utilizzare dati non tipizzati].

## <a name="querying"></a><span class="short-header">Eseguire query sui dati</span>Procedura: Eseguire query sui dati da un servizio mobile

In questa sezione viene descritto come eseguire query nel servizio mobile. Nelle sottosezioni vengono descritti aspetti diversi come ordinamento, filtri e paging.

### <a name="filtering"></a>Procedura: Filtrare i dati restituiti

Il codice seguente illustra come filtrare i dati includendo una clausola `Where` in una query. Restituisce tutti gli elementi da `todoTable` per i quali la proprietà `Complete` è uguale a `false`. La funzione `Where` applica un predicato di filtro di riga alla query sulla tabella.

    // This query filters out completed TodoItems and 
    // items without a timestamp. 
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

È possibile visualizzare l'URI della richiesta inviata al servizio mobile usando un software di ispezione dei messaggi come gli strumenti di sviluppo per browser o Fiddler. Nell'URI della richiesta riportato di seguito si noterà inoltre che viene modificata la stringa di query stessa:

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

L'istruzione `where` riportata sopra trova elementi con stato `Complete` impostato su false con `Text` diverso da null.

Sarebbe stato anche possibile utilizzare più righe:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

I due metodi si equivalgono e possono essere utilizzati in modo intercambiabile. La prima opzione, che prevede la concatenazione di più predicati in una query, è più compatta ed è consigliata.

La clausola `where` supporta operazioni che vengono convertite nel subset OData di Servizi mobili. Sono inclusi gli operatori relazionali (==, !=, \<, \<=, \>, \>=), gli operatori aritmetici (+, -, /, \*, %), l'approssimazione dei numeri (Math.Floor, Math.Ceiling), funzioni di stringa (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), proprietà relative alla data (Year, Month, Day, Hour, Minute, Second), proprietà di accesso di un oggetto ed espressioni che combinano tutti questi elementi.

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti

Il codice seguente illustra come ordinare i dati includendo una funzione `OrderBy` o `OrderByDescending` nella query. L'operazione restituisce elementi della tabella `todoTable` in ordine crescente in base al campo `Text`. Per impostazione predefinita, il server restituisce solo i primi 50 elementi.

<div class="dev-callout"><strong>Nota</strong> <p>Per impostazione predefinita, viene utilizzata una dimensione di pagina basata sul server, per evitare la restituzione di tutti gli elementi. In questo modo, le richieste predefinite di set di dati di grandi dimensioni non hanno conseguenze negative sul servizio. </p> </div>

È possibile aumentare il numero di elementi da restituire chiamando `Take` come descritto nella sezione successiva.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       
    List<TodoItem> items = await query.ToListAsync();           

### <a name="paging"></a>Procedura: Restituire i dati in pagine

Il codice seguente illustra come implementare il paging nei dati restituiti tramite le clausole `Take` e `Skip` nella query. La query seguente, se eseguita, restituisce le prime tre voci della tabella.

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
            

È anche possibile utilizzare il metodo [IncludeTotalCount][] per assicurarsi che la query consenta di ottenere il conteggio totale di *tutti* i record che sarebbero stati restituiti ignorando qualsiasi clausola di limite/paging specificata:

    query = query.IncludeTotalCount();

Nella situazione illustrata il passaggio di valori di paging hardcoded ai metodi `Take` e `Skip` è stato semplificato. In un'app reale è possibile utilizzare query simili con un controllo pager o un'interfaccia utente paragonabile per consentire agli utenti di passare alle pagine precedenti e successive.

### <a name="selecting"></a>Procedura: Selezionare colonne specifiche

È possibile specificare il set di proprietà da includere nei risultati aggiungendo alla query una clausola `Select`. Ad esempio, nel codice seguente viene illustrato come selezionare un solo campo e come selezionare e formattare più campi:

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

    // This query filters out the item with the ID of 25
    TodoItem item25 = await todoTable.LookupAsync(25);

## <a name="inserting"></a><span class="short-header">Inserire dati</span>Procedura: Inserire dati in un servizio mobile

<div class="dev-callout"><strong>Nota</strong> <p>Per eseguire operazioni di inserimento, ricerca, eliminazione o aggiornamento su un tipo, &egrave; necessario creare un membro denominato <strong>Id</strong> (indipendentemente dal caso). La classe di esempio <strong>TodoItem</strong> ha infatti un membro denominato <strong>Id</strong>. Durante le operazioni di inserimento, l'ID deve essere sempre impostato sul valore predefinito. Viceversa, durante le operazioni di aggiornamento e di eliminazione l'ID deve essere sempre impostato su un valore diverso da quello predefinito.</p> </div>

Nel codice seguente viene illustrato come inserire nuove righe in una tabella. Il parametro contiene i dati da inserire come oggetto .NET.

    await todoTable.InsertAsync(todoItem);

Dopo la restituzione della chiamata await `todoTable.InsertAsync`, l'ID dell'oggetto nel server viene inserito nell'oggetto `todoItem` del client.

Per inserire dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito. Di nuovo, si noti che quando si inserisce un oggetto non va specificato un ID.

    JObject jo = new JObject(); 
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Se si prova a inserire un elemento con il campo "Id" già impostato, il servizio genererà un'eccezione `MobileServiceInvalidOperationException`.

## <a name="modifying"></a><span class="short-header">Modificare dati</span>Procedura: Modificare dati in un servizio mobile

Nel codice seguente viene illustrato come aggiornare con nuove informazioni un'istanza esistente con lo stesso ID. Il parametro contiene i dati da aggiornare come oggetto .NET.

    await todoTable.UpdateAsync(todoItem);

Per inserire dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito. Si noti che durante un'operazione di aggiornamento è necessario specificare un ID per consentire al servizio mobile di identificare l'istanza da aggiornare. È possibile ottenere l'ID dal risultato della chiamata `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);
            

Se si prova ad aggiornare un elemento prima di impostare il campo "Id", il servizio non è in grado di individuare l'istanza da aggiornare e di conseguenza genera un'eccezione `MobileServiceInvalidOperationException`. Analogamente, se si prova ad aggiornare un elemento non tipizzato prima di impostare il campo "Id", il servizio genera un'eccezione `MobileServiceInvalidOperationException`.

## <a name="deleting"></a><span class="short-header">Eliminare dati</span>Procedura: Eliminare dati in un servizio mobile

Nel codice seguente viene illustrato come eliminare un'istanza esistente. L'istanza è identificata dal campo "Id" impostato in `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Per eliminare dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito. Si noti che per una richiesta di eliminazione è necessario specificare un ID per consentire al servizio mobile di identificare l'istanza da eliminare. Per una richiesta di eliminazione è necessario solo l'ID: altre proprietà non vengono passate al servizio e, in caso contrario, vengono ignorate. Anche il risultato di una chiamata `DeleteAsync` equivale normalmente a `null`. È possibile ottenere l'ID da passare dal risultato della chiamata `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    await table.DeleteAsync(jo);
            

Se si prova a eliminare un elemento prima di impostare il campo "Id", il servizio non è in grado di individuare l'istanza da eliminare e di conseguenza genera un'eccezione `MobileServiceInvalidOperationException`. Analogamente, se si prova a eliminare un elemento non tipizzato prima di impostare il campo "Id", il servizio genera un'eccezione `MobileServiceInvalidOperationException`.

## <a name="authentication"></a><span class="short-header">Autenticare</span>Procedura: Autenticare gli utenti

Servizi mobili supporta l'autenticazione e l'autorizzazione di utenti delle app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per ulteriori informazioni, vedere l'esercitazione "Introduzione all'autenticazione" ([Xamarin.iOS][4]/[Xamarin.Android][5]).

Sono supportati due flussi di autenticazione: un *flusso server* e un *flusso client*. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, poiché si basa su SDK specifici del provider e del dispositivo.

### Flusso server

Per consentire a Servizi mobili di gestire il processo di autenticazione nell'app di Windows Store o per Windows Phone,
è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio mobile è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider. Per ulteriori informazioni, vedere l'esercitazione "Introduzione all'autenticazione" ([Xamarin.iOS][4]/[Xamarin.Android][5]).

Dopo aver effettuato la registrazione del provider di identità, è sufficiente chiamare il metodo [LoginAsync][] con il valore del provider [MobileServiceAuthenticationProvider][]. Ad esempio, con il codice seguente viene avviato un accesso al flusso server mediante Facebook.

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

Se si utilizza un provider di identità diverso da Facebook, sostituire il valore di [MobileServiceAuthenticationProvider][] con il nome del provider.

In questo caso, Servizi mobili gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione di Servizi mobili una volta eseguito correttamente l'accesso con il provider di identità. Il metodo [LoginAsync][] restituisce un utente [MobileServiceUser][], che fornisce sia un elemento [userId][] dell'utente autenticato sia un elemento [MobileServiceAuthenticationToken][] sotto forma di token Web JSON (JWT). È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza. Per ulteriori informazioni, vedere [Memorizzazione nella cache del token di autenticazione][].

### Flusso client

L'applicazione può inoltre contattare il provider di identità in modo indipendente e fornire il token restituito a Servizi mobili per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

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

### <a name="caching"></a>Memorizzazione nella cache del token di autenticazione

In alcuni casi, è possibile evitare la chiamata al metodo di accesso dopo la prima autenticazione dell'utente. È possibile utilizzare un archivio sicuro locale, ad esempio [Xamarin.Auth][], per memorizzare nella cache l'identità dell'utente corrente al primo accesso. A ogni accesso successivo verrà verificata la presenza dell'identità dell'utente nella cache. Se la cache è vuota, è comunque necessario richiedere all'utente di ripetere la procedura di accesso.

    using Xamarin.Auth;
    var accountStore = AccountStore.Create(); // Xamarin.iOS
    //var accountStore = AccountStore.Create(this); // Xamarin.Android

    // After logging in
    var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
    accountStore.Save(account, "Facebook");

    // Log in 
    var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
    if (accounts.Count != 0)
    {
        user = new MobileServiceUser (accounts[0].Username);
        user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
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
    accountStore.Delete(account, "Facebook");

## <a name="errors"></a><span class="short-header">Gestire gli errori</span>Procedura: Gestire gli errori

In Servizi mobili è possibile rilevare, convalidare e risolvere gli errori in diversi modi.

Ad esempio, gli script del server vengono registrati in un servizio mobile e possono essere utilizzati per eseguire numerose operazioni sui dati inseriti e aggiornati, incluse la convalida e la modifica dei dati. Si consideri ad esempio la definizione e la registrazione di uno script del server per la convalida e la modifica dei dati:

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

## <a name="untyped"></a><span class="short-header">Utilizzare dati non tipizzati</span>Procedura: Utilizzare dati non tipizzati

Il componente client Xamarin è progettato per gli scenari fortemente tipizzati. In alcune situazioni una tipizzazione meno rigida può risultare più pratica, come nel caso in cui sia necessario gestire oggetti con uno schema aperto. Questo scenario viene abilitato come segue. Nelle query non verrà utilizzato LINQ ma il formato di trasmissione.

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");         

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Si ottengono valori JSON utilizzabili come contenitore delle proprietà. Per ulteriori informazioni su JToken e Json.NET, vedere [Json.NET][].

## <a name="unit-testing"></a><span class="short-header">Progettare test</span>Procedura: Progettare unit test

Il valore restituito da `MobileServiceClient.GetTable` e le query sono interfacce e sono quindi facilmente "imitabili" a scopo di test. Di conseguenza, è possibile creare una tabella `MyMockTable : IMobileServiceTable<TodoItem>` che implementa la logica del test.

## <a name="nextsteps"></a>Passaggi successivi

Dopo aver completato questo argomento di riferimento per i concetti e le procedure, è possibile eseguire importanti attività in Servizi mobili:

-   Introduzione a Servizi mobili ([Xamarin.iOS][6]/[Xamarin.Android][7])
    Nozioni di base sull'uso di Servizi mobili.

-   Introduzione ai dati ([Xamarin.iOS][8]/[Xamarin.Android][9])
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   Introduzione all'autenticazione ([Xamarin.iOS][10]/[Xamarin.Android][11])
    Informazioni sull'autenticazione degli utenti dell'app con un provider di identità.

-   Uso di script per la convalida e la modifica di dati ([Xamarin.iOS][12]/[Xamarin.Android][13])
    Ulteriori informazioni sull'uso di script del server in Servizi mobili per convalidare e modificare i dati inviati dall'app.

-   Uso del paging per ridefinire le query ([Xamarin.iOS][14]/[Xamarin.Android][15])
    Informazioni su come usare il paging nelle query per controllare la quantità di dati gestiti in un'unica richiesta.

-   Autorizzazione di utenti con script ([Xamarin.iOS][16]/[Xamarin.Android][17])
    Informazioni sul valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato, che verrà usato per filtrare i dati restituiti da Servizi mobili.

<!-- Anchors. --> <!-- URLs. -->

  [.NET Framework]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework"
  [HTML/JavaScript]: /it-it/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript"
  [iOS]: /it-it/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS"
  [Android]: /it-it/develop/mobile/how-to-guides/work-with-android-client-library/ "Android"
  [Xamarin]: /it-it/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin"
  [Xamarin.iOS]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios/
  [Xamarin.Android]: /it-it/develop/mobile/tutorials/get-started-xamarin-android/
  [1]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios/
  [2]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-android/
  [3]: http://xamarin.com/download/
  [Mobile Services SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
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
  [Procedura: Inserire dati in un servizio mobile]: #inserting
  [Procedura: Modificare dati in un servizio mobile]: #modifying
  [Procedura: Eliminare dati in un servizio mobile]: #deleting
  [Procedura: Autenticare gli utenti]: #authentication
  [Procedura: Gestire gli errori]: #errors
  [Procedura: Utilizzare dati non tipizzati]: #untyped
  [Procedura: Progettare unit test]: #unit-testing
  [Passaggi successivi]: #nextsteps
  [mobile-services-concepts]: ../includes/mobile-services-concepts.md
  [Creare una tabella]: http://go.microsoft.com/fwlink/?LinkId=298592
  [Schema dinamico]: http://go.microsoft.com/fwlink/?LinkId=296271
  [GetTable]: http://msdn.microsoft.com/it-it/library/windowsazure/jj554275.aspx
  [IncludeTotalCount]: http://msdn.microsoft.com/it-it/library/windowsazure/jj730933.aspx
  [4]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios/
  [5]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-android/
  [LoginAsync]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
  [MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
  [MobileServiceUser]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
  [userId]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
  [MobileServiceAuthenticationToken]: http://msdn.microsoft.com/it-it/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
  [Memorizzazione nella cache del token di autenticazione]: #caching
  [Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth
  [Json.NET]: http://json.codeplex.com/
  [6]: /it-it/develop/mobile/tutorials/get-started-xamarin-ios
  [7]: /it-it/develop/mobile/tutorials/get-started-xamarin-android
  [8]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [9]: /it-it/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [10]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [11]: /it-it/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [12]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
  [13]: /it-it/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
  [14]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [15]: /it-it/develop/mobile/tutorials/add-paging-to-data-xamarin-android
  [16]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
  [17]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
