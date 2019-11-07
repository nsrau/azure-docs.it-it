---
title: API HTTP in Durable Functions-funzioni di Azure
description: Informazioni su come implementare API HTTP nell'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: azfuncdf
ms.openlocfilehash: ad2b1b9236f88f99542f8705372af664cc299ee0
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614815"
---
# <a name="http-api-reference"></a>Informazioni di riferimento sulle API HTTP

L'estensione Durable Functions espone un set di API HTTP predefinite che possono essere utilizzate per eseguire attività di gestione su [orchestrazioni](durable-functions-types-features-overview.md#orchestrator-functions), [entità](durable-functions-types-features-overview.md#entity-functions)e [Hub attività](durable-functions-task-hubs.md). Queste API HTTP sono webhook di estendibilità autorizzati dall'host di funzioni di Azure, ma gestiti direttamente dall'estensione Durable Functions.

Per tutte le API HTTP implementate dall'estensione sono necessari i parametri seguenti. Il tipo di dati di tutti i parametri è `string`.

| Parametro        | Tipo di parametro  | Descrizione |
|------------------|-----------------|-------------|
| **`taskHub`**    | Stringa di query    | Nome dell'[hub attività](durable-functions-task-hubs.md). Se non specificato, viene usato il nome dell'hub attività dell'app per le funzioni corrente. |
| **`connection`** | Stringa di query    | **Nome** della stringa di connessione per l'account di archiviazione. Se non specificato, viene usata la stringa di connessione predefinita dell'app per le funzioni. |
| **`systemKey`**  | Stringa di query    | Chiave di autorizzazione necessaria per richiamare l'API. |

`systemKey` è una chiave di autorizzazione generata automaticamente dall'host di funzioni di Azure. In particolare, concede l'accesso alle API dell'estensione Durable Task e può essere gestita allo stesso modo di [altre chiavi di autorizzazione](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). È possibile generare URL contenenti i valori di stringa di query `taskHub`, `connection`e `systemKey` corretti utilizzando le API di [associazione del client di orchestrazione](durable-functions-bindings.md#orchestration-client) , ad esempio le API `CreateCheckStatusResponse` e `CreateHttpManagementPayload` in .NET, oppure le API `createCheckStatusResponse` e `createHttpManagementPayload` in JavaScript.

Le prossime sezioni illustrano le specifiche API HTTP supportate dall'estensione e offrono esempi su come possono essere usate.

## <a name="start-orchestration"></a>Avviare l'orchestrazione

Avvia l'esecuzione di una nuova istanza della funzione dell'agente di orchestrazione specificata.

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti:

| Campo              | Tipo di parametro  | Descrizione |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | Nome della funzione dell'agente di orchestrazione da avviare. |
| **`instanceId`**   | URL             | Parametro facoltativo. ID dell'istanza di orchestrazione. Se non specificato, la funzione dell'agente di orchestrazione inizierà con un ID istanza casuale. |
| **`{content}`**    | Contenuto della richiesta | facoltativo. Input della funzione dell'agente di orchestrazione in formato JSON. |

### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 202 (accettato)** : la funzione dell'agente di orchestrazione specificata è stata pianificata per l'avvio dell'esecuzione. L'intestazione della risposta `Location` contiene un URL per il polling dello stato dell'orchestrazione.
* **HTTP 400 (richiesta**non valida): la funzione dell'agente di orchestrazione specificata non esiste, l'ID istanza specificato non è valido oppure il contenuto della richiesta non è un JSON valido.

Di seguito è riportata una richiesta di esempio che avvia una funzione dell'agente di orchestrazione `RestartVMs` e include il payload dell'oggetto JSON:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

Il payload di risposta per i case **HTTP 202** è un oggetto JSON con i campi seguenti:

| Campo                       | Descrizione                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID dell'istanza di orchestrazione. |
| **`statusQueryGetUri`**     |URL di stato dell'istanza di orchestrazione. |
| **`sendEventPostUri`**      |URL di generazione evento dell'istanza di orchestrazione. |
| **`terminatePostUri`**      |URL di terminazione dell'istanza di orchestrazione. |
| **`purgeHistoryDeleteUri`** |URL dell'istanza di orchestrazione "Elimina cronologia". |
| **`rewindPostUri`**         |anteprima URL "Rewind" dell'istanza di orchestrazione. |

Il tipo di dati di tutti i campi è `string`.

Ecco un payload di risposta di esempio per un'istanza di orchestrazione con `abc123` come ID (formattato per migliorare la leggibilità):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

La risposta HTTP è progettata per essere compatibile con il *modello di consumer di polling*. Include anche le intestazioni di risposta rilevanti seguenti:

* **Location**: URL dell'endpoint di stato. Questo URL contiene lo stesso valore del campo `statusQueryGetUri`.
* **Retry-After**: numero di secondi di attesa tra le operazioni di polling. Il valore predefinito è `10`.

Per ulteriori informazioni sul modello di polling HTTP asincrono, vedere la documentazione relativa al [rilevamento delle operazioni asincrone http](durable-functions-http-features.md#async-operation-tracking) .

## <a name="get-instance-status"></a>Get instance status (Ottieni stato istanza)

Ottiene lo stato di un'istanza di orchestrazione specificata.

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti:

| Campo                   | Tipo di parametro  | Descrizione |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID dell'istanza di orchestrazione. |
| **`showInput`**         | Stringa di query    | Parametro facoltativo. Se impostato su `false`, l'input della funzione non verrà incluso nel payload della risposta.|
| **`showHistory`**       | Stringa di query    | Parametro facoltativo. Se impostato su `true`, la cronologia di esecuzione dell'orchestrazione verrà inclusa nel payload della risposta.|
| **`showHistoryOutput`** | Stringa di query    | Parametro facoltativo. Se impostato su `true`, gli output della funzione verranno inclusi nella cronologia di esecuzione dell'orchestrazione.|
| **`createdTimeFrom`**   | Stringa di query    | Parametro facoltativo. Quando specificato, filtra l'elenco delle istanze restituite create in corrispondenza o dopo il timestamp ISO8601 specificato.|
| **`createdTimeTo`**     | Stringa di query    | Parametro facoltativo. Quando specificato, filtra l'elenco delle istanze restituite create in corrispondenza o prima del timestamp ISO8601 specificato.|
| **`runtimeStatus`**     | Stringa di query    | Parametro facoltativo. Se specificato, filtra l'elenco delle istanze restituite in base allo stato del runtime. Per visualizzare l'elenco dei possibili valori dello stato di runtime, vedere l'articolo [querying instances](durable-functions-instance-management.md) . |

### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 200 (OK)** : l'istanza specificata è in stato completato.
* **HTTP 202 (Accettata )** : l'istanza specificata è in esecuzione.
* **HTTP 400 (Richiesta non valida)** : l'istanza specificata ha avuto esito negativo o è stata terminata.
* **HTTP 404 (Non trovata)** : l'istanza specificata non esiste o l'esecuzione non è iniziata.
* **HTTP 500 (Errore interno del server)** : si è verificato un errore dell'istanza specificata con un'eccezione non gestita.

Il payload di risposta per i casi **HTTP 200** e **HTTP 202** è un oggetto JSON con i campi seguenti:

| Campo                 | Tipo di dati | Descrizione |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | stringa    | Stato di runtime dell'istanza. I valori includono *In esecuzione*, *In sospeso*, *Non riuscito*, *Annullato*, *Terminato*, *Completato*. |
| **`input`**           | JSON      | Dati JSON usati per inizializzare l'istanza. Questo campo è `null` quando il parametro della stringa di query `showInput` è impostato su `false`.|
| **`customStatus`**    | JSON      | I dati JSON usati per lo stato dell'orchestrazione personalizzato. Se non impostato, il campo è `null`. |
| **`output`**          | JSON      | Output JSON dell'istanza. Questo campo è `null` se l'istanza non è in stato completato. |
| **`createdTime`**     | stringa    | Data e ora di creazione dell'istanza. Usa la notazione estesa ISO 8601. |
| **`lastUpdatedTime`** | stringa    | Data e ora dell'ultimo stato persistente dell'istanza. Usa la notazione estesa ISO 8601. |
| **`historyEvents`**   | JSON      | Matrice JSON contenente la cronologia di esecuzione dell'orchestrazione. Questo campo è `null` a meno che il parametro della stringa di query `showHistory` non sia impostato su `true`. |

Ecco un payload di risposta di esempio che include la cronologia di esecuzione dell'orchestrazione e gli output delle attività (formattato per migliorarne la leggibilità):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

La risposta **HTTP 202** include anche un'intestazione di risposta **Location** che referenzia lo stesso URL del campo `statusQueryGetUri` citato in precedenza.

## <a name="get-all-instances-status"></a>Ottenere lo stato di tutte le istanze

È anche possibile eseguire una query sullo stato di tutte le istanze rimuovendo il `instanceId` dalla richiesta "Get instance status". In questo caso, i parametri di base sono gli stessi di "Get instance status". Sono supportati anche i parametri della stringa di query per il filtro.

Tenere presente che `connection` e `code` sono facoltativi. Se si dispone di auth anonimo per la funzione, `code` non è obbligatorio.
Se non si vuole usare una stringa di connessione di archiviazione diversa da quella definita nell'impostazione dell'app AzureWebJobsStorage, è possibile ignorare tranquillamente il parametro della stringa di query di connessione.

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti:

| Campo                   | Tipo di parametro  | Descrizione |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID dell'istanza di orchestrazione. |
| **`showInput`**         | Stringa di query    | Parametro facoltativo. Se impostato su `false`, l'input della funzione non verrà incluso nel payload della risposta.|
| **`showHistory`**       | Stringa di query    | Parametro facoltativo. Se impostato su `true`, la cronologia di esecuzione dell'orchestrazione verrà inclusa nel payload della risposta.|
| **`showHistoryOutput`** | Stringa di query    | Parametro facoltativo. Se impostato su `true`, gli output della funzione verranno inclusi nella cronologia di esecuzione dell'orchestrazione.|
| **`createdTimeFrom`**   | Stringa di query    | Parametro facoltativo. Quando specificato, filtra l'elenco delle istanze restituite create in corrispondenza o dopo il timestamp ISO8601 specificato.|
| **`createdTimeTo`**     | Stringa di query    | Parametro facoltativo. Quando specificato, filtra l'elenco delle istanze restituite create in corrispondenza o prima del timestamp ISO8601 specificato.|
| **`runtimeStatus`**     | Stringa di query    | Parametro facoltativo. Se specificato, filtra l'elenco delle istanze restituite in base allo stato del runtime. Per visualizzare l'elenco dei possibili valori dello stato di runtime, vedere l'articolo [querying instances](durable-functions-instance-management.md) . |
| **`top`**               | Stringa di query    | Parametro facoltativo. Se specificato, limita il numero di istanze restituite dalla query. |

### <a name="response"></a>Response

Di seguito è riportato un esempio di payload di risposta, compreso lo stato dell'orchestrazione (formattato per migliorare la leggibilità):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Questa operazione può risultare molto costosa in termini di Archiviazione di Azure I/O se sono presenti molte righe nella tabella delle istanze. Altre informazioni sulla tabella di istanza sono reperibili nella documentazione [Prestazioni e scalabilità in Funzioni permanenti (Funzioni di Azure)](durable-functions-perf-and-scale.md#instances-table).
>

Se sono presenti più risultati, nell'intestazione della risposta viene restituito un token di continuazione.  Il nome dell’intestazione è `x-ms-continuation-token`.

Se si imposta il valore del token di continuazione nell'intestazione della richiesta successiva, è possibile ottenere la pagina di risultati successiva. Il nome dell'intestazione della richiesta è anche `x-ms-continuation-token`.

## <a name="purge-single-instance-history"></a>Ripulisci cronologia istanza singola

Elimina la cronologia e gli artefatti correlati per un'istanza di orchestrazione specificata.

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti:

| Campo             | Tipo di parametro  | Descrizione |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID dell'istanza di orchestrazione. |

### <a name="response"></a>Response

È possibile restituire i seguenti valori di codice di stato HTTP.

* **HTTP 200 (OK)** : la cronologia dell'istanza è stata eliminata correttamente.
* **HTTP 404 (non trovato)** : l'istanza specificata non esiste.

Il payload di risposta per il case **HTTP 200** è un oggetto JSON con il campo seguente:

| Campo                  | Tipo di dati | Descrizione |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | numero intero   | Numero di istanze eliminate. Per il caso a istanza singola, questo valore deve sempre essere `1`. |

Di seguito è riportato un payload di risposta di esempio (formattato per migliorare la leggibilità):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Elimina più cronologie di istanze

È anche possibile eliminare la cronologia e gli artefatti correlati per più istanze all'interno di un hub attività rimuovendo il `{instanceId}` dalla richiesta "Ripulisci la cronologia di una singola istanza". Per eliminare selettivamente la cronologia delle istanze, usare gli stessi filtri descritti nella richiesta "Ottieni stato di tutte le istanze".

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti:

| Campo                 | Tipo di parametro  | Descrizione |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Stringa di query    | Filtra l'elenco delle istanze eliminate create in corrispondenza o dopo il timestamp ISO8601 specificato.|
| **`createdTimeTo`**   | Stringa di query    | Parametro facoltativo. Quando specificato, filtra l'elenco delle istanze eliminate che sono state create in corrispondenza o prima del timestamp ISO8601 specificato.|
| **`runtimeStatus`**   | Stringa di query    | Parametro facoltativo. Quando specificato, filtra l'elenco delle istanze eliminate in base al relativo stato di Runtime. Per visualizzare l'elenco dei possibili valori dello stato di runtime, vedere l'articolo [querying instances](durable-functions-instance-management.md) . |

> [!NOTE]
> Questa operazione può essere molto costosa in termini di I/O di archiviazione di Azure, se sono presenti numerose righe nelle tabelle delle istanze e/o della cronologia. Per ulteriori informazioni su queste tabelle, vedere la documentazione relativa [a prestazioni e scalabilità in Durable Functions (funzioni di Azure)](durable-functions-perf-and-scale.md#instances-table) .

### <a name="response"></a>Response

È possibile restituire i seguenti valori di codice di stato HTTP.

* **HTTP 200 (OK)** : la cronologia dell'istanza è stata eliminata correttamente.
* **HTTP 404 (non trovato)** : non sono state trovate istanze corrispondenti all'espressione di filtro.

Il payload di risposta per il case **HTTP 200** è un oggetto JSON con il campo seguente:

| Campo                   | Tipo di dati | Descrizione |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | numero intero   | Numero di istanze eliminate. |

Di seguito è riportato un payload di risposta di esempio (formattato per migliorare la leggibilità):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Raise event (Genera evento)

Invia un messaggio di notifica evento per un'istanza di orchestrazione in esecuzione.

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti:

| Campo             | Tipo di parametro  | Descrizione |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID dell'istanza di orchestrazione. |
| **`eventName`**   | URL             | Nome dell'evento atteso dall'istanza di orchestrazione di destinazione. |
| **`{content}`**   | Contenuto della richiesta | Payload dell'evento in formato JSON. |

### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 202 (Accettata)** : l'evento generato è stato accettato per l'elaborazione.
* **HTTP 400 (Richiesta non valida)** : il contenuto della richiesta non è di tipo `application/json` o non è un oggetto JSON valido.
* **HTTP 404 (Non trovata)** : l'istanza specificata non è stata trovata.
* **HTTP 410 (Non disponibile)** : l'istanza specificata è stata completata o ha avuto esito negativo e non può elaborare gli eventi generati.

Ecco una richiesta di esempio che invia la stringa JSON `"incr"` a un'istanza in attesa di un evento denominato **operation**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Nelle risposte per questa API non è presente contenuto.

## <a name="terminate-instance"></a>Arresta istanza

Termina un'istanza di orchestrazione in esecuzione.

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti.

| Campo             | Tipo di parametro  | Descrizione |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID dell'istanza di orchestrazione. |
| **`reason`**      | Stringa di query    | facoltativo. Motivo dell'interruzione dell'istanza di orchestrazione. |

### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 202 (Accettata)** : la richiesta di interruzione è stata accettata per l'elaborazione.
* **HTTP 404 (Non trovata)** : l'istanza specificata non è stata trovata.
* **HTTP 410 (Non disponibile)** : l'istanza specificata è stata completata o ha avuto esito negativo.

Ecco un esempio di richiesta che termina un'istanza in esecuzione e specifica il motivo **buggy** (con errori):

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Nelle risposte per questa API non è presente contenuto.

## <a name="rewind-instance-preview"></a>Ripristinare un'istanza (anteprima)

Ripristina lo stato in corso di esecuzione di un'istanza di orchestrazione non riuscita riproducendo le operazioni non riuscite più recenti.

### <a name="request"></a>Richiesta

Per la versione 1. x del runtime di funzioni, la richiesta viene formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Nella versione 2. x del runtime di funzioni, il formato dell'URL presenta tutti gli stessi parametri, ma con un prefisso leggermente diverso:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti.

| Campo             | Tipo di parametro  | Descrizione |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID dell'istanza di orchestrazione. |
| **`reason`**      | Stringa di query    | facoltativo. Motivo del ripristino dell'istanza di orchestrazione. |

### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 202 (Accettata)** : la richiesta di ripristino è stata accettata per l'elaborazione.
* **HTTP 404 (Non trovata)** : l'istanza specificata non è stata trovata.
* **HTTP 410 (Non disponibile)** : l'istanza specificata è stata completata o terminata.

Di seguito è riportato un esempio di richiesta che ripristina un'istanza non riuscita e specifica il motivo **fixed** (corretto):

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Nelle risposte per questa API non è presente contenuto.

## <a name="signal-entity"></a>Entità Signal

Invia un messaggio di operazione unidirezionale a un' [entità durevole](durable-functions-types-features-overview.md#entity-functions). Se l'entità non esiste, verrà creata automaticamente.

> [!NOTE]
> Le entità durevoli sono disponibili a partire da Durable Functions 2,0.

### <a name="request"></a>Richiesta

La richiesta HTTP è formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti:

| Campo             | Tipo di parametro  | Descrizione |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Tipo dell'entità. |
| **`entityKey`**   | URL             | Nome univoco dell'entità. |
| **`op`**          | Stringa di query    | facoltativo. Nome dell'operazione definita dall'utente da richiamare. |
| **`{content}`**   | Contenuto della richiesta | Payload dell'evento in formato JSON. |

Di seguito è riportato un esempio di richiesta che invia un messaggio "Add" definito dall'utente a un'entità `Counter` denominata `steps`. Il contenuto del messaggio è il valore `5`. Se l'entità non esiste già, verrà creata da questa richiesta:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

### <a name="response"></a>Response

Questa operazione presenta diverse risposte possibili:

* **HTTP 202 (accettato)** : l'operazione di segnalazione è stata accettata per l'elaborazione asincrona.
* **HTTP 400 (richiesta**non valida): il contenuto della richiesta non è di tipo `application/json`, non è un JSON valido o ha un valore `entityKey` non valido.
* **HTTP 404 (non trovato)** : il `entityType` specificato non è stato trovato.

Una richiesta HTTP con esito positivo non contiene contenuto nella risposta. Una richiesta HTTP non riuscita può contenere informazioni sull'errore in formato JSON nel contenuto della risposta.

## <a name="query-entity"></a>Entità query

Ottiene lo stato dell'entità specificata.

### <a name="request"></a>Richiesta

La richiesta HTTP è formattata come segue (vengono visualizzate più righe per maggiore chiarezza):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Response

Questa operazione ha due risposte possibili:

* **HTTP 200 (OK)** : l'entità specificata esiste.
* **HTTP 404 (non trovato)** : l'entità specificata non è stata trovata.

Una risposta con esito positivo contiene lo stato serializzato JSON dell'entità come contenuto.

### <a name="example"></a>Esempio
La richiesta HTTP di esempio seguente ottiene lo stato di un'entità di `Counter` esistente denominata `steps`:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Se l'entità `Counter` contiene semplicemente una serie di passaggi salvati in un campo `currentValue`, il contenuto della risposta potrebbe essere simile al seguente (formattato per migliorare la leggibilità):

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare Application Insights per monitorare le funzioni permanenti](durable-functions-diagnostics.md)