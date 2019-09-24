---
title: Entità durevoli - Funzioni di Azure
description: Questo articolo fornisce informazioni sulle entità durevoli e su come usarle nell'estensione Durable Functions per Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 99e61cef55bd97704063e4d2da90909d0376c327
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961457"
---
# <a name="entity-functions-preview"></a>Funzioni di entità (anteprima)

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccole parti di stato, note come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, il *trigger di entità*. A differenza delle funzioni dell'agente di orchestrazione, però, le funzioni di entità non hanno vincoli di codice specifici. Le funzioni di entità, inoltre, gestiscono lo stato in modo esplicito anziché in modo implicito, rappresentandolo tramite flusso di controllo.

> [!NOTE]
> Le funzioni di entità e le funzionalità correlate sono disponibili solo in Durable Functions 2.0 e versioni successive. Le funzioni di entità sono attualmente disponibili in versione di anteprima pubblica.

## <a name="entity-identity"></a>Identità di entità

È possibile accedere alle entità (talvolta dette anche *istanze* di entità) tramite un identificatore univoco, ovvero l'*ID entità*. Un ID entità è semplicemente una coppia di stringhe che identifica in modo univoco un'istanza di entità. È costituita da:

* Un **nome entità**, ossia un nome che identifica il tipo di entità (ad esempio, "Counter").
* Una **chiave di entità**, ossia una stringa che identifica in modo univoco l'entità tra tutte le altre entità con lo stesso nome (ad esempio un GUID).

È ad esempio possibile usare una funzione di entità *counter* per tenere il punteggio in un gioco online. Ogni istanza del gioco avrà un ID entità univoco, ad esempio `@Counter@Game1`, `@Counter@Game2` e così via. Per tutte le operazioni destinate a una determinata entità è necessario specificare un ID entità come parametro.

## <a name="programming-models"></a>Modelli di programmazione

Le entità durevoli supportano due diversi modelli di programmazione. Il primo modello è un modello "funzionale" dinamico in cui l'entità è definita da una singola funzione. Il secondo modello è un modello orientato a oggetti in cui l'entità è definita da una classe e da metodi. Questi modelli e i modelli di programmazione per l'interazione con le entità sono descritti nelle sezioni successive.

### <a name="defining-entities"></a>Definizione delle entità

Sono disponibili due modelli di programmazione facoltativi per la creazione di entità durevoli. Il codice seguente è un esempio di entità *Counter* semplice implementata come funzione standard. Questa funzione definisce tre *operazioni*, `add`, `reset` e `get`, ciascuna delle quali opera su un valore di stato integer, `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Questo modello funziona al meglio per le implementazioni di entità semplici o per le implementazioni con un set dinamico di operazioni. Tuttavia, è anche possibile usare un modello di programmazione basato su classi utile per le entità statiche ma con implementazioni più complesse. L'esempio seguente usa un'implementazione equivalente dell'entità `Counter` mediante classi e metodi.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> Il metodo del punto di ingresso della funzione con l'attributo `[FunctionName]` *deve* essere dichiarato come `static` quando si usano le classi di entità. I metodi del punto di ingresso non statici possono comportare l'inizializzazione di più oggetti e potenzialmente altri comportamenti non definiti.

Nel modello di programmazione basato su classi, l'oggetto `IDurableEntityContext` è disponibile nella proprietà statica `Entity.Current`.

Il modello basato su classi è simile al modello di programmazione diffuso da [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). In questo modello un tipo di entità è definito come una classe .NET. Ogni metodo della classe è un'operazione che può essere richiamata da un client esterno. Diversamente da Orleans, tuttavia, le interfacce .NET sono facoltative. Nell'esempio di entità *Counter* precedente non è stata usata un'interfaccia, ma è comunque possibile richiamarla tramite altre funzioni o tramite chiamate API HTTP.

> [!NOTE]
> Le funzioni trigger di entità sono disponibili in Durable Functions 2.0 e versioni successive. Attualmente le funzioni trigger di entità sono disponibili solo per le app per le funzioni .NET.

### <a name="accessing-entities-from-clients"></a>Accesso alle entità dai client

Le entità durevoli possono essere richiamate o sottoposte a query da funzioni comuni, note anche come *funzioni client*, mediante l'[associazione di output dei client di entità](durable-functions-bindings.md#entity-client). L'esempio seguente illustra una funzione attivata da una coda che *segnala* un'entità usando questa associazione.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

> [!NOTE]
> Le funzioni .NET supportano sia metodi con tipizzazione debole sia metodi indipendenti dai tipi per la segnalazione delle entità. Per informazioni dettagliate, vedere la documentazione di riferimento per l'[associazione client di entità](durable-functions-bindings.md#entity-client-usage).

Il termine *segnalazione* indica che la chiamata dell'API di entità è unidirezionale e asincrona. Non è possibile che una *funzione client* sappia quando l'entità ha elaborato l'operazione, né è possibile che una funzione di entità restituisca un valore a una funzione client. La messaggistica basata su coda unidirezionale è stata una scelta intenzionale di progettazione delle entità durevoli, per favorire la durabilità rispetto alle prestazioni. Questa scelta di progettazione è uno dei compromessi delle entità durevoli rispetto ad altre tecnologie analoghe. Attualmente solo le orchestrazioni sono in grado di gestire i valori restituiti dalle entità, come descritto nella sezione successiva.

Le funzioni client possono anche eseguire query sullo stato delle entità, come illustrato nell'esempio seguente:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

Le query sullo stato dell'entità vengono inviate all'archivio di rilevamento durevole e restituiscono lo stato *persistente* più recente dell'entità. Lo stato restituito potrebbe essere obsoleto rispetto allo stato in memoria dell'entità. Solo le orchestrazioni possono leggere lo stato in memoria di un'entità, come descritto nella sezione seguente.

### <a name="accessing-entities-from-orchestrations"></a>Accesso alle entità dalle orchestrazioni

Le funzioni dell'agente di orchestrazione possono accedere alle entità mediante API nell'[associazione del trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger). Le funzioni dell'agente di orchestrazione possono scegliere tra la comunicazione unidirezionale (detta anche "Fire and Forget" e *segnalazione*) e la comunicazione bidirezionale (richiesta e risposta, detta anche *chiamata*). L'esempio di codice seguente illustra una funzione dell'agente di orchestrazione che *chiama* e *segnala* un'entità *Counter*.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

Solo le orchestrazioni sono in grado di chiamare entità e ottenere una risposta, che può essere un valore restituito o un'eccezione. Le funzioni client che usano l'[associazione client](durable-functions-bindings.md#entity-client) possono solo *segnalare* le entità.

> [!NOTE]
> La chiamata di un'entità da una funzione dell'agente di orchestrazione è simile alla chiamata di una [funzione di attività](durable-functions-types-features-overview.md#activity-functions) da una funzione dell'agente di orchestrazione. La differenza principale consiste nel fatto che le funzioni di entità sono oggetti durevoli con un indirizzo (l'*ID entità*) e supportano la specifica di un nome di operazione. Le funzioni di attività, d'altra parte, sono senza stato e non hanno il concetto di operazioni.

### <a name="dependency-injection-in-entity-classes-net"></a>Inserimento di dipendenze nelle classi di entità (.NET)

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

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Diversamente da quando si usa l'inserimento del costruttore nelle normali funzioni di Azure per .NET, il metodo del punto di ingresso delle funzioni per le entità basate su classi *deve* essere dichiarato come `static`. La dichiarazione di un punto di ingresso della funzione non statico può causare conflitti tra l'inizializzatore di oggetto delle normali funzioni di Azure e l'inizializzatore di oggetto delle entità durevoli.

### <a name="bindings-in-entity-classes-net"></a>Associazioni nelle classi di entità (.NET)

Diversamente dalle funzioni normali, i metodi delle classi di entità non hanno accesso diretto alle associazioni di input e di output. Al contrario, i dati di associazione devono essere acquisiti nella dichiarazione della funzione del punto di ingresso e quindi passati al metodo `DispatchAsync<T>`. Qualsiasi oggetto passato a `DispatchAsync<T>` verrà passato automaticamente al costruttore della classe di entità come argomento.

L'esempio seguente illustra come è possibile rendere disponibile un riferimento a `CloudBlobContainer` dall'[associazione di input del BLOB](../functions-bindings-storage-blob.md#input) a un'entità basata su classe.

```csharp
public class BlobBackedEntity
{
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

## <a name="entity-coordination"></a>Coordinamento delle entità

In alcuni casi può essere necessario coordinare le operazioni tra più entità. In un'applicazione bancaria, ad esempio, possono essere presenti entità che rappresentano singoli conti bancari. Quando si trasferiscono fondi da un conto a un altro, è necessario verificare che il conto _di origine_ disponga di fondi sufficienti e che gli aggiornamenti ai conti sia _di origine_ che _di destinazione_ vengano eseguiti in modo coerente a livello di transazione.

### <a name="transfer-funds-example-in-c"></a>Esempio di trasferimento di fondi in C#

Il codice di esempio seguente trasferisce i fondi tra due entità _account_ usando una funzione dell'agente di orchestrazione. Per il coordinamento degli aggiornamenti delle entità è necessario usare il metodo `LockAsync` per creare una _sezione critica_ nell'orchestrazione:

> [!NOTE]
> Per semplicità, in questo esempio viene riutilizzata l'entità `Counter` definita in precedenza. Tuttavia, in un'applicazione reale, è preferibile definire invece un'entità `BankAccount` più dettagliata.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

In .NET, `LockAsync` restituisce un oggetto `IDisposable` che termina la sezione critica quando viene eliminato. Questo risultato `IDisposable` può essere usato insieme a un blocco `using` per ottenere una rappresentazione sintattica della sezione critica.

Nell'esempio precedente una funzione dell'agente di orchestrazione ha trasferito fondi da un'entità _di origine_ a un' entità _di destinazione_. Il metodo `LockAsync` ha bloccato le entità del conto sia _di origine_ che _di destinazione_. Questo blocco ha assicurato che nessun altro client potesse eseguire query o modificare lo stato dei due conti fino a quando la logica di orchestrazione non fosse uscita dalla _sezione critica_ alla fine dell'istruzione `using`. In questo modo è stata evitata la possibilità di lasciare scoperto il conto _di origine_.

### <a name="critical-section-behavior"></a>Comportamento della sezione critica

Il metodo `LockAsync` crea una _sezione critica_ in un'orchestrazione. Queste _sezioni critiche_ impediscono ad altre orchestrazioni di apportare modifiche in sovrapposizione a un set di entità specificato. Internamente, l'API `LockAsync` invia operazioni di "blocco" alle entità e restituisce un risultato quando riceve un messaggio di risposta di "blocco acquisito" da ognuna di queste stesse entità. Le operazioni di *blocco* e *sblocco* sono operazioni predefinite supportate da tutte le entità.

Non sono consentite operazioni da altri client su un'entità mentre è in stato di blocco. Questo comportamento garantisce che solo un'istanza di orchestrazione possa bloccare un'entità alla volta. Se un chiamante tenta di richiamare un'operazione su un'entità mentre è bloccata da un'orchestrazione, tale operazione viene inserita in una *coda di operazioni in sospeso*. Nessuna operazione in sospeso verrà elaborata finché l'orchestrazione che ha determinato il blocco non lo rilascia.

> [!NOTE] 
> Questo comportamento è leggermente diverso dalle primitive di sincronizzazione usate nella maggior parte dei linguaggi di programmazione, ad esempio l'istruzione `lock` in C#. In C#, ad esempio, l'istruzione `lock` deve essere usata da tutti i thread per garantire la corretta sincronizzazione tra più thread. Per le entità, tuttavia, non è necessario che tutti i chiamanti _blocchino_ in modo esplicito un'entità. Se un chiamante blocca un'entità, tutte le altre operazioni sull'entità vengono bloccate e accodate dietro il blocco.

I blocchi sulle entità sono durevoli, quindi vengono mantenuti anche se il processo in esecuzione viene riciclato. I blocchi vengono mantenuti internamente come parte dello stato durevole di un'entità.

### <a name="critical-section-restrictions"></a>Restrizioni relative alle sezioni critiche

L'uso delle sezioni critiche è sottoposto a diverse restrizioni. Queste restrizioni servono a impedire deadlock e rientranze.

* Le sezioni critiche non possono essere annidate.
* Le sezioni critiche non possono creare orchestrazioni secondarie.
* Le sezioni critiche possono chiamare solo le entità che hanno bloccato.
* Le sezioni critiche non possono chiamare la stessa entità usando più chiamate parallele.
* Le sezioni critiche possono segnalare solo le entità che non hanno bloccato.

## <a name="comparison-with-virtual-actors"></a>Confronto con gli attori virtuali

Molte delle funzionalità delle entità durevoli sono ispirate al [modello di attore](https://en.wikipedia.org/wiki/Actor_model). Se si ha già familiarità con gli attori, si riconosceranno molti dei concetti descritti in questo articolo. In particolare, le entità durevoli sono simili agli [attori virtuali](https://research.microsoft.com/projects/orleans/) per molti aspetti:

* Le entità durevoli sono indirizzabili tramite un *ID entità*.
* Le operazioni di entità durevoli vengono eseguite in modo seriale, una alla volta, per evitare race condition.
* Le entità durevoli vengono create automaticamente quando vengono chiamate o segnalate.
* Quando non si eseguono operazioni, le entità durevoli vengono scaricate automaticamente dalla memoria.

Esistono tuttavia alcune importanti differenze da tenere in considerazione:

* Le entità durevoli favoriscono la *durabilità* rispetto alla *latenza* e quindi potrebbero non essere appropriate per le applicazioni con requisiti di latenza rigidi.
* I messaggi inviati tra entità vengono recapitati in modo affidabile e nell'ordine corretto.
* Le entità durevoli possono essere usate insieme alle orchestrazioni durevoli e supportano i meccanismi di blocco distribuito.
* I modelli di richiesta/risposta nelle entità sono limitati alle orchestrazioni. Per la comunicazione *da client a entità* e *da entità a entità* è consentita solo la messaggistica unidirezionale (nota anche come "segnalazione"), come nel modello di attore originale. Questo comportamento impedisce i deadlock distribuiti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sugli hub attività](durable-functions-task-hubs.md)