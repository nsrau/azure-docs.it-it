---
title: Guida per gli sviluppatori di entità durevoli in .NET-funzioni di Azure
description: Come usare le entità durevoli in .NET con l'estensione Durable Functions per funzioni di Azure.
services: functions
author: sebastianburckhardt
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: a59e5443c80c9372f646edfdae2261157a41acc9
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614896"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Guida per gli sviluppatori di entità durevoli in .NET

In questo articolo vengono descritte in dettaglio le interfacce disponibili per lo sviluppo di entità durevoli con .NET, inclusi esempi e consigli generali. 

Le funzioni di entità offrono agli sviluppatori di applicazioni senza server un modo pratico per organizzare lo stato delle applicazioni come una raccolta di entità con granularità fine. Per informazioni più dettagliate sui concetti sottostanti, vedere l'articolo relativo alle [entità durevoli: concetti](durable-functions-entities.md) .

Sono attualmente disponibili due API per la definizione delle entità:

- La **sintassi basata su** classi rappresenta entità e operazioni come classi e metodi. Questa sintassi produce codice facilmente leggibile e consente di richiamare le operazioni in modo controllato dai tipi attraverso le interfacce. 

- La **sintassi basata su funzioni** è un'interfaccia di livello inferiore che rappresenta le entità come funzioni. Fornisce un controllo preciso sulla modalità di invio delle operazioni dell'entità e sulla modalità di gestione dello stato dell'entità.  

Questo articolo è incentrato principalmente sulla sintassi basata su classi, perché si prevede che sia più adatta per la maggior parte delle applicazioni. Tuttavia, la [sintassi basata su funzione](#function-based-syntax) può essere appropriata per le applicazioni che desiderano definire o gestire le proprie astrazioni per le operazioni e lo stato dell'entità. Inoltre, può essere appropriato per l'implementazione di librerie che richiedono genericità non attualmente supportata dalla sintassi basata su classe. 

> [!NOTE]
> La sintassi basata su classe è semplicemente un livello sopra la sintassi basata su funzioni, quindi entrambe le varianti possono essere utilizzate in modo interscambiabile nella stessa applicazione. 
 
## <a name="defining-entity-classes"></a>Definizione delle classi di entità

Nell'esempio seguente viene illustrata un'implementazione di un'entità `Counter` che archivia un solo valore di tipo integer e offre quattro operazioni `Add`, `Reset`, `Get` e `Delete`.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

La funzione `Run` contiene lo standard richiesto per l'utilizzo della sintassi basata su classe. Deve essere una funzione di Azure *statica* . Viene eseguita una volta per ogni messaggio di operazione elaborato dall'entità. Quando viene chiamato `DispatchAsync<T>` e l'entità non è già in memoria, costruisce un oggetto di tipo `T` e popola i campi dall'ultimo JSON reso permanente trovato nell'archivio (se presente). Quindi richiama il metodo con il nome corrispondente.

> [!NOTE]
> Lo stato di un'entità basata su classi viene **creato in modo implicito** prima che l'entità elabori un'operazione e possa essere **eliminata in modo esplicito** in un'operazione chiamando `Entity.Current.DeleteState()`.

### <a name="class-requirements"></a>Requisiti della classe
 
Le classi di entità sono POCO (Plain Old CLR Object) che non richiedono superclasse, interfacce o attributi speciali. Tuttavia

- La classe deve essere costruibile (vedere [costruzione di entità](#entity-construction)).
- La classe deve essere serializzabile in JSON (vedere [serializzazione di entità](#entity-serialization)).

Inoltre, qualsiasi metodo destinato a essere richiamato come operazione deve soddisfare requisiti aggiuntivi:

- Un'operazione deve avere al massimo un argomento e non deve contenere overload o argomenti di tipo generico.
- Un'operazione che deve essere chiamata da un'orchestrazione mediante un'interfaccia deve restituire `Task` o `Task<T>`.
- Gli argomenti e i valori restituiti devono essere valori serializzabili o oggetti.

### <a name="what-can-operations-do"></a>Operazioni possibili

Tutte le operazioni dell'entità possono leggere e aggiornare lo stato dell'entità e le modifiche apportate allo stato vengono rese automaticamente disponibili nell'archivio. Inoltre, le operazioni possono eseguire operazioni di I/O esterne o altri calcoli, entro i limiti generali comuni a tutte le funzioni di Azure.

Le operazioni possono anche accedere alle funzionalità fornite dal contesto `Entity.Current`:

* `EntityName`: nome dell'entità attualmente in esecuzione.
* `EntityKey`: chiave dell'entità attualmente in esecuzione.
* `EntityId`: ID dell'entità attualmente in esecuzione (include il nome e la chiave).
* `SignalEntity`: Invia un messaggio unidirezionale a un'entità.
* `CreateNewOrchestration`: avvia una nuova orchestrazione.
* `DeleteState`: Elimina lo stato di questa entità.

Ad esempio, è possibile modificare l'entità contatore in modo da avviare un'orchestrazione quando il contatore raggiunge 100 e passa l'ID entità come argomento di input:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Accesso diretto alle entità

È possibile accedere direttamente alle entità basate su classi, usando nomi di stringa espliciti per l'entità e le relative operazioni. Di seguito vengono forniti alcuni esempi. per una spiegazione più approfondita dei concetti sottostanti (ad esempio, i segnali e le chiamate), vedere l'argomento relativo all' [accesso alle entità](durable-functions-entities.md#accessing-entities). 

> [!NOTE]
> Laddove possibile, è consigliabile [accedere alle entità tramite le interfacce](#accessing-entities-through-interfaces), perché fornisce maggiore controllo dei tipi.

### <a name="example-client-signals-entity"></a>Esempio: entità Signals client

La seguente funzione http di Azure implementa un'operazione di eliminazione usando le convenzioni REST. Invia un segnale DELETE all'entità contatore la cui chiave viene passata nel percorso URL.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Esempio: il client legge lo stato dell'entità

La funzione http di Azure seguente implementa un'operazione GET usando le convenzioni REST. Legge lo stato corrente dell'entità contatore la cui chiave viene passata nel percorso URL.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> L'oggetto restituito da `ReadEntityStateAsync` è semplicemente una copia locale, ovvero uno snapshot dello stato dell'entità da un punto precedente nel tempo. In particolare, può essere obsoleto e la modifica di questo oggetto non ha alcun effetto sull'entità effettiva. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Esempio: orchestrazione First Signals, then calls Entity

Tramite l'orchestrazione seguente viene segnalata un'entità contatore per incrementarla e viene quindi chiamata la stessa entità per leggere il valore più recente.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Accesso alle entità tramite interfacce

È possibile utilizzare le interfacce per accedere alle entità tramite oggetti proxy generati. Questo approccio assicura che il nome e il tipo di argomento di un'operazione corrispondano a ciò che viene implementato. È consigliabile usare le interfacce per accedere alle entità quando possibile.

Ad esempio, è possibile modificare l'esempio di contatore come segue:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Le classi di entità e le interfacce di entità sono simili alle interfacce di granularità e granularità diffuse da [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Per altre informazioni su analogie e differenze tra le entità durevoli e Orleans, vedere [confronto con gli attori virtuali](durable-functions-entities.md#comparison-with-virtual-actors).

Oltre a fornire il controllo dei tipi, le interfacce sono utili per una migliore separazione delle problematiche all'interno dell'applicazione. Ad esempio, poiché un'entità può implementare più interfacce, una singola entità può gestire più ruoli. Inoltre, poiché un'interfaccia può essere implementata da più entità, i modelli di comunicazione generale possono essere implementati come librerie riutilizzabili.

### <a name="example-client-signals-entity-through-interface"></a>Esempio: l'entità segnala il client tramite l'interfaccia

Il codice client può usare `SignalEntityAsync<TEntityInterface>` per inviare segnali a entità che implementano `TEntityInterface`. Ad esempio:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

In questo esempio, il parametro `proxy` è un'istanza generata dinamicamente di `ICounter`, che converte internamente la chiamata a `Delete` in un segnale.

> [!NOTE]
> Le API `SignalEntityAsync` possono essere utilizzate solo per le operazioni unidirezionali. Anche se un'operazione restituisce `Task<T>`, il valore del parametro `T` sarà sempre null o `default`, non il risultato effettivo.
Ad esempio, non ha senso segnalare l'operazione `Get`, in quanto non viene restituito alcun valore. I client possono invece usare `ReadStateAsync` per accedere direttamente allo stato del contatore oppure può avviare una funzione dell'agente di orchestrazione che chiama l'operazione `Get`. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Esempio: orchestrazione per primo segnale, quindi chiama l'entità tramite il proxy

Per chiamare o segnalare un'entità all'interno di un'orchestrazione, è possibile utilizzare `CreateEntityProxy`, insieme al tipo di interfaccia, per generare un proxy per l'entità. Questo proxy può quindi essere usato per chiamare o segnalare le operazioni:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

In modo implicito, tutte le operazioni che restituiscono `void` vengono segnalate e vengono chiamate tutte le operazioni che restituiscono `Task` o `Task<T>`. È possibile modificare questo comportamento predefinito e le operazioni di segnalazione anche se restituiscono un'attività, usando il metodo `SignalEntity<IInterfaceType>` in modo esplicito.

### <a name="shorter-option-for-specifying-the-target"></a>Opzione più breve per specificare la destinazione

Quando si chiama o si segnala un'entità usando un'interfaccia, il primo argomento deve specificare l'entità di destinazione. La destinazione può essere specificata specificando l'ID entità oppure, nei casi in cui è presente una sola classe che implementa l'entità, solo la chiave di entità:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Se viene specificata solo la chiave di entità e non è possibile trovare un'implementazione univoca in fase di esecuzione, viene generata `InvalidOperationException`. 

### <a name="restrictions-on-entity-interfaces"></a>Restrizioni sulle interfacce di entità

Come di consueto, tutti i parametri e i tipi restituiti devono essere serializzabili in JSON. In caso contrario, vengono generate eccezioni di serializzazione in fase di esecuzione.

Vengono inoltre applicate alcune regole aggiuntive:
* Le interfacce di entità devono definire solo metodi.
* Le interfacce di entità non devono contenere parametri generici.
* I metodi dell'interfaccia di entità non devono avere più di un parametro.
* I metodi dell'interfaccia di entità devono restituire `void`, `Task` o `Task<T>` 

Se una di queste regole viene violata, viene generata un'`InvalidOperationException` in fase di esecuzione quando l'interfaccia viene usata come argomento di tipo per `SignalEntity` o `CreateProxy`. Il messaggio di eccezione spiega quale regola è stata interrotta.

> [!NOTE]
> I metodi di interfaccia che restituiscono `void` possono essere segnalati solo (unidirezionali), non chiamati (bidirezionale). I metodi di interfaccia che restituiscono `Task` o `Task<T>` possono essere chiamati o segnalati. Se chiamato, restituiscono il risultato dell'operazione oppure generano di nuovo le eccezioni generate dall'operazione. Tuttavia, quando vengono segnalati, non restituiscono il risultato o l'eccezione effettiva dell'operazione, ma solo il valore predefinito.

## <a name="entity-serialization"></a>Serializzazione di entità

Poiché lo stato di un'entità è durevole in modo permanente, la classe di entità deve essere serializzabile. Il runtime di Durable Functions usa la libreria [JSON.NET](https://www.newtonsoft.com/json) per questo scopo, che supporta una serie di criteri e attributi per controllare il processo di serializzazione e deserializzazione. I tipi di C# dati usati più di frequente, tra cui matrici e tipi di raccolta, sono già serializzabili e possono essere usati facilmente per definire lo stato delle entità durevoli.

Ad esempio, Json.NET è in grado di serializzare e deserializzare facilmente la classe seguente:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Attributi di serializzazione

Nell'esempio precedente si è scelto di includere diversi attributi per rendere la serializzazione sottostante più visibile:
- Viene annotata la classe con `[JsonObject(MemberSerialization.OptIn)]` per ricordare che la classe deve essere serializzabile e per salvare in modo permanente solo i membri contrassegnati in modo esplicito come proprietà JSON.
-  Si annotano i campi da salvare in modo permanente con `[JsonProperty("name")]` per ricordare che un campo fa parte dello stato dell'entità permanente e per specificare il nome della proprietà da usare nella rappresentazione JSON.

Tuttavia, questi attributi non sono obbligatori. sono consentite altre convenzioni o attributi purché funzionino con Json.NET. Ad esempio, è possibile usare gli attributi `[DataContract]` oppure nessun attributo:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Per impostazione predefinita, il nome della classe *non* è archiviato come parte della rappresentazione JSON, ovvero si usa `TypeNameHandling.None` come impostazione predefinita. Questo comportamento predefinito può essere sostituito usando gli attributi `JsonObject` o `JsonProperty`.

### <a name="making-changes-to-class-definitions"></a>Apportare modifiche alle definizioni delle classi

È necessario prestare attenzione quando si apportano modifiche a una definizione di classe dopo l'esecuzione di un'applicazione, perché l'oggetto JSON archiviato potrebbe non corrispondere più alla nuova definizione di classe. Tuttavia, spesso è possibile gestire correttamente i formati di dati modificabili purché si conosca il processo di deserializzazione usato da `JsonConvert.PopulateObject`.

Ad esempio, di seguito sono riportati alcuni esempi di modifiche e il relativo effetto:

1. Se viene aggiunta una nuova proprietà, che non è presente nel file JSON archiviato, presuppone il valore predefinito.
1. Se una proprietà viene rimossa, che è presente nel file JSON archiviato, il contenuto precedente viene perso.
1. Se una proprietà viene rinominata, l'effetto è come se venisse rimosso il vecchio e ne verrà aggiunto uno nuovo.
1. Se il tipo di una proprietà viene modificato in modo che non possa più essere deserializzato dal JSON archiviato, viene generata un'eccezione.
1. Se il tipo di una proprietà viene modificato, ma può comunque essere deserializzato dal file JSON archiviato, questa operazione verrà eseguita.

Sono disponibili molte opzioni per personalizzare il comportamento di Json.NET. Ad esempio, per forzare un'eccezione se il JSON archiviato contiene un campo che non è presente nella classe, specificare l'attributo `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`. È anche possibile scrivere codice personalizzato per la deserializzazione in grado di leggere JSON archiviato in formati arbitrari.

## <a name="entity-construction"></a>Costruzione di entità

Talvolta si desidera esercitare un maggiore controllo sulla modalità di costruzione degli oggetti entità. Sono ora descritte diverse opzioni per modificare il comportamento predefinito quando si costruiscono oggetti entità. 

### <a name="custom-initialization-on-first-access"></a>Inizializzazione personalizzata al primo accesso

Occasionalmente è necessario eseguire un'inizializzazione speciale prima di inviare un'operazione a un'entità a cui non è mai stato eseguito l'accesso o che è stata eliminata. Per specificare questo comportamento, è possibile aggiungere un condizionale prima del `DispatchAsync`:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Associazioni nelle classi di entità

Diversamente dalle funzioni regolari, i metodi della classe di entità non hanno accesso diretto alle associazioni di input e di output. Al contrario, i dati di associazione devono essere acquisiti nella dichiarazione della funzione del punto di ingresso e quindi passati al metodo `DispatchAsync<T>`. Qualsiasi oggetto passato a `DispatchAsync<T>` verrà passato automaticamente al costruttore della classe di entità come argomento.

L'esempio seguente illustra come è possibile rendere disponibile un riferimento a `CloudBlobContainer` dall'[associazione di input del BLOB](../functions-bindings-storage-blob.md#input) a un'entità basata su classe.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Per altre informazioni sulle associazioni in Funzioni di Azure, vedere la documentazione su [trigger e associazioni di Funzioni di Azure](../functions-triggers-bindings.md).

### <a name="dependency-injection-in-entity-classes"></a>Inserimento di dipendenze nelle classi di entità

Le classi di entità supportano l'[inserimento di dipendenze di Funzioni di Azure](../functions-dotnet-dependency-injection.md). L'esempio seguente illustra come registrare un servizio `IHttpClientFactory` in un'entità basata su classe.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

Il frammento di codice seguente illustra come incorporare il servizio inserito nella classe di entità.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Per evitare problemi di serializzazione, assicurarsi di escludere i campi destinati a archiviare i valori inseriti dalla serializzazione.

> [!NOTE]
> Diversamente da quando si usa l'inserimento del costruttore nelle normali funzioni di Azure per .NET, il metodo del punto di ingresso delle funzioni per le entità basate su classi *deve* essere dichiarato come `static`. La dichiarazione di un punto di ingresso della funzione non statico può causare conflitti tra l'inizializzatore di oggetto delle normali funzioni di Azure e l'inizializzatore di oggetto delle entità durevoli.

## <a name="function-based-syntax"></a>Sintassi basata su funzioni

Fino a questo punto abbiamo dedicato la sintassi basata su classi, perché si prevede che sia più adatto per la maggior parte delle applicazioni. Tuttavia, la sintassi basata su funzione può essere appropriata per le applicazioni che desiderano definire o gestire le proprie astrazioni per le operazioni e lo stato dell'entità. Inoltre, può essere appropriato quando si implementano librerie che richiedono genericità non attualmente supportata dalla sintassi basata su classe. 

Con la sintassi basata sulla funzione, la funzione di entità gestisce in modo esplicito l'invio dell'operazione e gestisce in modo esplicito lo stato dell'entità. Nel codice seguente, ad esempio, viene illustrata l'entità *Counter* implementata utilizzando la sintassi basata sulla funzione.  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Oggetto contesto dell'entità

È possibile accedere alla funzionalità specifica dell'entità tramite un oggetto di contesto di tipo `IDurableEntityContext`. Questo oggetto context è disponibile come parametro per la funzione dell'entità e tramite la proprietà async-local `Entity.Current`.

I membri seguenti forniscono informazioni sull'operazione corrente e consentono di specificare un valore restituito. 

* `EntityName`: nome dell'entità attualmente in esecuzione.
* `EntityKey`: chiave dell'entità attualmente in esecuzione.
* `EntityId`: ID dell'entità attualmente in esecuzione (include il nome e la chiave).
* `OperationName`: il nome dell'operazione corrente.
* `GetInput<TInput>()`: Ottiene l'input per l'operazione corrente.
* `Return(arg)`: restituisce un valore all'orchestrazione che ha chiamato l'operazione.

I membri seguenti gestiscono lo stato dell'entità (creazione, lettura, aggiornamento, eliminazione). 

* `HasState`: indica se l'entità esiste, ovvero presenta uno stato. 
* `GetState<TState>()`: ottiene lo stato corrente dell'entità. Se non esiste già, viene creato.
* `SetState(arg)`: crea o aggiorna lo stato dell'entità.
* `DeleteState()`: Elimina lo stato dell'entità, se esistente. 

Se lo stato restituito da `GetState` è un oggetto, può essere modificato direttamente dal codice dell'applicazione. Non è necessario chiamare di nuovo `SetState` alla fine, ma anche senza danni. Se `GetState<TState>` viene chiamato più volte, è necessario utilizzare lo stesso tipo.

Infine, i membri seguenti vengono utilizzati per segnalare altre entità o avviare nuove orchestrazioni:

* `SignalEntity(EntityId, operation, input)`: Invia un messaggio unidirezionale a un'entità.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: avvia una nuova orchestrazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sui concetti relativi alle entità](durable-functions-entities.md)
