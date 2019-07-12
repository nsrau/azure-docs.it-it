---
title: Funzionalità - funzioni di Azure in funzioni permanenti anteprima
description: Informazioni sulle funzionalità di anteprima per funzioni permanenti.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 7101519aa4a87995dac3a7f11046eed84a2c09b6
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812770"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Anteprima della versione 2.0 di funzioni permanente (funzioni di Azure)

*Funzioni permanenti* è un'estensione di [Funzioni di Azure](../functions-overview.md) e [Processi Web di Azure](../../app-service/web-sites-create-web-jobs.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. Se non ha già familiarità con funzioni permanenti, vedere la [documentazione con la panoramica](durable-functions-overview.md).

Funzioni permanenti è una funzionalità di disponibilità generale (disponibile a livello generale) di funzioni di Azure 1.x, ma contiene anche diverse funzionalità secondarie che sono attualmente in anteprima pubblica. Questo articolo descrive le funzionalità di anteprima rilasciata di recente e passa i dettagli su come funzionano e come è possibile iniziare a utilizzarle.

> [!NOTE]
> Queste funzionalità di anteprima sono parte di una versione 2.0 di funzioni permanenti, che è attualmente una **qualità versione di anteprima** con molte modifiche di rilievo. Azure funzioni durevoli compila pacchetto di estensione è reperibile in nuget.org con le versioni costituiti **2.0.0-betaX**. Tali build non sono pensate per i carichi di lavoro di produzione e versioni successive possono contenere modifiche di rilievo aggiuntive.

## <a name="breaking-changes"></a>Modifiche di rilievo

2\.0 di funzioni permanenti sono introdotte diverse modifiche di rilievo. Le applicazioni esistenti non possono essere compatibili con 2.0 di funzioni permanenti senza modifiche al codice. In questa sezione sono elencate alcune delle modifiche:

### <a name="hostjson-schema"></a>Schema di host. JSON

Il frammento seguente mostra il nuovo schema per l'host. JSON. Le modifiche principali da tenere presenti sono le nuove sottosezioni:

* `"storageProvider"` (e `"azureStorage"` sottosezione) per la configurazione di specifici dell'archiviazione
* `"tracking"` per il rilevamento e la configurazione della registrazione
* `"notifications"` (e `"eventGrid"` sottosezione) per la configurazione di notifica della griglia di eventi

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

2\.0 di funzioni durevoli continua di stabilizzarsi e altre modifiche verranno introdotti i `durableTask` sezione host. JSON. Per altre informazioni su queste modifiche, vedere [questo problema su GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Modifiche all'interfaccia pubblica

Gli oggetti "contesto" vari supportati da funzioni permanenti hanno classi di base astratte destinate all'uso in di unit test. Come parte di 2.0 di funzioni durevoli, queste classi base astratte sono state sostituite con le interfacce. Codice della funzione che usa direttamente i tipi concreti non sono interessate.

La tabella seguente descrive le modifiche principali:

| Tipo precedente | Nuovo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Nel caso in cui una classe base astratta inclusi metodi virtuali, questi metodi virtuali sono stati sostituiti dai metodi di estensione definiti in `DurableContextExtensions`.

## <a name="entity-functions"></a>Funzioni dell'entità

Definiscono le operazioni per la lettura e aggiornamento piccole parti di stato, noto come funzioni Entity *entità permanente*. Ad esempio le funzioni di orchestrazione entità sono funzioni con un tipo speciale di trigger, *trigger entità*. A differenza delle funzioni dell'agente di orchestrazione, le funzioni entità non hanno vincoli qualsiasi codice specifico. Funzioni dell'entità anche gestire lo stato in modo esplicito anziché in modo implicito che rappresenta lo stato tramite il flusso di controllo.

### <a name="net-programing-models"></a>Modelli di programmazione .NET

Esistono due modelli di programmazione facoltativi per la creazione di entità permanente. Il codice seguente è riportato un esempio di una semplice *contatore* entità implementata come una funzione standard. Questa funzione definisce tre *operations*, `add`, `reset`, e `get`, ognuno dei che operano su un valore di stato, numero intero `currentValue`.

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

Questo modello è adatto per implementazioni semplici entità o implementazioni che includono un set dinamico di operazioni. Tuttavia, è anche un modello di programmazione basata su classe che è utile per le entità che sono statici, ma hanno implementazioni più complesse. L'esempio seguente è un'implementazione dell'equivalente di `Counter` entità usando metodi e classi .NET.

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

Il modello basato su classi è simile al modello di programmazione, rese famose da [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). In questo modello, un tipo di entità è definito come una classe .NET. Ogni metodo della classe è un'operazione che può essere richiamata da un client esterno. A differenza di Orleans, Mostra, tuttavia, le interfacce di .NET sono facoltative. Il precedente *contatore* esempio non è stata utilizzata un'interfaccia, ma possono comunque essere richiamato tramite altre funzioni o le chiamate API HTTP.

Entity *istanze* sono accessibili tramite un identificatore univoco, il *ID entità*. Un ID di entità è semplicemente una coppia di stringhe che identifica in modo univoco un'istanza di entità. È costituita da:

* Un' **nome dell'entità**: un nome che identifica il tipo di entità (ad esempio, "Contatore").
* Un' **chiave di entità**: una stringa che identifica in modo univoco l'entità tra tutte le altre entità con lo stesso nome (ad esempio, un GUID).

Ad esempio, un *contatore* funzione entità può essere usato per tenere il punteggio in un gioco online. Ogni istanza del gioco avrà un ID di entità univoca, ad esempio `@Counter@Game1`, `@Counter@Game2`e così via.

### <a name="comparison-with-virtual-actors"></a>Confronto con gli attori virtuali

La progettazione delle entità durevole dipende in larga misura per il [modello actor](https://en.wikipedia.org/wiki/Actor_model). Se si ha già familiarità con gli attori, i concetti relativi all'entità permanente devono essere familiari. In particolare, sono simili a entità permanente [actors virtuale](https://research.microsoft.com/projects/orleans/) in molti modi:

* Le entità permanenti sono indirizzabili tramite un *ID entità*.
* Entità durevole operazioni vengono eseguite in modo seriale, uno alla volta, per evitare race condition.
* Entità durevoli vengono create automaticamente quando vengono chiamati o segnalati.
* Quando non si esegue operazioni, le entità permanenti sono automaticamente scaricate dalla memoria.

Esistono alcune importanti differenze, tuttavia, che è importante sottolineare:

* Definire le priorità entità permanente *durabilità* failover *latenza*e quindi potrebbero non essere appropriato per le applicazioni con requisiti di latenza strict.
* I messaggi inviati tra le entità vengano recapitati in modo affidabile e in ordine.
* Entità durevoli può essere usata in combinazione con le orchestrazioni durevoli e possono fungere da blocchi distribuiti, che sono descritte più avanti in questo articolo.
* I modelli di richiesta/risposta in entità sono limitati alle orchestrazioni. Per la comunicazione di entità ed entità, sono consentiti solo i messaggi unidirezionali (noto anche come "segnalazione"), come nel modello actor originale. Questo comportamento impedisce deadlock distribuito.

### <a name="durable-entity-net-apis"></a>Entità durevole API .NET

Supporto Entity comporta diverse API. Per uno, è disponibile una nuova API per la definizione delle funzioni dell'entità, come illustrato in precedenza, che specificano l'azione da intraprendere quando viene richiamata un'operazione su un'entità. Inoltre, le API esistenti per i client e le orchestrazioni sono state aggiornate con nuove funzionalità per l'interazione con entità.

#### <a name="implementing-entity-operations"></a>Implementazione delle operazioni di entità

L'esecuzione di un'operazione su un'entità può chiamare questi membri dell'oggetto di contesto (`IDurableEntityContext` in .NET):

* **OperationName**: Ottiene il nome dell'operazione.
* **GetInput\<TInput >** : Ottiene l'input per l'operazione.
* **GetState\<TState >** : Ottiene lo stato corrente dell'entità.
* **SetState**: aggiorna lo stato dell'entità.
* **SignalEntity**: invia un messaggio unidirezionale a un'entità.
* **Self**: Ottiene l'ID dell'entità.
* **Restituire**: restituisce un valore al client o l'orchestrazione che ha chiamato l'operazione.
* **IsNewlyConstructed**: restituisce `true` se l'entità non esiste prima dell'operazione.
* **DestructOnExit**: Elimina l'entità dopo aver completato l'operazione.

Le operazioni sono meno restrizioni rispetto a orchestrazioni:

* Operazioni possono chiamare i/o esterno, usando le API sincrone o asincrone (è consigliabile usare solo quelli asincroni).
* Operazioni possono essere non deterministico. Ad esempio, è possibile chiamare `DateTime.UtcNow`, `Guid.NewGuid()` o `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Accede alle entità dai client

Entità permanente può essere richiamata da funzioni ordinarie tramite il `orchestrationClient` binding (`IDurableOrchestrationClient` in .NET). Sono supportati i metodi seguenti:

* **ReadEntityStateAsync\<T >** : legge lo stato di un'entità.
* **SignalEntityAsync**: invia un messaggio unidirezionale a un'entità e attende il suo da accodare.
* **SignalEntityAsync\<T >** : uguale allo `SignalEntityAsync` ma usa un oggetto proxy generato di tipo `T`.

Il precedente `SignalEntityAsync` chiamata è necessario specificare il nome dell'operazione di entità come una `string` e il payload dell'operazione come una `object`. Esempio di codice seguente è riportato un esempio di questo modello:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

È anche possibile generare un oggetto proxy per l'accesso indipendente dai tipi. Per generare un proxy indipendente dai tipi, il tipo di entità deve implementare un'interfaccia. Ad esempio, si supponga che il `Counter` entità indicato in precedenza implementato un `ICounter` interfaccia, definito come segue:

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

Il codice client può quindi utilizzare `SignalEntityAsync<T>` e specificare il `ICounter` interfaccia come parametro di tipo per generare un proxy indipendente dai tipi. Questo utilizzo dei proxy è indipendente dai tipi è illustrato nell'esempio di codice seguente:

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

Nell'esempio precedente, il `proxy` parametro è un'istanza generata in modo dinamico dei `ICounter`, che converte internamente la chiamata a `Add` nell'equivalente (non tipizzato) le chiamate a `SignalEntityAsync`.

> [!NOTE]
> È importante notare che il `ReadEntityStateAsync` e `SignalEntityAsync` metodi `IDurableOrchestrationClient` stabilire le priorità delle prestazioni tramite la coerenza. `ReadEntityStateAsync` può restituire un valore non aggiornato, e `SignalEntityAsync` può restituire prima del completamento dell'operazione.

#### <a name="accessing-entities-from-orchestrations"></a>Accede alle entità dalle orchestrazioni

Le orchestrazioni possono accedere le entità utilizzando la `IDurableOrchestrationContext` oggetto. È possibile scegliere tra una comunicazione unidirezionale (generato automaticamente) e comunicazioni bidirezionali (richiesta e risposta). I rispettivi metodi sono:

* **SignalEntity**: invia un messaggio unidirezionale a un'entità.
* **CallEntityAsync**: invia un messaggio a un'entità e attende una risposta che indica che l'operazione è stata completata.
* **CallEntityAsync\<T >** : invia un messaggio a un'entità e attende una risposta che contiene un risultato di tipo T.

Quando si usa la comunicazione bidirezionale, tutte le eccezioni generate durante l'esecuzione dell'operazione vengono anche trasmessi nuovamente all'orchestrazione chiama e generata di nuovo. Al contrario, quando si usa fire-and-forget, le eccezioni non vengono rispettate.

Per l'accesso indipendente dai tipi, funzioni di orchestrazione possono generare i proxy basati su un'interfaccia. Il `CreateEntityProxy` metodo di estensione può essere utilizzato per questo scopo:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration)]
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

Nell'esempio precedente, un'entità "counter" è stato si presuppone che esista che implementa il `IAsyncCounter` interfaccia. L'orchestrazione è stata quindi in grado di utilizzare il `IAsyncCounter` definizione per generare un tipo di proxy per interagire in modo sincrono con l'entità del tipo.

### <a name="locking-entities-from-orchestrations"></a>Blocco entità dalle orchestrazioni

Le orchestrazioni possono bloccare le entità. Questa funzionalità fornisce un modo semplice per evitare le competizioni indesiderate usando *sezioni critiche*.

L'oggetto contesto offre i metodi seguenti:

* **LockAsync**: acquisisce i blocchi su una o più entità.
* **IsLocked**: restituisce true se attualmente è in una sezione critica, false in caso contrario.

La sezione critica end e tutti i blocchi vengono rilasciati, quando l'orchestrazione termina. In .NET `LockAsync` restituisce un `IDisposable` che termina la sezione critica quando eliminato, che può essere usata insieme a un `using` clausola per ottenere una rappresentazione sintattica della sezione critica.

Ad esempio, prendere in considerazione un'orchestrazione che deve verificare se sono disponibili due giocatori e quindi assegnarle entrambi a un gioco. Questa attività può essere implementata tramite una sezione critica, come indicato di seguito:

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

All'interno della sezione critica, entrambe le entità di Windows Media player sono bloccate, ovvero che non devono eseguire operazioni diverse da quelle che vengono chiamati dall'interno della sezione critica). Questo comportamento impedisce le competizioni con operazioni in conflitto, ad esempio lettori da assegnare a un altro giochi, o per la firma è disattivata.

Sono imposti diverse restrizioni sulle sezioni critiche come può essere utilizzato. Tali restrizioni consentono di impedire i deadlock e reentrancy.

* Le sezioni critiche non possono essere annidate.
* Le sezioni critiche non è possibile creare suborchestrations.
* Le sezioni critiche possono chiamare solo le entità che hanno bloccato.
* Le sezioni critiche non è possibile chiamare la stessa entità usando più chiamate parallele.
* Le sezioni critiche possono segnalare solo le entità che non hanno bloccato.

## <a name="alternate-storage-providers"></a>Provider di archiviazione alternativo

Durable Task Framework supporta più provider di archiviazione oggi, tra cui [archiviazione di Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Bus di servizio di Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), una [emulatore in memoria](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e un esperimento [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) provider. Tuttavia, fino ad ora, l'estensione di attività permanenti per funzioni di Azure supportato solo il provider di archiviazione di Azure. Partire dalla versione 2.0 di funzioni permanenti, il supporto per i provider di archiviazione alternativo viene aggiunto, inizia con il provider di Redis.

> [!NOTE]
> Funzioni durevoli 2.0 supporta solo i provider di .NET Standard 2.0 compatibili. Al momento della scrittura, il provider di Bus di servizio di Azure non supporta .NET Standard 2.0 e pertanto non è disponibile come provider di archiviazione alternativo.

### <a name="emulator"></a>Emulatore

Il [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) provider è una memoria locale, il provider di archiviazione non durevole adatto per scenari di test in locali. Può essere configurato utilizzando la seguente query minima **host. JSON** dello schema:

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

Il [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) provider mantiene tutti gli stati di orchestrazione a un cluster Redis configurato.

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

Il `connectionStringName` deve fare riferimento al nome di una variabile di ambiente o impostazione di app. Tale variabile di ambiente o impostazione di app deve contenere un valore di stringa di connessione Redis costituiti *: porta*. Ad esempio, `localhost:6379` per la connessione a un cluster Redis locale.

> [!NOTE]
> Il provider di Redis è attualmente in fase sperimentale e supporta solo App per le funzioni in esecuzione in un singolo nodo. Non è garantito che il provider di Redis verrà mai resa disponibile a livello generale e potrebbe venire rimosso in una versione futura.
