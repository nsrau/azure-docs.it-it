---
title: Funzionalità di Durable Functions Preview-funzioni di Azure
description: Informazioni sulle funzionalità di anteprima per Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 94784ee9a72d11e168828f1543cbe62219ac4a74
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097948"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (funzioni di Azure)

*Funzioni permanenti* è un'estensione di [Funzioni di Azure](../functions-overview.md) e [Processi Web di Azure](../../app-service/web-sites-create-web-jobs.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. Se non si ha già familiarità con Durable Functions, vedere la [documentazione di panoramica](durable-functions-overview.md).

Durable Functions 1. x è una funzionalità GA (disponibile a livello generale) di funzioni di Azure, ma contiene anche alcune sottofunzionalità attualmente disponibili in anteprima pubblica. In questo articolo vengono descritte le funzionalità di anteprima appena rilasciate e vengono fornite informazioni dettagliate sul funzionamento e sul modo in cui è possibile iniziare a utilizzarle.

> [!NOTE]
> Queste funzionalità di anteprima fanno parte di una versione di Durable Functions 2,0, che attualmente è una **versione di anteprima** con diverse modifiche di rilievo. Le compilazioni di pacchetti di estensione durevoli di funzioni di Azure sono reperibili in nuget.org con versioni nel formato **2.0.0-betaX**. Queste compilazioni non sono destinate ai carichi di lavoro di produzione e le versioni successive possono contenere ulteriori modifiche di rilievo.

## <a name="breaking-changes"></a>Modifiche che causano un'interruzione

In Durable Functions 2,0 sono state introdotte diverse modifiche di rilievo. Non è previsto che le applicazioni esistenti siano compatibili con Durable Functions 2,0 senza modifiche al codice. In questa sezione sono elencate alcune delle modifiche:

### <a name="hostjson-schema"></a>Schema host. JSON

Il frammento di codice seguente mostra il nuovo schema per host. JSON. Le principali modifiche da tenere presenti sono le nuove sottosezioni:

* `"storageProvider"`(e la `"azureStorage"` sottosezione) per la configurazione specifica dell'archiviazione
* `"tracking"`per la configurazione di rilevamento e registrazione
* `"notifications"`(e la `"eventGrid"` sottosezione) per la configurazione delle notifiche di griglia di eventi

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Quando durable Functions 2,0 continua a stabilizzarsi, verranno introdotte ulteriori modifiche alla `durableTask` sezione host. JSON. Per ulteriori informazioni su queste modifiche, vedere [questo problema di GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Modifiche all'interfaccia pubblica

I vari oggetti "context" supportati da Durable Functions hanno classi di base astratte destinate all'uso nel testing unità. Come parte di Durable Functions 2,0, queste classi di base astratte sono state sostituite con le interfacce. Il codice di funzione che usa direttamente i tipi concreti non è interessato.

La tabella seguente rappresenta le modifiche principali:

| Tipo precedente | Nuovo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Nel caso in cui una classe base astratta contenesse metodi virtuali, questi metodi virtuali sono stati sostituiti dai metodi `DurableContextExtensions`di estensione definiti in.

## <a name="entity-functions"></a>Funzioni di entità

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccoli elementi di stato, noti come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, *trigger di entità*. Diversamente dalle funzioni dell'agente di orchestrazione, le funzioni di entità non hanno vincoli di codice specifici. Anche le funzioni di entità gestiscono lo stato in modo esplicito anziché rappresentare in modo implicito lo stato tramite flusso di controllo.

### <a name="net-programing-models"></a>Modelli di programmazione .NET

Sono disponibili due modelli di programmazione facoltativi per la creazione di entità durevoli. Il codice seguente è un esempio di un'entità *contatore* semplice implementata come funzione standard. Questa funzione definisce tre *operazioni*, `add` `reset`, e `get`, ciascuna delle quali opera su un valore di stato Integer, `currentValue`.

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

Questo modello funziona meglio per le implementazioni di entità semplici o per le implementazioni con un set dinamico di operazioni. Tuttavia, è disponibile anche un modello di programmazione basato su classi che risulta utile per le entità statiche ma con implementazioni più complesse. L'esempio seguente è un'implementazione equivalente dell'entità `Counter` con le classi e i metodi .NET.

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

Il modello basato su classi è simile al modello di programmazione diffuso da [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). In questo modello, un tipo di entità è definito come una classe .NET. Ogni metodo della classe è un'operazione che può essere richiamata da un client esterno. Diversamente da Orleans, tuttavia, le interfacce .NET sono facoltative. Nell'esempio di *contatore* precedente non è stata usata un'interfaccia, ma è comunque possibile richiamarla tramite altre funzioni o tramite chiamate API HTTP.

È possibile accedere alle *istanze* di entità tramite un identificatore univoco, ovvero l' *ID entità*. Un ID entità è semplicemente una coppia di stringhe che identifica in modo univoco un'istanza di entità. È costituita da:

* **Nome entità**: nome che identifica il tipo di entità (ad esempio, "contatore").
* **Chiave di entità**: stringa che identifica in modo univoco l'entità tra tutte le altre entità con lo stesso nome, ad esempio un GUID.

È ad esempio possibile usare una funzione di entità *contatore* per mantenere il punteggio in un gioco online. Ogni istanza del gioco avrà un ID entità univoco, ad esempio `@Counter@Game1`, `@Counter@Game2`e così via.

### <a name="comparison-with-virtual-actors"></a>Confronto con gli attori virtuali

La progettazione di entità durevoli è fortemente influenzata dal [modello Actor](https://en.wikipedia.org/wiki/Actor_model). Se si ha già familiarità con gli attori, i concetti alla base delle entità durevoli dovrebbero essere noti. In particolare, le entità durevoli sono simili agli [attori virtuali](https://research.microsoft.com/projects/orleans/) in molti modi:

* Le entità durevoli sono indirizzabili tramite un *ID entità*.
* Le operazioni di entità durevoli vengono eseguite in modo seriale, una alla volta, per evitare race condition.
* Le entità durevoli vengono create automaticamente quando vengono chiamate o segnalate.
* Quando non si eseguono operazioni, le entità durevoli vengono scaricate automaticamente dalla memoria.

Tuttavia, esistono alcune importanti differenze, vale a notare:

* Le entità durevoli assegnano priorità alla *durabilità* rispetto alla *latenza*e quindi potrebbero non essere appropriate per le applicazioni con requisiti di latenza rigidi.
* I messaggi inviati tra le entità vengono recapitati in modo affidabile e in ordine.
* Le entità durevoli possono essere usate in combinazione con le orchestrazioni durevoli e possono fungere da blocchi distribuiti, descritte più avanti in questo articolo.
* I modelli di richiesta/risposta nelle entità sono limitati alle orchestrazioni. Per la comunicazione da entità a entità, sono consentiti solo messaggi unidirezionali (noti anche come "segnalazione"), come nel modello Actor originale. Questo comportamento impedisce i deadlock distribuiti.

### <a name="durable-entity-net-apis"></a>API di Entity .NET durevoli

Il supporto per le entità implica diverse API. Per uno, esiste una nuova API per la definizione delle funzioni dell'entità, come illustrato in precedenza, che specifica cosa deve verificarsi quando viene richiamata un'operazione su un'entità. Inoltre, le API esistenti per i client e le orchestrazioni sono state aggiornate con nuove funzionalità per l'interazione con le entità.

#### <a name="implementing-entity-operations"></a>Implementazione di operazioni sulle entità

L'esecuzione di un'operazione su un'entità può chiamare questi membri nell'oggetto Context (`IDurableEntityContext` in .NET):

* **OperationName**: Ottiene il nome dell'operazione.
* **GetInput\<TInput >** : Ottiene l'input per l'operazione.
* **GetState\<tstate >** : ottiene lo stato corrente dell'entità.
* **Sestate**: aggiorna lo stato dell'entità.
* **SignalEntity**: Invia un messaggio unidirezionale a un'entità.
* **Self**: Ottiene l'ID dell'entità.
* **Return**: restituisce un valore per il client o l'orchestrazione che ha chiamato l'operazione.
* **IsNewlyConstructed**: restituisce `true` se l'entità non esisteva prima dell'operazione.
* **DestructOnExit**: Elimina l'entità dopo aver completato l'operazione.

Le operazioni sono meno limitate delle orchestrazioni:

* Le operazioni possono chiamare I/O esterno, usando le API sincrone o asincrone (si consiglia di usare solo I tipi asincroni).
* Le operazioni possono essere non deterministiche. Ad esempio, è possibile chiamare `DateTime.UtcNow` `Guid.NewGuid()` in modo sicuro o `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Accesso alle entità dai client

Le entità durevoli possono essere richiamate da `orchestrationClient` funzioni comuni`IDurableOrchestrationClient` tramite l'associazione (in .NET). Sono supportati i metodi seguenti:

* **ReadEntityStateAsync\<T >** : legge lo stato di un'entità.
* **SignalEntityAsync**: Invia un messaggio unidirezionale a un'entità e ne attende l'accodamento.
* **SignalEntityAsync\<T >** : uguale a `SignalEntityAsync` , ma usa un oggetto proxy generato di `T`tipo.

Per la `SignalEntityAsync` chiamata precedente è necessario specificare il nome dell'operazione dell'entità `string` come e il payload `object`dell'operazione come. Il codice di esempio seguente è un esempio di questo modello:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

È anche possibile generare un oggetto proxy per l'accesso indipendente dai tipi. Per generare un proxy indipendente dai tipi, il tipo di entità deve implementare un'interfaccia. Si supponga, ad esempio `Counter` , che l'entità citata in precedenza abbia implementato un' `ICounter` interfaccia, definita come segue:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Il codice client può quindi `SignalEntityAsync<T>` utilizzare e specificare `ICounter` l'interfaccia come parametro di tipo per generare un proxy indipendente dai tipi. Questo utilizzo di proxy indipendenti dai tipi viene illustrato nell'esempio di codice seguente:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

Nell'esempio precedente, `proxy` il parametro è un'istanza generata dinamicamente di `ICounter`, che converte internamente la chiamata a `Add` nella chiamata equivalente (non tipizzata) a `SignalEntityAsync`.

Il parametro di tipo `SignalEntityAsync<T>` per presenta le restrizioni seguenti:

* Il parametro di tipo deve essere un'interfaccia.
* Nell'interfaccia è possibile definire solo metodi. Proprietà non supportate.
* Ogni metodo deve definire uno o nessun parametro.
* Ogni metodo deve `void`restituire, `Task`o `Task<T>` dove `T` è un tipo serializzabile in JSON.
* L'interfaccia deve essere implementata esattamente da un tipo all'interno dell'assembly dell'interfaccia.

Nella maggior parte dei casi, le interfacce che non soddisfano questi requisiti genereranno un'eccezione in fase di esecuzione.

> [!NOTE]
> È importante notare che i metodi `ReadEntityStateAsync` `IDurableOrchestrationClient` e `SignalEntityAsync` per definire la priorità delle prestazioni rispetto alla coerenza. `ReadEntityStateAsync`può restituire un valore non aggiornato e `SignalEntityAsync` può restituire prima del completamento dell'operazione.

#### <a name="accessing-entities-from-orchestrations"></a>Accesso alle entità dalle orchestrazioni

Le orchestrazioni possono accedere alle entità `IDurableOrchestrationContext` utilizzando l'oggetto. Possono scegliere tra la comunicazione unidirezionale (Fire and Forget) e la comunicazione bidirezionale (richiesta e risposta). I rispettivi metodi sono:

* **SignalEntity**: Invia un messaggio unidirezionale a un'entità.
* **CallEntityAsync**: Invia un messaggio a un'entità e attende una risposta che indica che l'operazione è stata completata.
* **CallEntityAsync\<T >** : Invia un messaggio a un'entità e attende una risposta contenente un risultato di tipo t.

Quando si utilizza la comunicazione bidirezionale, le eccezioni generate durante l'esecuzione dell'operazione vengono trasmesse anche all'orchestrazione chiamante e rigenerate. Quando invece si utilizza Fire-and-Forget, le eccezioni non vengono osservate.

Per l'accesso indipendente dai tipi, le funzioni di orchestrazione possono generare proxy basati su un'interfaccia. Il `CreateEntityProxy` metodo di estensione può essere usato a questo scopo:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

Nell'esempio precedente è stata presunta l'esistenza di un'entità "Counter" che implementa `IAsyncCounter` l'interfaccia. L'orchestrazione ha quindi potuto usare la `IAsyncCounter` definizione del tipo per generare un tipo di proxy per interagire in modo sincrono con l'entità.

### <a name="locking-entities-from-orchestrations"></a>Blocco di entità dalle orchestrazioni

Le orchestrazioni possono bloccare le entità. Questa funzionalità offre un modo semplice per evitare le competizioni indesiderate usando *sezioni critiche*.

L'oggetto context fornisce i metodi seguenti:

* **LockAsync**: acquisisce blocchi in una o più entità.
* **Locked**: restituisce true se attualmente si trova in una sezione critica, false in caso contrario.

La sezione critica termina e vengono rilasciati tutti i blocchi, al termine dell'orchestrazione. In .NET `LockAsync` restituisce un oggetto `IDisposable` che termina la sezione critica quando viene eliminato, che può essere usato insieme a una `using` clausola per ottenere una rappresentazione sintattica della sezione critica.

Si consideri, ad esempio, un'orchestrazione che deve verificare se due giocatori sono disponibili e quindi assegnarli entrambi a un gioco. Questa attività può essere implementata usando una sezione critica come indicato di seguito:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

All'interno della sezione critica, entrambe le entità Player sono bloccate, il che significa che non eseguono operazioni diverse da quelle chiamate dall'interno della sezione critica. Questo comportamento impedisce le gare con operazioni in conflitto, ad esempio i giocatori assegnati a un altro gioco o la firma.

Vengono imposte diverse restrizioni relative al modo in cui è possibile usare le sezioni critiche. Queste restrizioni servono a impedire deadlock e rientranza.

* Non è possibile nidificare le sezioni critiche.
* Le sezioni critiche non possono creare sottoorchestrazioni.
* Le sezioni critiche possono chiamare solo le entità bloccate.
* Le sezioni critiche non possono chiamare la stessa entità utilizzando più chiamate parallele.
* Le sezioni critiche possono segnalare solo le entità non bloccate.

## <a name="alternate-storage-providers"></a>Provider di archiviazione alternativi

Il Framework di attività permanenti supporta attualmente più provider di archiviazione, tra cui [archiviazione di Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), bus di [servizio di Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), un [emulatore in memoria](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e un provider di [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) sperimentale. Tuttavia, fino ad ora, l'estensione delle attività durevoli per funzioni di Azure supporta solo il provider di archiviazione di Azure. A partire da Durable Functions 2,0, è in corso l'aggiunta del supporto per provider di archiviazione alternativi, a partire dal provider Redis.

> [!NOTE]
> Durable Functions 2,0 supporta solo provider compatibili con .NET Standard 2,0. Al momento della stesura del documento, il provider del bus di servizio di Azure non supporta .NET Standard 2,0 e pertanto non è disponibile come provider di archiviazione alternativo.

### <a name="emulator"></a>Emulatore

Il provider [DurableTask. Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) è una memoria locale e un provider di archiviazione non durevole adatto per scenari di test locali. Può essere configurata con lo schema **host. JSON** minimo seguente:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (sperimentale)

Il provider [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) rende permanente tutto lo stato dell'orchestrazione a un cluster Redis configurato.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` Deve fare riferimento al nome di un'impostazione dell'app o di una variabile di ambiente. L'impostazione dell'app o la variabile di ambiente deve contenere un valore della stringa di connessione Redis nel formato *Server: porta*. Ad esempio, `localhost:6379` per la connessione a un cluster Redis locale.

> [!NOTE]
> Il provider Redis è attualmente sperimentale e supporta solo le app per le funzioni in esecuzione su un singolo nodo. Non è garantito che il provider Redis venga mai reso disponibile a livello generale e potrebbe essere rimosso in una versione futura.
