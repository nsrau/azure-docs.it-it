---
title: API HTTP in Funzioni permanenti - Azure
description: Informazioni su come implementare API HTTP nell'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: bb5361022e4c9693812753ae33df5aeb037b5aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>API HTTP in Funzioni permanenti (Funzioni di Azure)

L'estensione Durable Task espone un set di API HTTP che può essere utilizzato per eseguire le attività seguenti:

* Recuperare lo stato di un'istanza di orchestrazione.
* Inviare un evento a un'istanza di orchestrazione in attesa.
* Terminare un'istanza di orchestrazione in esecuzione.

Ognuna di queste API HTTP è un'operazione webhook gestita direttamente dall'estensione Durable Task. Non sono specifiche di alcuna funzione nell'app per le funzioni.

> [!NOTE]
> Queste operazioni possono essere anche richiamate direttamente usando le API di gestione istanze sulla classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Per altre informazioni, vedere [Gestione delle istanze](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Rilevamento dell'URL di API HTTP

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) espone un'API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) che può essere usata per generare un payload di risposta HTTP contenente collegamenti a tutte le operazioni supportate. Di seguito è riportato un esempio di funzione trigger HTTP che illustra come usare questa API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Questa funzione di esempio genera i dati di risposta JSON seguenti. Il tipo di dati di tutti i campi è `string`.

| Campo             |Descrizione                           |
|-------------------|--------------------------------------|
| id                |ID dell'istanza di orchestrazione. |
| statusQueryGetUri |URL di stato dell'istanza di orchestrazione. |
| sendEventPostUri  |URL di generazione evento dell'istanza di orchestrazione. |
| terminatePostUri  |URL di terminazione dell'istanza di orchestrazione. |

Di seguito è riportata una risposta di esempio:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Il formato degli URL webhook può variare in base alla versione dell'host di Funzioni di Azure in esecuzione. L'esempio precedente è per l'host di Funzioni di Azure 2.0.

## <a name="async-operation-tracking"></a>Verifica di operazioni asincrone

La risposta HTTP indicata in precedenza è stata concepita per semplificare l'implementazione di API asincrone a esecuzione prolungata con Funzioni permanenti. Questo viene a volte definito *modello Polling Consumer*. Il flusso client/server funziona come segue:

1. Il client invia una richiesta HTTP per avviare un processo a esecuzione prolungata, ad esempio una funzione di orchestrazione.
2. Il trigger HTTP di destinazione restituisce una risposta HTTP 202 con un'intestazione `Location` con valore `statusQueryGetUri`.
3. Il client esegue il polling dell'URL nell'intestazione `Location`. Continua a visualizzare risposte HTTP 202 con un'intestazione `Location`.
4. Quando l'istanza viene completata o ha esito negativo, l'endpoint nell'intestazione `Location` restituisce HTTP 200.

Questo protocollo consente di coordinare processi a esecuzione prolungata con client o servizi esterni che supportano il polling di un endpoint HTTP e la capacità di seguire l'intestazione `Location`. Gli elementi fondamentali sono già integrati nelle API HTTP di Funzioni permanenti.

> [!NOTE]
> Per impostazione predefinita, tutte le azioni basate su HTTP fornite dalle [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) supportano il modello di operazione asincrono standard. Questo rende possibile incorporare una funzione permanente a esecuzione prolungata come parte di un flusso di lavoro di App per la logica. Per altre informazioni sul supporto dei modelli HTTP asincroni da parte di App per la logica, vedere la [documentazione sulle azioni e i trigger del flusso di lavoro di App per la logica di Azure](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Informazioni di riferimento sulle API HTTP

Tutte le API HTTP implementate dall'estensione richiedono i parametri seguenti. Il tipo di dati di tutti i parametri è `string`.

| .  | Tipo di parametro  | Descrizione |
|------------|-----------------|-------------|
| instanceId | URL             | ID dell'istanza di orchestrazione. |
| taskHub    | Stringa di query    | Nome dell'[hub attività](durable-functions-task-hubs.md). Se non specificato, viene usato il nome dell'hub attività dell'app per le funzioni corrente. |
| connessione | Stringa di query    | **Nome** della stringa di connessione per l'account di archiviazione. Se non specificato, viene usata la stringa di connessione predefinita dell'app per le funzioni. |
| systemKey  | Stringa di query    | Chiave di autorizzazione necessaria per richiamare l'API. |

`systemKey` è una chiave di autorizzazione generata automaticamente dall'host di Funzioni di Azure. In particolare, concede l'accesso alle API dell'estensione Durable Task e può essere gestita allo stesso modo di [altre chiavi di autorizzazione](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Il modo più semplice per individuare il valore `systemKey` consiste nell'usare l'API `CreateCheckStatusResponse` indicata in precedenza.

Le prossime sezioni illustrano le specifiche API HTTP supportate dall'estensione e offrono esempi su come possono essere usate.

### <a name="get-instance-status"></a>Get instance status (Ottieni stato istanza)

Ottiene lo stato di un'istanza di orchestrazione specificata.

#### <a name="request"></a>Richiesta

Per Funzioni 1.0, il formato della richiesta è il seguente:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Il formato di Funzioni 2.0 usa gli stessi parametri ma ha un prefisso URL leggermente diverso:

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 200 (OK)**: l'istanza specificata è in stato completato.
* **HTTP 202 (Accettata )**: l'istanza specificata è in esecuzione.
* **HTTP 400 (Richiesta non valida)**: l'istanza specificata ha avuto esito negativo o è stata terminata.
* **HTTP 404 (Non trovata)**: l'istanza specificata non esiste o l'esecuzione non è iniziata.

Il payload di risposta per i casi **HTTP 200** e **HTTP 202** è un oggetto JSON con i campi seguenti.

| Campo           | Tipo di dati | Descrizione |
|-----------------|-----------|-------------|
| runtimeStatus   | string    | Stato di runtime dell'istanza. I valori includono *In esecuzione*, *In sospeso*, *Non riuscito*, *Annullato*, *Terminato*, *Completato*. |
| input           | JSON      | Dati JSON usati per inizializzare l'istanza. |
| output          | JSON      | Output JSON dell'istanza. Questo campo è `null` se l'istanza non è in stato completato. |
| createdTime     | string    | Data e ora di creazione dell'istanza. Usa la notazione estesa ISO 8601. |
| lastUpdatedTime | string    | Data e ora dell'ultimo stato persistente dell'istanza. Usa la notazione estesa ISO 8601. |

Di seguito è riportato un payload di risposta di esempio (formattato per migliorare la leggibilità):

```json
{
  "runtimeStatus": "Completed",
  "input": null,
  "output": [
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
  ],
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T18:30:30Z"
}
```

La risposta **HTTP 202** include anche un'intestazione di risposta **Location** che referenzia lo stesso URL del campo `statusQueryGetUri` citato in precedenza.

### <a name="raise-event"></a>Raise event (Genera evento)

Invia un messaggio di notifica evento per un'istanza di orchestrazione in esecuzione.

#### <a name="request"></a>Richiesta

Per Funzioni 1.0, il formato della richiesta è il seguente:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Il formato di Funzioni 2.0 usa gli stessi parametri ma ha un prefisso URL leggermente diverso:

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti.

| Campo       | Tipo di parametro  | Tipo di dati | Descrizione |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | string    | Nome dell'evento atteso dall'istanza di orchestrazione di destinazione. |
| {content}   | Contenuto della richiesta | JSON      | Payload dell'evento in formato JSON. |

#### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 202 (Accettata)**: l'evento generato è stato accettato per l'elaborazione.
* **HTTP 400 (Richiesta non valida)**: il contenuto della richiesta non è di tipo `application/json` o non è un oggetto JSON valido.
* **HTTP 404 (Non trovata)**: l'istanza specificata non è stata trovata.
* **HTTP 410 (Non disponibile)**: l'istanza specificata è stata completata o ha avuto esito negativo e non può elaborare gli eventi generati.

Ecco una richiesta di esempio che invia la stringa JSON `"incr"` a un'istanza in attesa di un evento denominato **operation** (tratta dall'esempio di [contatore](durable-functions-counter.md)):

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Nelle risposte per questa API non è presente contenuto.

### <a name="terminate-instance"></a>Arresta istanza

Termina un'istanza di orchestrazione in esecuzione.

#### <a name="request"></a>Richiesta

Per Funzioni 1.0, il formato della richiesta è il seguente:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Il formato di Funzioni 2.0 usa gli stessi parametri ma ha un prefisso URL leggermente diverso:

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

I parametri della richiesta per questa API includono il set predefinito indicato in precedenza, nonché i parametri univoci seguenti.

| Campo       | Tipo di parametro  | Tipo di dati | Descrizione |
|-------------|-----------------|-----------|-------------|
| reason      | Stringa di query    | string    | Facoltativo. Motivo dell'interruzione dell'istanza di orchestrazione. |

#### <a name="response"></a>Response

Possono essere restituiti diversi valori di codice di stato.

* **HTTP 202 (Accettata)**: la richiesta di interruzione è stata accettata per l'elaborazione.
* **HTTP 404 (Non trovata)**: l'istanza specificata non è stata trovata.
* **HTTP 410 (Non disponibile)**: l'istanza specificata è stata completata o ha avuto esito negativo.

Ecco un esempio di richiesta che termina un'istanza in esecuzione e specifica il motivo **buggy** (con errori):

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Nelle risposte per questa API non è presente contenuto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come gestire gli errori](durable-functions-error-handling.md)
