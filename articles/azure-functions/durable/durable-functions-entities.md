---
title: Entità durevoli - Funzioni di Azure
description: Questo articolo contiene informazioni sulle entità durevoli e sul modo di usarle nell'estensione Durable Functions per Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d62281b4ccb522b3a784428bcf0284730f120628
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904034"
---
# <a name="entity-functions"></a>Funzioni di entità

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccole parti di stato, note come *entità durevoli*. In modo analogo alle funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, ovvero il *trigger di entità*. A differenza delle funzioni di orchestrazione, le funzioni di entità gestiscono lo stato in modo esplicito anziché in modo implicito, rappresentandolo tramite flusso di controllo.
Le entità consentono di aumentare le istanze delle applicazioni distribuendo il lavoro tra molte entità, ognuna con uno stato di dimensioni moderate.

> [!NOTE]
> Le funzioni di entità e le funzionalità correlate sono disponibili solo in Durable Functions 2.0 e versioni successive.

## <a name="general-concepts"></a>Concetti generali

Il comportamento delle entità è simile a quello di servizi minuscoli che comunicano tramite messaggi. Ogni entità ha un'identità univoca e uno stato interno (se esistente). Analogamente ai servizi o agli oggetti, le entità eseguono operazioni quando viene richiesto. Quando un'operazione viene eseguita, potrebbe aggiornare lo stato interno dell'entità, nonché chiamare servizi esterni e attendere la risposta. Le entità comunicano con altre entità, orchestrazioni e client usando messaggi che vengono inviati implicitamente tramite code affidabili. 

Per evitare conflitti, tutte le operazioni in una singola entità vengono eseguite in seriale, ossia una dopo l'altra. 

### <a name="entity-id"></a>ID entità
Le entità sono accessibili tramite un identificatore univoco, l'*ID entità*. Un ID entità è semplicemente una coppia di stringhe che identifica in modo univoco un'istanza di entità. È costituita dagli elementi seguenti:

* **Nome entità**, ovvero un nome che identifica il tipo dell'entità. Un esempio è "Counter". Questo nome deve corrispondere al nome della funzione che implementa l'entità. Non prevede la distinzione tra maiuscole e minuscole.
* **Chiave di entità**, ovvero una stringa che identifica in modo univoco l'entità tra tutte le altre con lo stesso nome. Un esempio è un identificatore GUID.

È possibile usare ad esempio una funzione di entità `Counter` per tenere il punteggio in un gioco online. Ogni istanza del gioco ha un ID entità univoco, ad esempio `@Counter@Game1` e `@Counter@Game2`. Per tutte le operazioni destinate a una determinata entità è necessario specificare un ID entità come parametro.

### <a name="entity-operations"></a>Operazioni delle entità ###

Per richiamare un'operazione su un'entità, specificare gli elementi seguenti:

* **ID entità** dell'entità di destinazione.
* **Nome dell'operazione**, ovvero una stringa che specifica l'operazione da eseguire. L'entità `Counter`, ad esempio, potrebbe supportare le operazioni `add`, `get` o `reset`.
* **Input dell'operazione**, ovvero un parametro di input facoltativo per l'operazione. L'operazione di addizione, ad esempio, può accettare come input un valore intero.

Le operazioni possono restituire un valore di risultato oppure un risultato di errore, ad esempio un errore JavaScript o un'eccezione .NET. Il risultato o l'errore può essere osservato dalle orchestrazioni che hanno chiamato l'operazione.

Un'operazione di entità può anche creare, leggere, aggiornare ed eliminare lo stato dell'entità. Lo stato dell'entità è sempre persistente nell'archiviazione.

## <a name="define-entities"></a>Definire le entità

Le due API distinte per la definizione di entità sono attualmente le seguenti:

**Sintassi basata su funzione** in cui le entità sono rappresentate come funzioni e le operazioni vengono sempre inviate esplicitamente dall'applicazione. Questa sintassi è valida per le entità con uno stato semplice, poche operazioni o un set dinamico di operazioni, ad esempio i framework di applicazioni. Questa sintassi può essere noiosa da gestire perché non rileva gli errori di tipo in fase di compilazione.

**Sintassi basata su classe** in cui le entità e le operazioni sono rappresentate da classi e metodi. Questa sintassi produce codice più facilmente leggibile e consente di richiamare le operazioni in modo indipendente dai tipi. La sintassi basata su classe è un livello sottile sopra la sintassi basata su funzione e quindi entrambe le varianti possono essere usate in modo intercambiabile nella stessa applicazione.

### <a name="example-function-based-syntax---c"></a>Esempio: Sintassi basata su funzioni - C#

Il codice seguente è un esempio di una semplice entità `Counter` implementata come funzione durevole. Tale funzione definisce tre operazioni, ovvero `add`, `reset` e `get`, ognuna delle quali opera con uno stato intero.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Per altre informazioni sulla sintassi basata su funzione e su come usarla, vedere [Sintassi basata su funzione](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Esempio: Sintassi basata su classi - C#

L'esempio seguente usa un'implementazione equivalente dell'entità `Counter` mediante classi e metodi.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Lo stato di questa entità è un oggetto di tipo `Counter`, che contiene un campo in cui è archiviato il valore corrente del contatore. Per renderlo persistente nell'archiviazione, questo oggetto viene serializzato e deserializzato dalla libreria [Json.NET](https://www.newtonsoft.com/json). 

Per altre informazioni sulla sintassi basata su classe e su come usarla, vedere [Definizione di classi di entità](durable-functions-dotnet-entities.md#defining-entity-classes).

### <a name="example-javascript-entity"></a>Esempio: Entità JavaScript

Le entità durevoli sono disponibili in JavaScript a partire dalla versione **1.3.0** del pacchetto npm `durable-functions`. Il codice seguente è l'entità `Counter` implementata come funzione durevole scritta in JavaScript.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

## <a name="access-entities"></a>Accedere alle entità

Per accedere alle entità, è possibile usare la comunicazione unidirezionale o bidirezionale. La terminologia seguente distingue le due forme di comunicazione: 

* La **chiamata** a un'entità usa una comunicazione bidirezionale (round trip). Viene inviato un messaggio dell'operazione all'entità e quindi si attende il messaggio di risposta prima di continuare. Il messaggio di risposta può restituire un valore di risultato o un risultato di errore, ad esempio un errore JavaScript o un'eccezione .NET. Questo risultato o errore viene quindi osservato dal chiamante.
* La **segnalazione** di un'entità usa una comunicazione unidirezionale (Fire and Forget). Viene inviato un messaggio dell'operazione, ma non si attende una risposta. Anche se il recapito del messaggio è garantito, il mittente non sa quando e non può osservare i valori dei risultati o gli errori.

È possibile accedere alle entità all'interno di funzioni client, funzioni di orchestrazione o funzioni di entità. Non tutte le forme di comunicazione sono supportate da tutti i contesti:

* Nei client è possibile segnalare le entità e leggerne lo stato.
* Nelle orchestrazioni è possibile segnalare le entità e chiamarle.
* Nelle entità è possibile segnalare le entità.

Gli esempi seguenti illustrano le diverse modalità di accesso alle entità.

> [!NOTE]
> Per semplicità, negli esempi seguenti viene illustrata la sintassi a tipizzazione debole per l'accesso alle entità. In generale, è consigliabile [accedere alle entità tramite interfacce](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) per un maggiore controllo del tipo.

### <a name="example-client-signals-an-entity"></a>Esempio: il client segnala un'entità

Per accedere alle entità da una funzione di Azure ordinaria, ovvero una funzione client, usare il [binding di output del client dell'entità](durable-functions-bindings.md#entity-client). L'esempio seguente illustra una funzione attivata da una coda che segnala un'entità usando questa associazione.

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

Il termine *segnalazione* indica che la chiamata dell'API di entità è unidirezionale e asincrona. Una funzione client non è in grado di riconoscere il momento in cui l'entità ha elaborato l'operazione. Inoltre, la funzione client non può osservare valori di risultati o eccezioni. 

### <a name="example-client-reads-an-entity-state"></a>Esempio: il client legge uno stato dell'entità

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    return context.df.readEntityState(entityId);
};
```

Le query sullo stato dell'entità vengono inviate all'archivio di rilevamento durevole e restituiscono lo stato persistente più recente dell'entità. Lo stato è sempre sottoposto a "commit", ossia non è mai uno stato intermedio temporaneo presupposto durante l'esecuzione di un'operazione. È tuttavia possibile che questo stato sia obsoleto rispetto allo stato in memoria dell'entità. Solo le orchestrazioni possono leggere lo stato in memoria di un'entità, come descritto nella sezione seguente.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Esempio: l'orchestrazione segnala e chiama un'entità

Le funzioni dell'agente di orchestrazione possono accedere alle entità mediante API nel [binding del trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger). L'esempio di codice seguente illustra una funzione dell'agente di orchestrazione che chiama e segnala un'entità `Counter`.

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
    if (currentValue < 10) {
        // One-way signal to the entity which updates the value - does not await a response
        yield context.df.signalEntity(entityId, "add", 1);
    }
});
```

Solo le orchestrazioni sono in grado di chiamare entità e ottenere una risposta, che può essere un valore restituito o un'eccezione. Le funzioni client che usano il [binding client](durable-functions-bindings.md#entity-client) possono solo segnalare entità.

> [!NOTE]
> La chiamata a un'entità da una funzione dell'agente di orchestrazione è simile alla chiamata a una [funzione di attività](durable-functions-types-features-overview.md#activity-functions) da una funzione dell'agente di orchestrazione. La differenza principale consiste nel fatto che le funzioni di entità sono oggetti durevoli con un indirizzo, ovvero l'ID entità. Le funzioni di entità supportano la specifica del nome di un'operazione. Le funzioni di attività sono invece senza stato e sono prive del concetto di operazioni.

### <a name="example-entity-signals-an-entity"></a>Esempio: l'entità segnala un'entità

Una funzione di entità può inviare segnali ad altre entità (o anche a se stessa) durante l'esecuzione di un'operazione.
È ad esempio possibile modificare l'esempio di entità `Counter` precedente in modo da inviare un segnale "milestone-reached" a un'entità di monitoraggio quando il contatore raggiunge il valore 100.

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

## <a name="entity-coordination"></a>Coordinamento delle entità

In alcuni casi può essere necessario coordinare le operazioni tra più entità. In un'applicazione bancaria, ad esempio, potrebbero essere presenti entità che rappresentano singoli conti bancari. Quando si trasferiscono i fondi da un conto a un altro, è necessario verificare che il conto di origine disponga di fondi sufficienti. È anche necessario verificare che gli aggiornamenti per i conti di origine e di destinazione vengano eseguiti in modo coerente a livello di transazione.

### <a name="example-transfer-funds-c"></a>Esempio: trasferire fondi (C#)

Il codice di esempio seguente trasferisce i fondi tra due entità account usando una funzione dell'agente di orchestrazione. Per il coordinamento degli aggiornamenti delle entità è necessario usare il metodo `LockAsync` per creare una _sezione critica_ nell'orchestrazione.

> [!NOTE]
> Per semplicità, in questo esempio viene riutilizzata l'entità `Counter` definita in precedenza. In un'applicazione reale, è preferibile definire un'entità `BankAccount` più dettagliata.

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

In .NET `LockAsync` restituisce un oggetto `IDisposable` che termina la sezione critica quando viene eliminato. Questo risultato `IDisposable` può essere usato insieme a un blocco `using` per ottenere una rappresentazione sintattica della sezione critica.

Nell'esempio precedente una funzione dell'agente di orchestrazione ha trasferito fondi da un'entità di origine a un'entità di destinazione. Il metodo `LockAsync` ha bloccato le entità account sia di origine sia di destinazione. Questo blocco ha assicurato che nessun altro client potesse eseguire query o modificare lo stato dei due conti fino a quando la logica di orchestrazione non fosse uscita dalla sezione critica alla fine dell'istruzione `using`. Questo comportamento impedisce la possibilità di sovrascrivere dal conto di origine.

> [!NOTE] 
> Quando un'orchestrazione termina, normalmente o con un errore, le eventuali sezioni critiche in corso vengono terminate implicitamente e tutti i blocchi vengono rilasciati.

### <a name="critical-section-behavior"></a>Comportamento della sezione critica

Il metodo `LockAsync` crea una sezione critica in un'orchestrazione. Tali sezioni critiche impediscono ad altre orchestrazioni di apportare modifiche in sovrapposizione a un set di entità specificato. Internamente, l'API `LockAsync` invia operazioni di "blocco" alle entità e restituisce un risultato quando riceve un messaggio di risposta di "blocco acquisito" da ognuna di queste stesse entità. Le operazioni di blocco e sblocco sono operazioni predefinite supportate da tutte le entità.

Su un'entità in stato di blocco non sono consentite operazioni da parte di altri client. Questo comportamento garantisce che solo un'istanza di orchestrazione possa bloccare un'entità alla volta. Se un chiamante tenta di richiamare un'operazione su un'entità mentre è bloccata da un'orchestrazione, tale operazione viene inserita in una coda di operazioni in sospeso. Nessuna operazione in sospeso viene elaborata finché l'orchestrazione che ha determinato il blocco non lo rilascia.

> [!NOTE] 
> Questo comportamento è leggermente diverso dalle primitive di sincronizzazione usate nella maggior parte dei linguaggi di programmazione, ad esempio l'istruzione `lock` in C#. In C# l'istruzione `lock` deve essere usata, ad esempio, da tutti i thread per garantire la corretta sincronizzazione tra più thread. Nel caso delle entità, tuttavia, non è necessario che tutti i chiamanti blocchino in modo esplicito un'entità. Se un chiamante blocca un'entità, tutte le altre operazioni sull'entità vengono bloccate e accodate dietro il blocco.

I blocchi sulle entità sono durevoli, quindi vengono mantenuti anche se il processo in esecuzione viene riciclato. I blocchi vengono mantenuti internamente come parte dello stato durevole di un'entità.

A differenza delle transazioni, le sezioni critiche non eseguono automaticamente il rollback delle modifiche in caso di errori. Al contrario, qualsiasi gestione degli errori, ad esempio il rollback o la ripetizione dei tentativi, deve essere codificata in modo esplicito, ad esempio rilevando errori oppure eccezioni. Questa scelta di progettazione è intenzionale. Il rollback automatico di tutti gli effetti di un'orchestrazione è in generale difficile o impossibile da implementare, perché le orchestrazioni potrebbero eseguire attività ed effettuare chiamate a servizi esterni che non possono essere annullate. Gli stessi tentativi di rollback possono anche non riuscire e richiedere un'ulteriore gestione degli errori.

### <a name="critical-section-rules"></a>Regole per le sezioni critiche

A differenza delle primitive di blocco di basso livello della maggior parte dei linguaggi di programmazione, le sezioni critiche sono *garantite contro i deadlock*. Per evitare deadlock vengono applicate le restrizioni seguenti: 

* Le sezioni critiche non possono essere annidate.
* Le sezioni critiche non possono creare orchestrazioni secondarie.
* Le sezioni critiche possono chiamare solo le entità che hanno bloccato.
* Le sezioni critiche non possono chiamare la stessa entità usando più chiamate parallele.
* Le sezioni critiche possono segnalare solo le entità che non hanno bloccato.

Qualsiasi violazione di queste regole genera un errore di runtime, ad esempio `LockingRulesViolationException` in .NET, che include un messaggio con l'indicazione della regola violata.

## <a name="comparison-with-virtual-actors"></a>Confronto con gli attori virtuali

Molte funzionalità delle entità durevoli sono ispirate al [modello di attore](https://en.wikipedia.org/wiki/Actor_model). Se si ha già familiarità con gli attori, è possibile riconoscere molti concetti descritti in questo articolo. Le entità durevoli sono particolarmente simili agli [attori virtuali](https://research.microsoft.com/projects/orleans/), o granelli, resi popolari dal [progetto Orleans](http://dotnet.github.io/orleans/). Ad esempio:

* Le entità durevoli sono indirizzabili tramite un ID entità.
* Le operazioni di entità durevoli vengono eseguite in modo seriale, una alla volta, per evitare race condition.
* Le entità durevoli vengono create implicitamente quando vengono chiamate o segnalate.
* Quando non si eseguono operazioni, le entità durevoli vengono scaricate automaticamente dalla memoria.

Esistono alcune importanti differenze da tenere in considerazione:

* Le entità durevoli favoriscono la durabilità rispetto alla latenza e quindi potrebbero non essere appropriate per le applicazioni con requisiti di latenza rigidi.
* Le entità durevoli non includono timeout predefiniti per i messaggi. In Orleans tutti i messaggi raggiungono il timeout dopo un tempo configurabile. Il valore predefinito è 30 secondi.
* I messaggi inviati tra entità vengono recapitati in modo affidabile e nell'ordine corretto. In Orleans il recapito affidabile o ordinato è supportato per il contenuto inviato tramite flussi, ma non è garantito per tutti i messaggi tra granelli.
* I modelli di richiesta/risposta nelle entità sono limitati alle orchestrazioni. All'interno delle entità sono consentiti solo messaggi unidirezionali ("segnalazione") come nel modello di attore originale, a differenza dei granelli di Orleans. 
* Per le entità durevoli non si verifica alcun deadlock. In Orleans si possono verificare deadlock, che si risolvono solo dopo il timeout dei messaggi.
* Le entità durevoli possono essere usate insieme alle orchestrazioni durevoli e supportano i meccanismi di blocco distribuito. 


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Leggere la guida dello sviluppatore alle entità durevoli in .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Informazioni sugli hub attività](durable-functions-task-hubs.md)
