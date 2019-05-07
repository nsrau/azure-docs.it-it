---
title: Funzionalità - funzioni di Azure in funzioni permanenti anteprima
description: Informazioni sulle funzionalità di anteprima per funzioni permanenti.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 6b3b49049ea1ed36a08fad9619183017b0f07d99
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077741"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Anteprima della versione 2.0 di funzioni permanente (funzioni di Azure)

*Funzioni permanenti* è un'estensione di [Funzioni di Azure](../functions-overview.md) e [Processi Web di Azure](../../app-service/web-sites-create-web-jobs.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. Se non ha già familiarità con funzioni permanenti, vedere la [documentazione con la panoramica](durable-functions-overview.md).

Funzioni permanenti è una funzionalità di disponibilità generale (disponibile a livello generale) di funzioni di Azure, ma contiene anche diverse funzionalità secondarie che sono attualmente in anteprima pubblica. Questo articolo descrive le funzionalità di anteprima rilasciata di recente e passa i dettagli su come funzionano e come è possibile iniziare a utilizzarle.

> [!NOTE]
> Queste funzionalità di anteprima sono parte di una versione 2.0 di funzioni permanenti, che è attualmente un' **versione di qualità alfa** con molte modifiche di rilievo. Azure funzioni durevoli compila pacchetto di estensione è reperibile in nuget.org con le versioni costituiti **2.0.0-alpha**. Tali build non sono adatte per i carichi di lavoro di produzione e versioni successive possono contenere modifiche di rilievo aggiuntive.

## <a name="breaking-changes"></a>Modifiche di rilievo

2.0 di funzioni permanenti sono introdotte diverse modifiche di rilievo. Le applicazioni esistenti non possono essere compatibili con 2.0 di funzioni permanenti senza modifiche al codice. In questa sezione sono elencate alcune delle modifiche:

### <a name="dropping-net-framework-support"></a>Eliminazione di supporto di .NET Framework

Supporto per .NET Framework (e pertanto le funzioni 1.0) è stato eliminato per 2.0 di funzioni permanenti. Il motivo principale è consentire ai collaboratori non Windows di facile compilare e testare le modifiche apportate alle funzioni durevoli da piattaforme macOS e Linux. Il secondo motivo è per incoraggiare gli sviluppatori per spostarne la versione più recente del runtime di funzioni di Azure.

### <a name="hostjson-schema"></a>Schema di host. JSON

Il frammento seguente mostra il nuovo schema per l'host. JSON. La modifica principale da tenere in considerazione di noi il nuovo `"storageProvider"` sezione e `"azureStorage"` sezione sottostanti. Questa modifica è stata eseguita per supportare [provider di archiviazione alternativo](durable-functions-preview.md#alternate-storage-providers).

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
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

2.0 di funzioni durevoli continua di stabilizzarsi e altre modifiche verranno introdotti i `durableTask` sezione host. JSON. Per altre informazioni su queste modifiche, vedere [questo problema su GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

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

Il codice seguente è riportato un esempio di una funzione semplice entità che definisce una *contatore* entità. La funzione definisce tre operazioni, `add`, `remove`, e `reset`, ognuno dei quali aggiornare un valore intero, `currentValue`.

```csharp
public static async Task Counter(
    [EntityTrigger(EntityName = "Counter")] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Entity *istanze* sono accessibili tramite un identificatore univoco, il *ID entità*. Un ID di entità è semplicemente una coppia di stringhe che identifica in modo univoco un'istanza di entità. È costituita da:

1. un' **nome dell'entità**: un nome che identifica il tipo di entità (ad esempio, "Contatore")
2. un' **chiave di entità**: una stringa che identifica in modo univoco l'entità tra tutte le altre entità con lo stesso nome (ad esempio, un GUID)

Ad esempio, un *contatore* funzione entità può essere usato per tenere il punteggio in un gioco online. Ogni istanza del gioco avrà un ID di entità univoca, ad esempio `@Counter@Game1`, `@Counter@Game2`e così via.

### <a name="comparison-with-virtual-actors"></a>Confronto con gli attori virtuali

La progettazione delle entità durevole dipende in larga misura per il [modello actor](https://en.wikipedia.org/wiki/Actor_model). Se si ha già familiarità con gli attori, i concetti relativi all'entità permanente devono essere familiari. In particolare, sono simili a entità permanente [actors virtuale](https://research.microsoft.com/en-us/projects/orleans/) in molti modi:

* Le entità permanenti sono indirizzabili tramite un *ID entità*.
* Entità durevole operazioni vengono eseguite in modo seriale, uno alla volta, per evitare race condition.
* Entità durevoli vengono create automaticamente quando vengono chiamati o segnalati.
* Quando non si esegue operazioni, le entità permanenti sono automaticamente scaricate dalla memoria.

Esistono alcune importanti differenze, tuttavia, che è importante sottolineare:

* Entità permanente vengono modellate come funzioni pure. Questa progettazione è diversa dalla maggior parte dei framework orientata agli oggetti che rappresentano attori utilizzando il supporto specifico del linguaggio per le classi, proprietà e metodi.
* Definire le priorità entità permanente *durabilità* failover *latenza*e quindi potrebbero non essere appropriato per le applicazioni con requisiti di latenza strict.
* I messaggi inviati tra le entità vengano recapitati in modo affidabile e in ordine.
* Entità durevoli può essere usata in combinazione con le orchestrazioni durevoli e possono fungere da blocchi distribuiti, che sono descritte più avanti in questo articolo.
* I modelli di richiesta/risposta in entità sono limitati alle orchestrazioni. Per la comunicazione di entità ed entità, sono consentiti solo i messaggi unidirezionali (noto anche come "segnalazione"), come nel modello actor originale. Questo comportamento impedisce deadlock distribuito.

### <a name="durable-entity-apis"></a>API Entity durevole

Supporto Entity comporta diverse API. Per uno, è disponibile una nuova API per la definizione delle funzioni dell'entità, come illustrato in precedenza, che specificano l'azione da intraprendere quando viene richiamata un'operazione su un'entità. Inoltre, le API esistenti per i client e le orchestrazioni sono state aggiornate con nuove funzionalità per l'interazione con entità.

### <a name="implementing-entity-operations"></a>Implementazione delle operazioni di entità

L'esecuzione di un'operazione su un'entità può chiamare questi membri dell'oggetto di contesto (`IDurableEntityContext` in .NET):

* **OperationName**: Ottiene il nome dell'operazione.
* **GetInput\<T >**: Ottiene l'input per l'operazione.
* **GetState\<T >**: Ottiene lo stato corrente dell'entità.
* **SetState**: aggiorna lo stato dell'entità.
* **SignalEntity**: invia un messaggio unidirezionale a un'entità.
* **Self**: Ottiene l'ID dell'entità.
* **Restituire**: restituisce un valore al client o l'orchestrazione che ha chiamato l'operazione.
* **IsNewlyConstructed**: restituisce `true` se l'entità non esiste prima dell'operazione.
* **DestructOnExit**: Elimina l'entità dopo aver completato l'operazione.

Le operazioni sono meno restrizioni rispetto a orchestrazioni:

* Operazioni possono chiamare i/o esterno, usando le API sincrone o asincrone (è consigliabile usare solo quelli asincroni).
* Operazioni possono essere non deterministico. Ad esempio, è possibile chiamare `DateTime.UtcNow`, `Guid.NewGuid()` o `new Random()`.

### <a name="accessing-entities-from-clients"></a>Accede alle entità dai client

Entità permanente può essere richiamata da funzioni ordinarie tramite il `orchestrationClient` binding (`IDurableOrchestrationClient` in .NET). Sono supportati i metodi seguenti:

* **ReadEntityStateAsync\<T >**: legge lo stato di un'entità.
* **SignalEntityAsync**: invia un messaggio unidirezionale a un'entità e attende il suo da accodare.

Questi metodi di definire la priorità delle prestazioni sul coerenza: `ReadEntityStateAsync` può restituire un valore non aggiornato, e `SignalEntityAsync` può restituire prima del completamento dell'operazione. Al contrario, la chiamata delle entità dalle orchestrazioni (come descritto di seguito) è con coerenza assoluta.

### <a name="accessing-entities-from-orchestrations"></a>Accede alle entità dalle orchestrazioni

Le orchestrazioni possono accedere le entità usando l'oggetto di contesto. È possibile scegliere tra una comunicazione unidirezionale (generato automaticamente) e comunicazioni bidirezionali (richiesta e risposta). I rispettivi metodi sono

* **SignalEntity**: invia un messaggio unidirezionale a un'entità.
* **CallEntityAsync**: invia un messaggio a un'entità e attende una risposta che indica che l'operazione è stata completata.
* **CallEntityAsync\<T >**: invia un messaggio a un'entità e attende una risposta che contiene un risultato di tipo T.

Quando si usa la comunicazione bidirezionale, tutte le eccezioni generate durante l'esecuzione dell'operazione vengono anche trasmessi nuovamente all'orchestrazione chiama e generata di nuovo. Al contrario, quando si usa fire-and-forget, le eccezioni non vengono rispettate.

### <a name="locking-entities-from-orchestrations"></a>Blocco entità dalle orchestrazioni

Le orchestrazioni possono bloccare le entità. Questa funzionalità fornisce un modo semplice per evitare le competizioni indesiderate usando *sezioni critiche*.

L'oggetto contesto offre i metodi seguenti:

* **LockAsync**: acquisisce i blocchi su una o più entità.
* **IsLocked**: restituisce true se attualmente è in una sezione critica, false in caso contrario.

La sezione critica end e tutti i blocchi vengono rilasciati, quando l'orchestrazione termina. In .NET `LockAsync` restituisce un `IDisposable` che termina la sezione critica quando eliminato, che può essere usata insieme a un `using` clausola per ottenere una rappresentazione sintattica della sezione critica.

Ad esempio, prendere in considerazione un'orchestrazione che deve verificare se sono disponibili due giocatori e quindi assegnarle entrambi a un gioco. Questa attività può essere implementata tramite una sezione critica, come indicato di seguito:

```csharp

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
> Il provider di Redis è attualmente in fase sperimentale e supporta solo App per le funzioni in esecuzione in un singolo nodo.