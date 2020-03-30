---
title: Guida per gli sviluppatori alle entità durevoli in .NET - Funzioni di AzureDeveloper's Guide to Durable Entities in .NET - Azure Functions
description: Come usare le entità durevoli in .NET con l'estensione Funzioni permanenti per Funzioni di Azure.How to work to work with durable entities in .NET with the Durable Functions extension for Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278129"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Guida per gli sviluppatori per le entità durevoli in .NETDeveloper's guide to durable entities in .NET

In questo articolo vengono descritte in dettaglio le interfacce disponibili per lo sviluppo di entità durevoli con .NET, inclusi esempi e consigli generali. 

Le funzioni di entità forniscono agli sviluppatori di applicazioni senza server un modo pratico per organizzare lo stato dell'applicazione come una raccolta di entità con granularità fine. Per altre informazioni sui concetti sottostanti, vedere l'articolo [Entità durevoli: concetti.](durable-functions-entities.md)

Attualmente offriamo due API per la definizione delle entità:We currently offer two APIs for defining entities:

- La **sintassi basata su classi** rappresenta le entità e le operazioni come classi e metodi. Questa sintassi produce codice facilmente leggibile e consente di richiamare le operazioni in modo controllato dal tipo tramite interfacce. 

- La **sintassi basata su funzione** è un'interfaccia di livello inferiore che rappresenta le entità come funzioni. Fornisce un controllo preciso sulla modalità di invio delle operazioni dell'entità e sulla gestione dello stato dell'entità.  

Questo articolo è incentrato principalmente sulla sintassi basata su classi, poiché ci aspettiamo che sia più adatta per la maggior parte delle applicazioni. Tuttavia, la [sintassi basata su funzione](#function-based-syntax) può essere appropriata per le applicazioni che desiderano definire o gestire le proprie astrazioni per lo stato dell'entità e le operazioni. Inoltre, può essere appropriato per l'implementazione di librerie che richiedono la genericità attualmente non supportata dalla sintassi basata su classi. 

> [!NOTE]
> La sintassi basata su classi è solo un livello superiore alla sintassi basata su funzione, pertanto entrambe le varianti possono essere utilizzate in modo intercambiabile nella stessa applicazione. 
 
## <a name="defining-entity-classes"></a>Definizione delle classi di entitàDefining entity classes

L'esempio seguente è `Counter` un'implementazione di un'entità che archivia `Get`un `Delete`singolo valore di tipo integer e offre quattro operazioni `Add`, `Reset`, e .

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

La `Run` funzione contiene il boilerplate necessario per l'utilizzo della sintassi basata su classi. Deve essere una funzione di Azure *statica.* Viene eseguito una volta per ogni messaggio dell'operazione elaborato dall'entità. Quando `DispatchAsync<T>` viene chiamato e l'entità non è già in `T` memoria, costruisce un oggetto di tipo e popola i campi dall'ultimo JSON persistente trovato nell'archivio (se presente). Quindi richiama il metodo con il nome corrispondente.

> [!NOTE]
> Lo stato di un'entità basata su classi viene creato in **modo implicito** prima che `Entity.Current.DeleteState()`l'entità elabori un'operazione e può essere eliminato in modo **esplicito** in un'operazione chiamando .

### <a name="class-requirements"></a>Requisiti della classe
 
Le classi di entità sono CCO (plain old CLR objects) che non richiedono superclassi, interfacce o attributi speciali. Tuttavia:

- La classe deve essere costruibile (vedere [Costruzione di entità](#entity-construction)).
- La classe deve essere serializzabile in JSON (vedere [Serializzazione dell'entità](#entity-serialization)).

Inoltre, qualsiasi metodo che deve essere richiamato come un'operazione deve soddisfare requisiti aggiuntivi:Also, any method that is intended to be invoked as an operation must satisfy additional requirements:

- Un'operazione deve avere al massimo un argomento e non deve avere overload o argomenti di tipo generico.
- Un'operazione destinata a essere chiamata da un'orchestrazione utilizzando un'interfaccia deve restituire `Task` o `Task<T>`.
- Gli argomenti e i valori restituiti devono essere valori serializzabili o oggetti.

### <a name="what-can-operations-do"></a>Cosa possono fare le operazioni?

Tutte le operazioni dell'entità possono leggere e aggiornare lo stato dell'entità e le modifiche allo stato vengono automaticamente rese persistenti nell'archiviazione. Inoltre, le operazioni possono eseguire operazioni di I/O esterne o altri calcoli, entro i limiti generali comuni a tutte le funzioni di Azure.So, operations can perform external I/O or other computations, within the general limits common to all Azure Functions.

Le operazioni hanno anche accesso `Entity.Current` alle funzionalità fornite dal contesto:

* `EntityName`: il nome dell'entità attualmente in esecuzione.
* `EntityKey`: la chiave dell'entità attualmente in esecuzione.
* `EntityId`: l'ID dell'entità attualmente in esecuzione (include il nome e la chiave).
* `SignalEntity`: invia un messaggio unidirezionale a un'entità.
* `CreateNewOrchestration`: avvia una nuova orchestrazione.
* `DeleteState`: elimina lo stato di questa entità.

Ad esempio, è possibile modificare l'entità contatore in modo che avvii un'orchestrazione quando il contatore raggiunge 100 e passa l'ID entità come argomento di input:For example, we can modify the counter entity so it starts an orchestration when the counter reaches 100 and passes the entity ID as an input argument:

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

È possibile accedere direttamente alle entità basate su classi, utilizzando nomi di stringa espliciti per l'entità e le relative operazioni. Di seguito vengono forniti alcuni esempi; per una spiegazione più approfondita dei concetti sottostanti (ad esempio segnali e chiamate) vedere la discussione in [Entità di Access](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Se possibile, è [consigliabile accedere alle entità tramite interfacce](#accessing-entities-through-interfaces), poiché offre un maggiore controllo dei tipi.

### <a name="example-client-signals-entity"></a>Esempio: entità dei segnali clientExample: client signals entity

La funzione Http di Azure seguente implementa un'operazione DELETE usando le convenzioni REST. Invia un segnale di eliminazione all'entità contatore la cui chiave viene passata nel percorso URL.

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

### <a name="example-client-reads-entity-state"></a>Esempio: il client legge lo stato dell'entitàExample: client reads entity state

La funzione Http di Azure seguente implementa un'operazione GET usando le convenzioni REST. Legge lo stato corrente dell'entità contatore la cui chiave viene passata nel percorso URL.

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
> L'oggetto restituito `ReadEntityStateAsync` da è solo una copia locale, ovvero uno snapshot dello stato dell'entità da un momento precedente. In particolare, potrebbe essere obsoleto e la modifica di questo oggetto non ha alcun effetto sull'entità effettiva. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Esempio: orchestrazione prima i segnali, quindi chiama l'entitàExample: orchestration first signals, then calls entity

L'orchestrazione seguente segnala a un'entità contatore di incrementarla e quindi chiama la stessa entità per leggere il valore più recente.

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

## <a name="accessing-entities-through-interfaces"></a>Accesso alle entità tramite interfacceAccessing entities through interfaces

Le interfacce possono essere utilizzate per accedere alle entità tramite oggetti proxy generati. Questo approccio garantisce che il nome e il tipo di argomento di un'operazione corrisponda a ciò che viene implementato. È consigliabile usare le interfacce per l'accesso alle entità quando possibile.

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

Le classi di entità e le interfacce di entità sono simili ai grani e alle interfacce granulosità popolari da [Orleans.](https://www.microsoft.com/research/project/orleans-virtual-actors/) Per ulteriori informazioni sulle analogie e le differenze tra le entità durevoli e Orleans, consultate [Confronto con attori virtuali.](durable-functions-entities.md#comparison-with-virtual-actors)

Oltre a fornire il controllo dei tipi, le interfacce sono utili per una migliore separazione dei problemi all'interno dell'applicazione. Ad esempio, poiché un'entità può implementare più interfacce, una singola entità può servire più ruoli. Inoltre, poiché un'interfaccia può essere implementata da più entità, i modelli di comunicazione generali possono essere implementati come librerie riutilizzabili.

### <a name="example-client-signals-entity-through-interface"></a>Esempio: entità di segnali client tramite interfacciaExample: client signals entity through interface

Il codice `SignalEntityAsync<TEntityInterface>` client può essere utilizzato `TEntityInterface`per inviare segnali alle entità che implementano . Ad esempio:

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

In questo esempio, il `proxy` parametro `ICounter`è un'istanza generata `Delete` dinamicamente di , che converte internamente la chiamata in in un segnale.

> [!NOTE]
> Le `SignalEntityAsync` API possono essere usate solo per operazioni unidirezionali. Anche se un'operazione `Task<T>`restituisce `T` , il valore `default`del parametro sarà sempre null o , non il risultato effettivo.
Ad esempio, non ha senso segnalare l'operazione, `Get` in quanto non viene restituito alcun valore. Al contrario, `ReadStateAsync` i client possono utilizzare direttamente per accedere allo stato `Get` del contatore oppure possono avviare una funzione dell'agente di orchestrazione che chiama l'operazione. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Esempio: orchestrazione prima segnali, quindi chiama l'entità tramite proxyExample: orchestration first signals, then calls entity through proxy

Per chiamare o segnalare un'entità `CreateEntityProxy` dall'interno di un'orchestrazione, può essere utilizzato, insieme al tipo di interfaccia, per generare un proxy per l'entità. Questo proxy può quindi essere utilizzato per chiamare o segnalare operazioni:

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

In modo implicito, `void` tutte le operazioni che `Task` restituiscono vengono segnalate e tutte le operazioni che restituiscono o `Task<T>` vengono chiamate. È possibile modificare questo comportamento predefinito e le operazioni di `SignalEntity<IInterfaceType>` segnale anche se restituiscono Task, utilizzando il metodo in modo esplicito.

### <a name="shorter-option-for-specifying-the-target"></a>Opzione più breve per specificare l'obiettivo

Quando si chiama o si segnala un'entità tramite un'interfaccia, il primo argomento deve specificare l'entità di destinazione. La destinazione può essere specificata specificando l'ID entità o, nei casi in cui è presente una sola classe che implementa l'entità, solo la chiave di entità:The target can be specified either by specifying the entity ID, or, in cases where there's just one class that implements the entity, just the entity key:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Se viene specificata solo la chiave di entità e non `InvalidOperationException` viene trovata un'implementazione univoca in fase di esecuzione, viene generata un'eccezione. 

### <a name="restrictions-on-entity-interfaces"></a>Restrizioni sulle interfacce di entità

Come di consueto, tutti i parametri e i tipi restituiti devono essere serializzabili in JSON. In caso contrario, le eccezioni di serializzazione vengono generate in fase di esecuzione.

Applichiamo anche alcune regole aggiuntive:
* Le interfacce di entità devono definire solo metodi.
* Le interfacce di entità non devono contenere parametri generici.
* I metodi dell'interfaccia di entità non devono avere più di un parametro.
* I metodi dell'interfaccia di entità devono restituire `void`, `Task`, o`Task<T>` 

Se una di queste regole `InvalidOperationException` viene violata, viene generata un'eccezione in fase di esecuzione quando l'interfaccia viene utilizzata come argomento di tipo per `SignalEntity` o `CreateProxy`. Il messaggio di eccezione spiega quale regola è stata interrotta.

> [!NOTE]
> I metodi `void` di interfaccia che restituiscono possono essere segnalati solo (unidirezionale), non chiamati (bidirezionali). I metodi `Task` di `Task<T>` interfaccia che restituiscono o possono essere chiamati o segnalati. Se viene chiamato, restituiscono il risultato dell'operazione o generano nuovamente eccezioni generate dall'operazione. Tuttavia, quando segnalati, non restituiscono il risultato effettivo o l'eccezione dall'operazione, ma solo il valore predefinito.

## <a name="entity-serialization"></a>Serializzazione dell'entità

Poiché lo stato di un'entità è persistente in modo permanente, la classe di entità deve essere serializzabile. Il runtime di Funzioni permanenti utilizza la libreria [di Json.NET](https://www.newtonsoft.com/json) a questo scopo, che supporta una serie di criteri e attributi per controllare il processo di serializzazione e deserializzazione. I tipi di dati C , inclusi quelli di tipo più comunemente utilizzati ,incluse le matrici e i tipi di raccolta, sono già serializzabili e possono essere facilmente utilizzati per definire lo stato delle entità durevoli.

Ad esempio, Json.NET possibile serializzare e deserializzare facilmente la classe seguente:

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

Nell'esempio precedente è stato scelto di includere diversi attributi per rendere più visibile la serializzazione sottostante:In the example above, we chose to include several attributes to make the underlying serialization more visible:
- Annotiamo la `[JsonObject(MemberSerialization.OptIn)]` classe con per ricordarci che la classe deve essere serializzabile e per rendere persistenti solo i membri che sono contrassegnati in modo esplicito come proprietà JSON.
-  Annotiamo i campi da `[JsonProperty("name")]` rendere persistenti per ricordare che un campo fa parte dello stato dell'entità persistente e per specificare il nome della proprietà da usare nella rappresentazione JSON.

Tuttavia, questi attributi non sono obbligatori; altre convenzioni o attributi sono consentiti purché funzionino con Json.NET. Ad esempio, si `[DataContract]` possono utilizzare attributi o nessun attributo:

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

Per impostazione predefinita, il nome della classe *non* viene archiviato `TypeNameHandling.None` come parte della rappresentazione JSON, ovvero viene usata come impostazione predefinita. Questo comportamento predefinito può `JsonObject` essere `JsonProperty` sostituito utilizzando o attributi.

### <a name="making-changes-to-class-definitions"></a>Apportare modifiche alle definizioni di classe

È necessario prestare attenzione quando si apportano modifiche a una definizione di classe dopo l'esecuzione di un'applicazione, perché l'oggetto JSON archiviato potrebbe non corrispondere più alla nuova definizione di classe. Tuttavia, è spesso possibile gestire correttamente la modifica dei formati dei `JsonConvert.PopulateObject`dati, purché si comprenda il processo di deserializzazione utilizzato da .

Ad esempio, ecco alcuni esempi di modifiche e il loro effetto:

1. Se viene aggiunta una nuova proprietà, che non è presente nel codice JSON archiviato, assume il valore predefinito.
1. Se una proprietà viene rimossa, presente nel codice JSON archiviato, il contenuto precedente viene perso.
1. Se una proprietà viene rinominata, l'effetto è come se rimuovesse quella precedente e ne aggiungesse una nuova.
1. Se il tipo di una proprietà viene modificato in modo che non possa più essere deserializzata dal codice JSON archiviato, viene generata un'eccezione.
1. Se il tipo di una proprietà viene modificato, ma può comunque essere deserializzato dal codice JSON archiviato, lo farà.

Sono disponibili molte opzioni per personalizzare il comportamento dei Json.NET. Ad esempio, per forzare un'eccezione se il codice JSON archiviato `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`contiene un campo non presente nella classe, specificare l'attributo . È anche possibile scrivere codice personalizzato per la deserializzazione in grado di leggere JSON archiviato in formati arbitrari.

## <a name="entity-construction"></a>Costruzione di entità

A volte si desidera esercitare un maggiore controllo sul modo in cui vengono costruiti gli oggetti entità. Vengono ora descritte diverse opzioni per modificare il comportamento predefinito durante la costruzione di oggetti entità. 

### <a name="custom-initialization-on-first-access"></a>Inizializzazione personalizzata al primo accesso

In alcuni casi è necessario eseguire alcune inizializzazioni speciali prima di inviare un'operazione a un'entità a cui non è mai stato effettuato l'accesso o che è stata eliminata. Per specificare questo comportamento, è `DispatchAsync`possibile aggiungere una condizione prima di :

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Associazioni nelle classi di entitàBindings in entity classes

A differenza delle funzioni normali, i metodi della classe di entità non hanno accesso diretto alle associazioni di input e output. Al contrario, i dati di associazione devono essere acquisiti nella dichiarazione della funzione del punto di ingresso e quindi passati al metodo `DispatchAsync<T>`. Qualsiasi oggetto passato a `DispatchAsync<T>` verrà passato automaticamente al costruttore della classe di entità come argomento.

L'esempio seguente illustra come è possibile rendere disponibile un riferimento a `CloudBlobContainer` dall'[associazione di input del BLOB](../functions-bindings-storage-blob-input.md) a un'entità basata su classe.

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

### <a name="dependency-injection-in-entity-classes"></a>Inserimento delle dipendenze nelle classi di entitàDependency injection in entity classes

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
> Per evitare problemi con la serializzazione, assicurarsi di escludere i campi destinati a archiviare i valori inseriti dalla serializzazione.

> [!NOTE]
> Diversamente da quando si usa l'inserimento del costruttore nelle normali funzioni di Azure per .NET, il metodo del punto di ingresso delle funzioni per le entità basate su classi *deve* essere dichiarato come `static`. La dichiarazione di un punto di ingresso della funzione non statico può causare conflitti tra l'inizializzatore di oggetto delle normali funzioni di Azure e l'inizializzatore di oggetto delle entità durevoli.

## <a name="function-based-syntax"></a>Sintassi basata su funzioni

Finora ci siamo concentrati sulla sintassi basata su classi, in quanto ci aspettiamo che sia più adatta per la maggior parte delle applicazioni. Tuttavia, la sintassi basata su funzioni può essere appropriata per le applicazioni che desiderano definire o gestire le proprie astrazioni per lo stato dell'entità e le operazioni. Inoltre, può essere appropriato quando si implementano librerie che richiedono la genericità attualmente non supportata dalla sintassi basata su classi. 

Con la sintassi basata su funzione, la funzione Entity gestisce in modo esplicito l'invio dell'operazione e gestisce in modo esplicito lo stato dell'entità. Ad esempio, il codice seguente mostra l'entità *Counter* implementata usando la sintassi basata su funzioni.  

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

È possibile accedere alle funzionalità specifiche dell'entità tramite un oggetto di contesto di tipo `IDurableEntityContext`. Questo oggetto di contesto è disponibile come parametro per `Entity.Current`la funzione di entità e tramite la proprietà async-local .

I membri seguenti forniscono informazioni sull'operazione corrente e consentono di specificare un valore restituito. 

* `EntityName`: il nome dell'entità attualmente in esecuzione.
* `EntityKey`: la chiave dell'entità attualmente in esecuzione.
* `EntityId`: l'ID dell'entità attualmente in esecuzione (include il nome e la chiave).
* `OperationName`: il nome dell'operazione corrente.
* `GetInput<TInput>()`: ottiene l'ingresso per l'operazione corrente.
* `Return(arg)`: restituisce un valore per l'orchestrazione che ha chiamato l'operazione.

I membri seguenti gestiscono lo stato dell'entità (creazione, lettura, aggiornamento, eliminazione). 

* `HasState`: se l'entità esiste, ovvero ha uno stato. 
* `GetState<TState>()`: ottiene lo stato corrente dell'entità. Se non esiste già, viene creato.
* `SetState(arg)`: crea o aggiorna lo stato dell'entità.
* `DeleteState()`: elimina lo stato dell'entità, se esistente. 

Se lo stato `GetState` restituito da è un oggetto, può essere modificato direttamente dal codice dell'applicazione. Non c'è `SetState` bisogno di chiamare di nuovo alla fine (ma anche nessun danno). Se `GetState<TState>` viene chiamato più volte, è necessario utilizzare lo stesso tipo.

Infine, i membri seguenti vengono utilizzati per segnalare altre entità o avviare nuove orchestrazioni:Finally, the following members are used to signal other entities, or start new orchestrations:

* `SignalEntity(EntityId, operation, input)`: invia un messaggio unidirezionale a un'entità.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: avvia una nuova orchestrazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sui concetti relativi alle entità](durable-functions-entities.md)
