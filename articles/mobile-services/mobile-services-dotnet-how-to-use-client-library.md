---
title: Utilizzo della libreria client gestita di Servizi mobili (Windows | Microsoft Docs
description: Informazioni su come usare un client .NET per Servizi mobili di Azure con le app Windows e Xamarin.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Come usare la libreria client gestita per Servizi mobili di Azure
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

## Panoramica
Questa guida illustra come eseguire scenari comuni usando la libreria client gestita per Servizi mobili di Azure in app Windows e Xamarin. Gli scenari presentati includono l'esecuzione di query su dati, l'inserimento, l'aggiornamento e l'eliminazione di dati, l'autenticazione di utenti e la gestione di errori. Se non si ha familiarità con Servizi mobili, si consiglia di completare prima l'esercitazione [Introduzione a Servizi mobili](mobile-services-dotnet-backend-xamarin-ios-get-started.md).

[!INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

## <a name="setup"></a>Installazione e prerequisiti
Si presuppone che siano stati creati un servizio mobile e una tabella. Per ulteriori informazioni, vedere [Creare una tabella](http://go.microsoft.com/fwlink/?LinkId=298592). Nel codice usato in questo argomento la tabella è denominata `TodoItem` e si presenta con le colonne seguenti: `Id`, `Text` e `Complete`.

Il tipo .NET tipizzato corrispondente sul lato client è il seguente:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

Si noti che il [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) viene utilizzato per definire il mapping tra il mapping di PropertyName tra il tipo di client e la tabella.

Quando è abilitato lo schema dinamico, in un servizio per dispositivi mobili di back-end JavaScript, Servizi mobili di Azure genera automaticamente nuove colonne basate sull'oggetto nelle richieste di inserimento o di aggiornamento. Per ulteriori informazioni, vedere [Schema dinamico](http://go.microsoft.com/fwlink/?LinkId=296271). In un servizio per dispositivi mobili di back-end .NET, la tabella è definita nel modello di dati del progetto.

## <a name="create-client"></a>Procedura: Creare il client di Servizi mobili
Il codice seguente consente di creare l'oggetto `MobileServiceClient` usato per accedere al servizio mobile.

    MobileServiceClient client = new MobileServiceClient(
        "AppUrl",
        "AppKey"
    );

Nel codice riportato sopra sostituire `AppUrl` e `AppKey` con l'URL del servizio mobile e la chiave applicazione, nell'ordine specificato. Entrambi gli elementi sono disponibili nel portale di Azure classico selezionando il servizio mobile e quindi facendo clic su "Dashboard".

> [!IMPORTANT]
> La chiave dell'applicazione consente di filtrare le richieste casuali nel servizio mobile e viene distribuita con l'applicazione. Poiché questa chiave non è crittografata, non può essere considerata sicura. Per proteggere i dati del servizio mobile, è necessario invece autenticare gli utenti prima di consentire l'accesso. Per altre informazioni, vedere la sezione [Procedura: Autenticare gli utenti](#authentication).
> 
> 

## <a name="instantiating"></a>Procedura: Creare un riferimento alla tabella
Tutto il codice che accede ai dati nella tabella di Servizi mobili o li modifica chiama funzioni sull'oggetto `MobileServiceTable`. Per ottenere un riferimento alla tabella, chiamare il metodo [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) su un'istanza dell'oggetto `MobileServiceClient`, come di seguito illustrato:

    IMobileServiceTable<TodoItem> todoTable =
        client.GetTable<TodoItem>();

Questo è il modello tipizzato di serializzazione. Più avanti in questo argomento verrà illustrato il [modello di serializzazione non tipizzato](#untyped).

## <a name="querying"></a>Procedura: Eseguire query sui dati da un servizio mobile
Questa sezione descrive come eseguire query nel servizio mobile, incluse le funzionalità seguenti:

* [Filtrare i dati restituiti]
* [Ordinare i dati restituiti]
* [Restituire i dati in pagine]
* [Selezionare colonne specifiche]
* [Cercare dati in base all'ID]

> [!NOTE]
> Viene usata una dimensione di pagina basata sul server, per evitare la restituzione di tutte le righe. In questo modo, le richieste predefinite di set di dati di grandi dimensioni non hanno conseguenze negative sul servizio. Per restituire più di 50 righe, usare il metodo `Take` descritto in [Restituire i dati in pagine].
> 
> 

### <a name="filtering"></a>Procedura: Filtrare i dati restituiti
Il codice seguente illustra come filtrare i dati includendo una clausola `Where` in una query. Restituisce tutti gli elementi da `todoTable` per i quali la proprietà `Complete` è uguale a `false`. La funzione `Where` applica un predicato di filtro di riga alla query sulla tabella.

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

L'istruzione `where` riportata sopra trova elementi con stato `Complete` impostato su false con `Text` diverso da null.

Sarebbe stato anche possibile utilizzare più righe:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

I due metodi si equivalgono e possono essere utilizzati in modo intercambiabile. La prima opzione, che prevede la concatenazione di più predicati in una query, è più compatta ed è consigliata.

La clausola `where` supporta operazioni che vengono convertite nel subset OData di Servizi mobili. Sono inclusi gli operatori relazionali (==, !=, <, <=, >, >=), gli operatori aritmetici (+, -, /, *, %), l'approssimazione dei numeri (Math.Floor, Math.Ceiling), funzioni di stringa (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), proprietà relative alla data (Year, Month, Day, Hour, Minute, Second), proprietà di accesso di un oggetto ed espressioni che combinano tutti questi elementi.

### <a name="sorting"></a>Procedura: Ordinare i dati restituiti
Il codice seguente illustra come ordinare i dati includendo una funzione `OrderBy` o `OrderByDescending` nella query. L'operazione restituisce elementi della tabella `todoTable` in ordine crescente in base al campo `Text`.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
     List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
     List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Procedura: Restituire i dati in pagine
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

#### Considerazioni sul paging per un servizio mobile back-end .NET
Per ignorare il limite di 50 righe in un servizio mobile back-end .NET, è necessario anche applicare l'attributo [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) al metodo pubblico GET e specificare il comportamento di paging. Se applicato al metodo, l'attributo seguente imposta il numero massimo di righe restituite su 1000:

    [EnableQuery(MaxTop=1000)]


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

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

## <a name="inserting"></a>Procedura: Inserire dati in un servizio mobile
> [!NOTE]
> Per eseguire operazioni di inserimento, ricerca, eliminazione o aggiornamento su un tipo, è necessario creare un membro denominato **Id**. La classe di esempio **TodoItem** ha infatti un membro denominato **Id**. Un valore ID è valido solo se è sempre presente nelle operazioni di aggiornamento ed eliminazione.
> 
> 

Nel codice seguente viene illustrato come inserire nuove righe in una tabella. Il parametro contiene i dati da inserire come oggetto .NET.

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


### Uso di valori ID
Servizi mobili supporta valori stringa univoci personalizzati per la colonna di tabella **id**. Ciò consente alle applicazioni di usare valori personalizzati come indirizzi e-mail o nomi utente per l'ID.

Gli ID stringa offrono i seguenti vantaggi:

* È possibile generare ID senza generare un round trip del database.
* L'unione di record da tabelle o database diversi risulta semplificata.
* L'integrazione di valori di ID con la logica di un'applicazione è più efficace.

Quando un valore ID di stringa non è impostato su un record inserito, Servizi mobili genera un valore univoco per l'ID. È possibile usare il metodo `Guid.NewGuid()` per generare valori ID personalizzati sul client o in un servizio mobile back-end .NET. Per altre informazioni sulla generazione di GUID in un servizio mobile back-end JavaScript, vedere [Procedura: Generare valori ID univoci](mobile-services-how-to-use-server-scripts.md#generate-guids).

È inoltre possibile usare ID di tipo integer per le tabelle. Per usare un ID integer, è necessario creare la tabella con il comando `mobile table create` e l'opzione `--integerId`. Questo comando viene utilizzato con l'interfaccia della riga di comando (CLI) per Azure. Per ulteriori informazioni sull'utilizzo dell'interfaccia della riga di comando, vedere [Comandi per la gestione delle tabelle di Servizi mobili](../virtual-machines-command-line-tools.md#Mobile_Tables).

## <a name="modifying"></a>Procedura: Modificare dati in un servizio mobile
Nel codice seguente viene illustrato come aggiornare con nuove informazioni un'istanza esistente con lo stesso ID. Il parametro contiene i dati da aggiornare come oggetto .NET.

    await todoTable.UpdateAsync(todoItem);


Per inserire dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito. Si noti che durante un'operazione di aggiornamento è necessario specificare un ID per consentire al servizio mobile di identificare l'istanza da aggiornare. È possibile ottenere l'ID dal risultato della chiamata `InsertAsync`.

    JObject jo = new JObject();
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Se si tenta di aggiornare un elemento senza specificare il valore "Id", il servizio non sarà in grado di individuare l'istanza da aggiornare e quindi Mobile Services SDK genererà un'eccezione `ArgumentException`.

## <a name="deleting"></a>Procedura: Eliminare dati in un servizio mobile
Nel codice seguente viene illustrato come eliminare un'istanza esistente. L'istanza è identificata dal campo "Id" impostato in `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Per eliminare dati non tipizzati, è possibile utilizzare Json.NET come illustrato di seguito. Si noti che per una richiesta di eliminazione è necessario specificare un ID per consentire al servizio mobile di identificare l'istanza da eliminare. Per una richiesta di eliminazione è necessario solo l'ID: altre proprietà non vengono passate al servizio e, in caso contrario, vengono ignorate. Anche il risultato di una chiamata `DeleteAsync` equivale normalmente a `null`. È possibile ottenere l'ID da passare dal risultato della chiamata `InsertAsync`.

    JObject jo = new JObject();
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Se si prova a eliminare un elemento prima di impostare il campo "Id", il servizio non è in grado di individuare l'istanza da eliminare e di conseguenza genera un'eccezione `MobileServiceInvalidOperationException`. Analogamente, se si prova a eliminare un elemento non tipizzato prima di impostare il campo "Id", il servizio genera un'eccezione `MobileServiceInvalidOperationException`.

## <a name="#custom-api"></a>Procedura: Chiamare un'API personalizzata
Un'API personalizzata consente di definire endpoint personalizzati che espongono la funzionalità del server di cui non è possibile eseguire il mapping a un'operazione di inserimento, aggiornamento, eliminazione o lettura. L'utilizzo di un'API personalizzata offre maggiore controllo sulla messaggistica, incluse la lettura e l'impostazione delle intestazioni del messaggio HTTP e la definizione di un formato del corpo del messaggio diverso da JSON. Per un esempio di come creare un'API personalizzata nel servizio mobile, vedere [Procedura: definire un endpoint API personalizzato](mobile-services-dotnet-backend-define-custom-api.md).

Per chiamare un'API personalizzata, è sufficiente chiamare uno degli overload del metodo [InvokeApiAsync] sul client. Ad esempio, la riga di codice seguente invia una richiesta POST all'API **completeAll** sul Servizio mobile:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Si noti che questa è una chiamata tipizzata al metodo, che richiede che il tipo restituito **MarkAllResult** sia definito. Sono supportati sia i metodi tipizzati, sia quelli non tipizzati. Questo esempio è abbastanza semplice in quanto è tipizzato, non invia payload, non ha parametri di query e non modifica le intestazioni della richiesta. Per esempi più realistici e una discussione completa sul metodo [InvokeApiAsync], vedere l'articolo relativo all'[API personalizzata nei Servizi mobili di Azure - SDK client].

## Procedura: Registrarsi per le notifiche push
Il client di Servizi mobili consente di registrarsi per le notifiche push con Hub di notifica di Azure. Durante la registrazione, si ottiene un handle fornito dal servizio di notifica push specifico della piattaforma. È quindi possibile specificare questo valore con qualsiasi tag al momento della creazione della registrazione. Il codice seguente registra l'app Windows per le notifiche push mediante il Servizio di notifica Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =
            await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel and a tag collection.
        var tags = new List<string>{ "mytag1", "mytag2"};
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
    }

Si noti che in questo esempio due tag sono inclusi con la registrazione. Per altre informazioni sulle app Windows, vedere [Aggiungere notifiche push all'app di Servizi mobili](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)

Le app Xamarin in esecuzione su iOS o Android richiedono codice aggiuntivo per poter essere registrate, rispettivamente, con i servizi APNS (Apple Push Notification Service) e GCM (Google Cloud Messaging). Per altre informazioni, vedere **Aggiungere notifiche push all'app di Servizi mobili** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

> [!NOTE]
> Quando è necessario inviare notifiche a specifici utenti registrati, è importante richiedere l'autenticazione prima della registrazione e quindi verificare che l'utente sia autorizzato a registrarsi con un tag specifico. È necessario ad esempio assicurarsi che un utente non si registri con un tag che è l'ID di un altro utente. Per altre informazioni, vedere [Inviare notifiche push agli utenti autenticati](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).
> 
> 

## <a name="pull-notifications"></a>Procedura: usare le notifiche periodiche in un'app Windows
Windows supporta le notifiche periodiche (notifiche pull) per aggiornare i riquadri animati. Con le notifiche periodiche abilitate, Windows accederà periodicamente un endpoint API personalizzato per aggiornare il riquadro dell'app nel menu start. Per usare le notifiche periodiche, è necessario [Definire un'API personalizzata](mobile-services-javascript-backend-define-custom-api.md) che restituisce dati XML in un formato specifico del riquadro. Per altre informazioni, vedere [Notifiche periodiche](https://msdn.microsoft.com/library/windows/apps/hh761461.aspx).

L'esempio seguente consente di attivare le notifiche periodiche per richiedere i dati di modello del riquadro da un endpoint personalizzato *riquadri*:

    TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
        new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
        PeriodicUpdateRecurrence.Hour
    );

Selezionare il valore di [PeriodicUpdateRecurrance](https://msdn.microsoft.com/library/windows/apps/windows.ui.notifications.periodicupdaterecurrence.aspx) più adatto alla frequenza di aggiornamento dei dati desiderata.

## <a name="optimisticconcurrency"></a>Procedura: Usare la concorrenza ottimistica
In alcuni casi, due o più client possono scrivere modifiche sullo stesso elemento contemporaneamente. Se il conflitto non viene rilevato, l'ultima scrittura sovrascrive tutti gli aggiornamenti precedenti, anche se non si tratta del risultato desiderato. Il controllo della concorrenza ottimistica presuppone che per ogni transazione sia possibile eseguire il commit, quindi non procede al blocco delle risorse. Prima di effettuare il commit di una transazione, il controllo della concorrenza ottimistica verifica che i dati non siano stati modificati da un'altra transazione. Se i dati sono stati modificati, verrà eseguito il rollback di tale transazione.

Servizi mobili supporta il controllo della concorrenza ottimistica tenendo traccia delle modifiche apportate a ogni elemento, usando la colonna di proprietà di sistema `__version` definita per ogni tabella creata da Servizi mobili. Ogni volta che un record viene aggiornato, Servizi mobili imposta la proprietà `__version` per quel record su un nuovo valore. Durante ogni richiesta di aggiornamento, la proprietà `__version` del record inclusa nella richiesta viene confrontata con la stessa proprietà relativa al record sul server. Se la versione passata con la richiesta non corrisponde a quella del server, la libreria client .NET di Servizi mobili genera un'eccezione `MobileServicePreconditionFailedException<T>`. Il tipo incluso nell'eccezione corrisponde al record del server e contiene la versione del record presente sul server. L'applicazione può quindi usare questa informazione per decidere se eseguire nuovamente la richiesta di aggiornamento con il valore `__version` corretto nel server per effettuare il commit delle modifiche.

Per abilitare la concorrenza ottimistica, l'applicazione definisce una colonna sulla classe di tabella per la proprietà di sistema `__version`. La definizione seguente ne è un esempio.

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


Le applicazioni che usano tabelle non tipizzate abilitano la concorrenza ottimistica impostando il flag `Version` per `SystemProperties` della tabella, come mostrato di seguito.

    //Enable optimistic concurrency by retrieving __version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


Nel codice seguente viene illustrato come risolvere un conflitto di scrittura, qualora venga rilevato. Per eseguire il commit di una risoluzione è necessario includere nella chiamata `UpdateAsync()` il valore `__version` corretto.

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
        MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
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


Per un esempio più completo dell'utilizzo della concorrenza ottimistica per Servizi mobili, vedere l'[esercitazione relativa alla concorrenza ottimistica].

## <a name="binding"></a>Procedura: associare dati di un servizio mobile a un'interfaccia utente Windows
Questa sezione illustra come visualizzare gli oggetti dati restituiti usando elementi dell'interfaccia utente in un'app Windows. Per eseguire una query sugli elementi incompleti in `todoTable` e visualizzarli in un semplice elenco, è possibile avviare il codice di esempio seguente per associare l'origine dell'elenco a una query. Mediante `MobileServiceCollection` è possibile creare una raccolta di associazione compatibile con Servizi mobili.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Alcuni controlli del runtime gestito supportano un'interfaccia denominata [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Questa interfaccia consente ai controlli di richiedere dati aggiuntivi nello scorrimento verso il basso. Per questa interfaccia, per le app universali di Windows 8.1 è disponibile un supporto incorporato tramite `MobileServiceIncrementalLoadingCollection`, che gestisce automaticamente le chiamate dai controlli. Per usare `MobileServiceIncrementalLoadingCollection` nelle app di Windows, seguire questa procedura:

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

## <a name="authentication"></a>Procedura: Autenticare gli utenti
Servizi mobili supporta l'autenticazione e l'autorizzazione di utenti di app tramite diversi provider di identità esterni: Facebook, Google, Microsoft Account, Twitter e Azure Active Directory. È possibile impostare le autorizzazioni per le tabelle per limitare l'accesso per operazioni specifiche solo agli utenti autenticati. È inoltre possibile utilizzare l'identità degli utenti autenticati per implementare regole di autorizzazione negli script del server. Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili].

Sono supportati due flussi di autenticazione, un *flusso server* e un *flusso client*. Il flusso server è il processo di autenticazione più semplice, poiché si basa sull'interfaccia di autenticazione Web del provider. Il flusso client assicura una maggiore integrazione con funzionalità specifiche del dispositivo, poiché si basa su SDK specifici del provider e del dispositivo.

### Flusso server
Per consentire a Servizi mobili di gestire il processo di autenticazione nelle app di Windows, è necessario effettuare la registrazione dell'app con il provider di identità. Nel proprio servizio mobile è quindi necessario configurare l'ID e il segreto dell'applicazione forniti dal provider. Per altre informazioni, vedere l'esercitazione [Aggiungere l'autenticazione all'app di Servizi mobili].

Dopo aver effettuato la registrazione del provider di identità, è sufficiente chiamare il metodo [LoginAsync] con il valore del provider [MobileServiceAuthenticationProvider]. Ad esempio, con il codice seguente viene avviato un accesso al flusso server mediante Facebook.

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

Se si utilizza un provider di identità diverso da Facebook, sostituire il valore di [MobileServiceAuthenticationProvider] con il nome del provider.

In questo caso, Servizi mobili gestisce il flusso di autenticazione OAuth 2.0 visualizzando la pagina di accesso del provider selezionato e generando un token di autenticazione di Servizi mobili una volta eseguito correttamente l'accesso con il provider di identità. Il metodo [LoginAsync] restituisce un utente [MobileServiceUser], che fornisce sia un elemento [userId] dell'utente autenticato sia un elemento [MobileServiceAuthenticationToken] sotto forma di token Web JSON (JWT). È possibile memorizzare questo token nella cache e riutilizzarlo fino alla scadenza. Per ulteriori informazioni, vedere [Memorizzazione nella cache del token di autenticazione].

### Flusso client
L'applicazione può inoltre contattare il provider di identità in modo indipendente e fornire il token restituito a Servizi mobili per l'autenticazione. Mediante il flusso client è possibile consentire agli utenti di effettuare l'accesso un'unica volta o recuperare dal provider di identità dati utente aggiuntivi.

#### Accesso singolo utilizzando un token da Facebook o Google
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


#### Accesso singolo utilizzando un account Microsoft con Live SDK
Per poter autenticare gli utenti, è necessario registrare l'app presso il centro per sviluppatori degli account Microsoft. È quindi necessario connettere questa registrazione al servizio mobile. Completare i passaggi nell'argomento [Registrare un'app per l'uso delle credenziali d'accesso di un account Microsoft](mobile-services-how-to-register-microsoft-authentication.md) per creare una registrazione di account Microsoft e connettersi al servizio mobile. Se si dispone di entrambe le versioni dell'app (Windows Store e Windows Phone 8/Silverlight), registrare prima la versione di Windows Store.

Il codice seguente si autentica utilizzando Live SDK e utilizza il token restituito per effettuare l'accesso al servizio mobile.

    private LiveConnectSession session;
     //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the mobile service.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the mobile service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope is requested.
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to Mobile Services.
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


### <a name="caching"></a>Memorizzazione nella cache del token di autenticazione
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

## <a name="errors"></a>Procedura: Gestire gli errori
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

## <a name="untyped"></a>Procedura: Usare dati non tipizzati
Il client .NET è progettato per gli scenari fortemente tipizzati. In alcune situazioni una tipizzazione meno rigida può risultare più pratica, come nel caso in cui sia necessario gestire oggetti con uno schema aperto. Questo scenario viene abilitato come segue. Nelle query non verrà utilizzato LINQ ma il formato di trasmissione.

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Si ottengono valori JSON utilizzabili come contenitore delle proprietà. Per ulteriori informazioni su JToken e Json.NET, vedere [Json.NET](http://json.codeplex.com/).

## <a name="unit-testing"></a>Procedura: Progettare unit test
Il valore restituito da `MobileServiceClient.GetTable` e le query sono interfacce e sono quindi facilmente "imitabili" a scopo di test. Di conseguenza, è possibile creare una tabella `MyMockTable : IMobileServiceTable<TodoItem>` che implementa la logica del test.

## <a name="customizing"></a>Procedura: Personalizzare il client
Questa sezione illustra i modi in cui è possibile personalizzare le intestazioni delle richieste e la serializzazione di oggetti JSON nella risposta.

### <a name="headers"></a>Procedura: Personalizzare le intestazioni delle richieste
Per supportare lo scenario specifico dell'app, potrebbe essere necessario personalizzare la comunicazione con il servizio mobile. È possibile ad esempio aggiungere un'intestazione personalizzata a tutte le richieste in uscita oppure modificare i codici di stato delle risposte. A questo scopo, è possibile specificare un gestore DelegatingHandler personalizzato, come illustrato nell'esempio seguente:

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
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

Questo codice aggiunge una nuova intestazione **x-my-header** nella richiesta e imposta arbitrariamente il codice di risposta su non disponibile. In uno scenario reale è necessario impostare il codice di stato della risposta in base alla logica personalizzata necessaria per l'applicazione.

### <a name="serialization"></a>Procedura: Personalizzare la serializzazione
La libreria client di Servizi mobili usa Json.NET per convertire una risposta JSON in oggetti .NET nel client. È possibile configurare il comportamento di questa serializzazione tra i tipi .NET e JSON nei messaggi. La classe [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) espone una proprietà `SerializerSettings` di tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Tramite questa proprietà è possibile impostare numerose proprietà di Json.NET, ad esempio:

    var settings = new JsonSerializerSettings();
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    client.SerializerSettings = settings;

Questa proprietà converte tutte le proprietà in lettere minuscole durante la serializzazione.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Filtrare i dati restituiti]: #filtering
[Ordinare i dati restituiti]: #sorting
[Restituire i dati in pagine]: #paging
[Selezionare colonne specifiche]: #selecting
[Cercare dati in base all'ID]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Use Optimistic Concurrency]: #optimisticconcurrency
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[Memorizzazione nella cache del token di autenticazione]: #caching
[How to: Call a custom API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Aggiungere l'autenticazione all'app di Servizi mobili]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[esercitazione relativa alla concorrenza ottimistica]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personalizzata nei Servizi mobili di Azure - SDK client]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx

<!---HONumber=AcomDC_0727_2016-->