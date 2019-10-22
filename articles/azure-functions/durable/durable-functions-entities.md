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
ms.openlocfilehash: e3a83730e47686e9d4757f057d2e8da4629fdd7a
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312130"
---
# <a name="entity-functions-preview"></a>Funzioni di entità (anteprima)

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccole parti di stato, note come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, il *trigger di entità*. A differenza delle funzioni di orchestrazione, le funzioni di entità gestiscono lo stato in modo esplicito anziché in modo implicito, rappresentandolo tramite flusso di controllo.
Le entità consentono di aumentare le istanze delle applicazioni distribuendo il lavoro tra molte entità, ognuna con uno stato di dimensioni moderate.

> [!NOTE]
> Le funzioni di entità e le funzionalità correlate sono disponibili solo in Durable Functions 2.0 e versioni successive. Le funzioni di entità sono attualmente disponibili in versione di anteprima pubblica.

## <a name="general-concepts"></a>Concetti generali

Il comportamento delle entità è simile a quello di servizi minuscoli che comunicano tramite messaggi. Ogni entità ha un'identità univoca e uno stato interno (se esistente). Analogamente ai servizi o agli oggetti, le entità eseguono operazioni quando viene richiesto. Quando viene eseguita, un'operazione può aggiornare lo stato interno dell'entità. Può anche chiamare servizi esterni e attendere la risposta. Le entità comunicano con altre entità, orchestrazioni e client usando messaggi che vengono inviati implicitamente tramite code affidabili. 

Per evitare conflitti, tutte le operazioni in una singola entità vengono eseguite in seriale, ossia una dopo l'altra. 

### <a name="entity-id"></a>ID entità
Le entità sono accessibili tramite un identificatore univoco, l'*ID entità*. Un ID entità è semplicemente una coppia di stringhe che identifica in modo univoco un'istanza di entità. È costituita da:

* Un **nome entità**, ossia un nome che identifica il tipo di entità (ad esempio, "Counter"). Questo nome deve corrispondere al nome della funzione che implementa l'entità. Non prevede la distinzione tra maiuscole e minuscole.
* Una **chiave di entità**, ossia una stringa che identifica in modo univoco l'entità tra tutte le altre entità con lo stesso nome (ad esempio un GUID).

È ad esempio possibile usare una funzione di entità *counter* per tenere il punteggio in un gioco online. Ogni istanza del gioco avrà un ID entità univoco, ad esempio `@Counter@Game1`, `@Counter@Game2` e così via. Per tutte le operazioni destinate a una determinata entità è necessario specificare un ID entità come parametro.

### <a name="entity-operations"></a>Operazioni delle entità ###

Per richiamare un'operazione in un'entità, specificare

* L'*ID entità* dell'entità di destinazione
* Il *nome dell'operazione*, ossia una stringa che specifica l'operazione da eseguire. Ad esempio, l'entità contatore potrebbe supportare operazioni di aggiunta, recupero o reimpostazione.
* L'*input dell'operazione*, ossia un parametro di input facoltativo per l'operazione. Ad esempio, l'operazione di aggiunta può accettare un importo intero come input.

Le operazioni possono restituire un valore di risultato oppure un risultato di errore, ad esempio un errore JavaScript o un'eccezione .NET. Il risultato o l'errore può essere osservato dalle orchestrazioni che hanno chiamato l'operazione.

Un'operazione di entità può anche creare, leggere, aggiornare ed eliminare lo stato dell'entità. Lo stato dell'entità è sempre persistente nell'archiviazione.

## <a name="defining-entities"></a>Definizione delle entità

Attualmente sono disponibili due API distinte per la definizione di entità.

Una **sintassi basata su funzione** in cui le entità sono rappresentate come funzioni e le operazioni vengono sempre inviate esplicitamente dall'applicazione. Questa sintassi è valida per le entità con uno stato semplice, poche operazioni o un set dinamico di operazioni, ad esempio i framework di applicazioni. Può tuttavia essere noiosa da mantenere, perché non rileva errori di tipo in fase di compilazione.

Una **sintassi basata su classe** in cui le entità e le operazioni sono rappresentate da classi e metodi. Questa sintassi produce codice più facilmente leggibile e consente di richiamare le operazioni in modo indipendente dai tipi. La sintassi basata su classe è un livello sottile sopra la sintassi basata su funzione, quindi entrambe le varianti possono essere usate in modo intercambiabile nella stessa applicazione.

### <a name="example-function-based-syntax"></a>Esempio: Sintassi basata su funzione

Il codice seguente è un esempio di semplice entità *Counter* implementata come funzione durevole. Questa funzione definisce tre operazioni, `add`, `reset` e `get`, ciascuna delle quali opera con uno stato integer.

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
    }
}
```

Per altre informazioni sulla sintassi basata su funzione e su come usarla, vedere [Sintassi basata su funzione](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax"></a>Esempio: Sintassi basata su classe

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

## <a name="accessing-entities"></a>Accesso alle entità

Per accedere alle entità, è possibile usare la comunicazione unidirezionale o bidirezionale. La terminologia seguente viene usata per distinguere due operazioni: 

* **Chiamare** un'entità significa usare la comunicazione bidirezionale: all'entità viene inviato un messaggio dell'operazione e quindi si attende il messaggio di risposta prima di continuare. Il messaggio di risposta può fornire un valore di risultato o un risultato di errore, ad esempio un errore JavaScript o un'eccezione .NET. Questo risultato o errore viene quindi osservato dal chiamante.
* **Segnalare** un'entità significa usare la comunicazione unidirezionale: viene inviato un messaggio dell'operazione, ma non si attende una risposta. Anche se è garantito che alla fine il messaggio venga recapitato, il mittente non sa quando e non può osservare i valori dei risultati o gli errori.

È possibile accedere alle entità all'interno di funzioni client, funzioni di orchestrazione o funzioni di entità. Non tutte le forme di comunicazione sono supportate da tutti i contesti:

* All'interno dei client è possibile *segnalare* le entità e *leggere* il relativo stato.
* All'interno delle orchestrazioni è possibile *segnalare* e *chiamare* le entità.
* All'interno delle entità è possibile *segnalare* le entità.

Di seguito sono riportati alcuni esempio che illustrano questi vari modi di accesso alle entità.

> [!NOTE]
> Per semplicità, gli esempi mostrano la sintassi con tipizzazione debole per l'accesso alle entità. In generale, è consigliabile [accedere alle entità tramite interfacce](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) perché in questo modo è disponibile un maggior controllo del tipo.

### <a name="example-client-signals-an-entity"></a>Esempio: il client segnala un'entità

Per accedere alle entità da una funzione di Azure ordinaria, ossia una *funzione client*, usare il [binding di output del client dell'entità](durable-functions-bindings.md#entity-client). L'esempio seguente illustra una funzione attivata da una coda che *segnala* un'entità usando questa associazione.

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

Il termine *segnalazione* indica che la chiamata dell'API di entità è unidirezionale e asincrona. Una *funzione client* non è in grado di riconoscere quando l'entità ha elaborato l'operazione. Inoltre, la funzione client non può osservare valori di risultati o eccezioni. 

### <a name="example-client-reads-an-entity-state"></a>Esempio: il client legge lo stato di un'entità

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

Le query sullo stato dell'entità vengono inviate all'archivio di rilevamento durevole e restituiscono lo stato *persistente* più recente dell'entità. Lo stato è sempre sottoposto a "commit", ossia non è mai uno stato intermedio temporaneo presupposto durante l'esecuzione di un'operazione. È tuttavia possibile che questo stato sia obsoleto rispetto allo stato in memoria dell'entità. Solo le orchestrazioni possono leggere lo stato in memoria di un'entità, come descritto nella sezione seguente.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Esempio: l'orchestrazione segnala e chiama un'entità

Le funzioni dell'agente di orchestrazione possono accedere alle entità mediante API nell'[associazione del trigger di orchestrazione](durable-functions-bindings.md#orchestration-trigger). L'esempio di codice seguente illustra una funzione dell'agente di orchestrazione che *chiama* e *segnala* un'entità *Counter*.

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

Solo le orchestrazioni sono in grado di chiamare entità e ottenere una risposta, che può essere un valore restituito o un'eccezione. Le funzioni client che usano l'[associazione client](durable-functions-bindings.md#entity-client) possono solo *segnalare* le entità.

> [!NOTE]
> La chiamata a un'entità da una funzione dell'agente di orchestrazione è simile alla chiamata a una [funzione di attività](durable-functions-types-features-overview.md#activity-functions) da una funzione dell'agente di orchestrazione. La differenza principale consiste nel fatto che le funzioni di entità sono oggetti durevoli con un indirizzo (l'*ID entità*) e supportano la specifica di un nome di operazione. Le funzioni di attività, d'altra parte, sono senza stato e non hanno il concetto di operazioni.

### <a name="example-entity-signals-an-entity"></a>Esempio: l'entità segnala un'entità

Una funzione di entità può inviare segnali ad altre entità (o anche a se stessa) durante l'esecuzione di un'operazione.
È ad esempio possibile modificare l'esempio di entità contatore precedente in modo da inviare un segnale "milestone-reached" a un'entità di monitoraggio quando il contatore raggiunge il valore 100:

```csharp
   case "add":
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }
        currentValue += amount;
        break;
```

Il frammento di codice seguente illustra come incorporare il servizio inserito nella classe di entità.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public async Task<int> GetAsync(string url)
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

### <a name="example-transfer-funds"></a>Esempio: Trasferire fondi

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

> [!NOTE] 
> Quando un'orchestrazione termina, normalmente o con un errore, le eventuali sezioni critiche in corso vengono terminate implicitamente e tutti i blocchi vengono rilasciati.

### <a name="critical-section-behavior"></a>Comportamento della sezione critica

Il metodo `LockAsync` crea una _sezione critica_ in un'orchestrazione. Queste _sezioni critiche_ impediscono ad altre orchestrazioni di apportare modifiche in sovrapposizione a un set di entità specificato. Internamente, l'API `LockAsync` invia operazioni di "blocco" alle entità e restituisce un risultato quando riceve un messaggio di risposta di "blocco acquisito" da ognuna di queste stesse entità. Le operazioni di *blocco* e *sblocco* sono operazioni predefinite supportate da tutte le entità.

Non sono consentite operazioni da altri client su un'entità mentre è in stato di blocco. Questo comportamento garantisce che solo un'istanza di orchestrazione possa bloccare un'entità alla volta. Se un chiamante tenta di richiamare un'operazione su un'entità mentre è bloccata da un'orchestrazione, tale operazione viene inserita in una *coda di operazioni in sospeso*. Nessuna operazione in sospeso verrà elaborata finché l'orchestrazione che ha determinato il blocco non lo rilascia.

> [!NOTE] 
> Questo comportamento è leggermente diverso dalle primitive di sincronizzazione usate nella maggior parte dei linguaggi di programmazione, ad esempio l'istruzione `lock` in C#. In C#, ad esempio, l'istruzione `lock` deve essere usata da tutti i thread per garantire la corretta sincronizzazione tra più thread. Per le entità, tuttavia, non è necessario che tutti i chiamanti _blocchino_ in modo esplicito un'entità. Se un chiamante blocca un'entità, tutte le altre operazioni sull'entità vengono bloccate e accodate dietro il blocco.

I blocchi sulle entità sono durevoli, quindi vengono mantenuti anche se il processo in esecuzione viene riciclato. I blocchi vengono mantenuti internamente come parte dello stato durevole di un'entità.

A differenza delle transazioni, le sezioni critiche non eseguono automaticamente il rollback delle modifiche in caso di errori. Al contrario, qualsiasi tipo di gestione degli errori (rollback, ripetizione o altro) deve essere inserito esplicitamente nel codice, ad esempio rilevando errori o eccezioni. Questa scelta di progettazione è intenzionale. Il rollback automatico di tutti gli effetti di un'orchestrazione è in generale difficile o impossibile da implementare, perché le orchestrazioni possono eseguire attività ed effettuare chiamate a servizi esterni che non possono essere annullate. Inoltre, gli stessi tentativi di rollback possono non riuscire e richiedere un'ulteriore gestione degli errori.

### <a name="critical-section-rules"></a>Regole per le sezioni critiche

A differenza delle primitive di blocco di basso livello della maggior parte dei linguaggi di programmazione, le sezioni critiche sono **garantite contro i deadlock**. Per evitare deadlock vengono applicate le restrizioni seguenti: 

* Le sezioni critiche non possono essere annidate.
* Le sezioni critiche non possono creare orchestrazioni secondarie.
* Le sezioni critiche possono chiamare solo le entità che hanno bloccato.
* Le sezioni critiche non possono chiamare la stessa entità usando più chiamate parallele.
* Le sezioni critiche possono segnalare solo le entità che non hanno bloccato.

Qualsiasi violazione di queste regole genera un errore di runtime, ad esempio`LockingRulesViolationException` in .NET, che include un messaggio con l'indicazione della regola violata.

## <a name="comparison-with-virtual-actors"></a>Confronto con gli attori virtuali

Molte delle funzionalità delle entità durevoli sono ispirate al [modello di attore](https://en.wikipedia.org/wiki/Actor_model). Se si ha già familiarità con gli attori, si riconosceranno molti dei concetti descritti in questo articolo. Le entità durevoli sono particolarmente simili agli [attori virtuali](https://research.microsoft.com/projects/orleans/) o *granelli*, resi popolari dal [progetto Orleans](http://dotnet.github.io/orleans/). Ad esempio:

* Le entità durevoli sono indirizzabili tramite un *ID entità*.
* Le operazioni di entità durevoli vengono eseguite in modo seriale, una alla volta, per evitare race condition.
* Le entità durevoli vengono create implicitamente quando vengono chiamate o segnalate.
* Quando non si eseguono operazioni, le entità durevoli vengono scaricate automaticamente dalla memoria.

Esistono tuttavia alcune importanti differenze da tenere in considerazione:

* Le entità durevoli favoriscono la *durabilità* rispetto alla *latenza* e quindi potrebbero non essere appropriate per le applicazioni con requisiti di latenza rigidi.
* Le entità durevoli non includono timeout predefiniti per i messaggi. In Orleans il timeout di tutti i messaggi si verifica dopo un periodo di tempo configurabile, che per impostazione predefinita è di 30 secondi.
* I messaggi inviati tra entità vengono recapitati in modo affidabile e nell'ordine corretto. In Orleans il recapito affidabile o ordinato è supportato per il contenuto inviato tramite flussi, ma non è garantito per tutti i messaggi tra granelli.
* I modelli di richiesta/risposta nelle entità sono limitati alle orchestrazioni. All'interno delle entità sono consentiti solo messaggi unidirezionali ("segnalazione") come nel modello di attore originale, a differenza dei granelli di Orleans. 
* Le entità durevoli non sono soggette a deadlock. In Orleans si possono verificare deadlock, che si risolvono solo dopo il timeout dei messaggi.
* Le entità durevoli possono essere usate insieme alle orchestrazioni durevoli e supportano i meccanismi di blocco distribuito. 


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Leggere la guida dello sviluppatore alle entità durevoli in .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Informazioni sugli hub attività](durable-functions-task-hubs.md)
