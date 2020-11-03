---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284388"
---
Gli errori generati in funzioni di Azure possono provenire da una qualsiasi delle origini seguenti:

- Uso di [trigger e associazioni](..\articles\azure-functions\functions-triggers-bindings.md) di funzioni di Azure predefinite
- Chiamate alle API dei servizi di Azure sottostanti
- Chiamate agli endpoint REST
- Chiamate a librerie client, pacchetti o API di terze parti

Le procedure di gestione degli errori seguenti sono importanti per evitare la perdita di dati o messaggi mancanti. Le procedure consigliate per la gestione degli errori includono le azioni seguenti:

- [Abilita Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Usa la gestione degli errori strutturata](#use-structured-error-handling)
- [Progettazione per idempotenza](../articles/azure-functions/functions-idempotent.md)
- [Implementare i criteri di ripetizione dei tentativi](#retry-policies) (laddove appropriato)

### <a name="use-structured-error-handling"></a>Usa la gestione degli errori strutturata

L'acquisizione e la registrazione degli errori è fondamentale per il monitoraggio dell'integrità dell'applicazione. Il livello superiore di qualsiasi codice di funzione deve includere un blocco try/catch. Nel blocco catch è possibile acquisire e registrare gli errori.

## <a name="retry-policies"></a>Criteri di ripetizione dei tentativi

Un criterio di ripetizione dei tentativi può essere definito in qualsiasi funzione per qualsiasi tipo di trigger nell'app per le funzioni.  Il criterio di ripetizione dei tentativi esegue nuovamente una funzione fino a quando non viene eseguita correttamente o fino a quando non si verifica il numero massimo di tentativi.  I criteri di ripetizione dei tentativi possono essere definiti per tutte le funzioni in un'app o per le singole funzioni.  Per impostazione predefinita, un'app per le funzioni non ritenterà i messaggi (a parte i [trigger specifici che hanno un criterio di ripetizione nell'origine del trigger](#trigger-specific-retry-support)).  Un criterio di ripetizione viene valutato ogni volta che un'esecuzione genera un'eccezione non rilevata.  Come procedura consigliata, è consigliabile intercettare tutte le eccezioni nel codice e generare di nuovo gli eventuali errori che dovrebbero causare un nuovo tentativo.  Gli hub eventi e i checkpoint Azure Cosmos DB non verranno scritti fino al completamento del criterio di ripetizione dei tentativi per l'esecuzione, ovvero la progressione su tale partizione viene sospesa fino al completamento del batch corrente.

### <a name="retry-policy-options"></a>Opzioni dei criteri di ripetizione

Per la definizione di criteri di ripetizione sono disponibili le opzioni seguenti.

Il numero massimo di **tentativi** è il numero massimo di volte in cui un'esecuzione viene ritentata prima di un eventuale errore. Il valore `-1` indica di riprovare a tempo indefinito. Il numero di tentativi corrente viene archiviato in memoria dell'istanza. È possibile che si verifichi un errore di un'istanza tra tentativi.  Quando un'istanza ha esito negativo durante un criterio di ripetizione, il numero di tentativi viene perso.  Quando si verificano errori di istanza, i trigger come gli hub eventi, Azure Cosmos DB e l'archiviazione di Accodamento sono in grado di riprendere l'elaborazione e ritentare il batch in una nuova istanza, con il numero di tentativi reimpostato su zero.  Altri trigger, ad esempio HTTP e timer, non vengono ripresi in una nuova istanza.  Questo significa che il numero massimo di tentativi è un lavoro ottimale e, in alcuni casi rari, è possibile che un'esecuzione venga ritentata più del valore massimo o che per i trigger come HTTP e timer vengano ritentati in modo inferiore rispetto al valore massimo.

La **strategia di ripetizione dei tentativi** controlla il comportamento dei tentativi.  Di seguito sono riportate due opzioni di ripetizione dei tentativi supportate:

| Opzione | Descrizione|
|---|---|
|**`fixedDelay`**| Una quantità di tempo specificata può trascorrere tra un tentativo e l'altro.|
| **`exponentialBackoff`**| Il primo tentativo attende il ritardo minimo. Nei successivi tentativi, il tempo viene aggiunto in modo esponenziale alla durata iniziale per ogni ripetizione, fino a quando non viene raggiunto il ritardo massimo.  Il back-off esponenziale aggiunge una piccola sequenza casuale ai ritardi per scaglionare i tentativi negli scenari con velocità effettiva elevata.|

#### <a name="app-level-configuration"></a>Configurazione a livello di app

Un criterio di ripetizione dei tentativi può essere definito per tutte le funzioni in un'app [usando il `host.json` file](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Configurazione a livello di funzione

Un criterio di ripetizione dei tentativi può essere definito per una funzione specifica.  La configurazione specifica della funzione ha la priorità sulla configurazione a livello di app.

#### <a name="fixed-delay-retry"></a>Correzione di un nuovo tentativo di ritardo

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Ripetizione backoff esponenziale

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Ecco i criteri di ripetizione dei tentativi nel *function.js* file:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|Proprietà di function.json  |Proprietà Attribute | Descrizione |
|---------|---------|---------| 
|strategia|n/d|Obbligatorio. La strategia di ripetizione dei tentativi da usare. I valori validi sono `fixedDelay` o `exponentialBackoff`.|
|maxRetryCount|n/d|Obbligatorio. Numero massimo di tentativi consentiti per l'esecuzione di una funzione. `-1` indica di riprovare a tempo indefinito.|
|delayInterval|n/d|Ritardo che verrà usato tra i tentativi quando si usa la `fixedDelay` strategia.|
|minimumInterval|n/d|Ritardo minimo tra tentativi quando si usa la `exponentialBackoff` strategia.|
|maximumInterval|n/d|Ritardo massimo tra i tentativi quando si usa la `exponentialBackoff` strategia.| 

## <a name="trigger-specific-retry-support"></a>Supporto per tentativi specifici del trigger

Alcuni trigger forniscono nuovi tentativi nell'origine del trigger.  Questi tentativi di trigger possono essere usati in aggiunta o come sostituzione per i criteri di ripetizione dei tentativi host dell'app per le funzioni.  Se si desidera un numero fisso di tentativi, è necessario usare i criteri di ripetizione dei tentativi specifici del trigger rispetto ai criteri di ripetizione dei tentativi host generici.  I trigger seguenti supportano i tentativi nell'origine del trigger:

* [Archivio BLOB di Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Archiviazione code di Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Bus di servizio Azure (coda/argomento)](../articles/azure-functions/functions-bindings-service-bus.md)

Per impostazione predefinita, questi trigger ritentano le richieste fino a cinque volte. Dopo il quinto tentativo, sia l'archiviazione di Accodamento di Azure che il trigger del bus di servizio di Azure scrivono un messaggio in una [coda non elaborabile](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).
