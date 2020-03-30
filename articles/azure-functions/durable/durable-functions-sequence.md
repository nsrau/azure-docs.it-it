---
title: Concatenamento di funzioni in Funzioni permanenti - Azure
description: Scopri come eseguire un esempio di Funzioni permanenti che effettua una sequenza di funzioni.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562063"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Concatenamento di funzioni in Funzioni permanenti - Esempio di sequenza di Hello

Il concatenamento di funzioni si riferisce al criterio di esecuzione di una sequenza di funzioni in un determinato ordine. Spesso l'output di una funzione deve essere applicato all'input di un'altra funzione. Questo articolo descrive la sequenza di concatenamento creata al completamento dell'avvio rapido di Durable Functions ([ C# ](durable-functions-create-first-csharp.md) oppure [JavaScript](quickstart-js-vscode.md)). Per altre informazioni su Durable Functions, vedere [Panoramica di Durable Functions](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funzioni

Questo articolo descrive le funzioni seguenti nell'app di esempio:

* `E1_HelloSequence`: [funzione dell'agente](durable-functions-bindings.md#orchestration-trigger) `E1_SayHello` di orchestrazione che chiama più volte in una sequenza. Archivia gli output delle chiamate `E1_SayHello` e registra i risultati.
* `E1_SayHello`: [funzione](durable-functions-bindings.md#activity-trigger) di attività che antepone una stringa con "Hello".
* `HttpStart`: funzione attivata da HTTP che avvia un'istanza dell'agente di orchestrazione.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence funzione dell'agente di orchestrazione

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Tutte le funzioni di orchestrazione C# devono avere un parametro di tipo `DurableOrchestrationContext` disponibile nell'assembly `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Questo oggetto di contesto consente di chiamare `CallActivityAsync` altre funzioni di *attività* e passare parametri di input utilizzando il relativo metodo.

Il codice chiama `E1_SayHello` tre volte in sequenza con valori dei parametri diversi. Il valore restituito di ogni chiamata viene aggiunto all'elenco `outputs`, che viene restituito al termine della funzione.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> JavaScript Durable Functions è disponibile solo per il runtime di Functions 2.0.

#### <a name="functionjson"></a>function.json

Se si usa Visual Studio Code o il portale di Azure per lo sviluppo, questo è il contenuto del file *function.json* per la funzione di orchestrazione. La maggior parte dei file *function.json* dell'agente di orchestrazione sono quasi identici a questo.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Fondamentale è il tipo di associazione `orchestrationTrigger`. Tutte le funzioni di orchestrazione devono usare questo tipo di trigger.

> [!WARNING]
> Per rispettare la regola "no I/O" delle funzioni di orchestrazione, non usare associazioni di input o output quando si usa l'associazione di trigger `orchestrationTrigger`.  Quando sono necessarie altre associazioni di input o output, occorre invece usarle nel contesto delle funzioni `activityTrigger` già chiamate dall'agente di orchestrazione. Per altre informazioni, vedere l'articolo Vincoli di [codice della funzione dell'agente di orchestrazione.](durable-functions-code-constraints.md)

#### <a name="indexjs"></a>index.js

Ecco la funzione:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Tutte le funzioni di orchestrazione [ `durable-functions` ](https://www.npmjs.com/package/durable-functions)JavaScript devono includere il modulo . Si tratta di una libreria che consente di scrivere funzioni durevoli in JavaScript.It's a library that enables you to write Durable Functions in JavaScript. Esistono tre differenze significative tra una funzione di orchestrazione e altre funzioni JavaScript:

1. La funzione è una [funzione generatore.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript). .
2. Per la funzione viene eseguito il wrapping in una chiamata al metodo `durable-functions` del modulo `orchestrator` (qui `df`).
3. La funzione deve essere sincrona. Poiché il metodo 'orchestrator' gestisce la chiamata a 'context.done', la funzione deve semplicemente restituire.

L'oggetto `context` `df` contiene un oggetto di contesto di orchestrazione durevole `callActivity` che consente di chiamare altre funzioni di *attività* e passare parametri di input utilizzando il relativo metodo. Il codice chiama `E1_SayHello` tre volte in sequenza con valori di parametro diversi, usando `yield` per indicare che l'esecuzione deve attendere la restituzione delle chiamate di funzioni di attività asincrone. Il valore restituito di ogni `outputs` chiamata viene aggiunto alla matrice, che viene restituita alla fine della funzione.

---

### <a name="e1_sayhello-activity-function"></a>Funzione di attività E1_SayHello

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Le attività `ActivityTrigger` utilizzano l'attributo. Utilizzare l'oggetto fornito `IDurableActivityContext` per eseguire azioni correlate all'attività, ad esempio l'accesso al valore di input tramite `GetInput<T>`.

L'implementazione di `E1_SayHello` è un'operazione di formattazione delle stringhe relativamente semplice.

Anziché eseguire `IDurableActivityContext`l'associazione a un oggetto , è possibile eseguire l'associazione direttamente al tipo passato nella funzione di attività. Ad esempio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

Il file *function.json* per la funzione di attività `E1_SayHello` è simile a quello di `E1_HelloSequence` ad eccezione del fatto che usa un tipo di associazione `activityTrigger` anziché un tipo di associazione `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Tutte le funzioni chiamate da una funzione di orchestrazione devono usare l'associazione `activityTrigger`.

L'implementazione di `E1_SayHello` è un'operazione di formattazione delle stringhe relativamente semplice.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

A differenza di una funzione di orchestrazione di JavaScript, una funzione di attività non ha bisogno di alcuna configurazione speciale. L'input passato al metodo tramite la funzione dell'agente di orchestrazione è presente nell'oggetto `context.bindings` sotto il nome del binding `activityTrigger`, in questo caso `context.bindings.name`. Il nome del binding può essere impostato come parametro della funzione esportata e vi si può accedere direttamente, come nel codice di esempio.

---

### <a name="httpstart-client-function"></a>Funzione client HttpStartHttpStart client function

È possibile avviare un'istanza della funzione dell'agente di orchestrazione usando una funzione client. Verrà utilizzata `HttpStart` la funzione attivata HTTP `E1_HelloSequence`per avviare istanze di .

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Per interagire con gli agenti di `DurableClient` orchestrazione, la funzione deve includere un'associazione di input. Utilizzare il client per avviare un'orchestrazione. Può anche aiutare a restituire una risposta HTTP contenente gli URL per controllare lo stato della nuova orchestrazione.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/funzione.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Per interagire con gli agenti di `durableClient` orchestrazione, la funzione deve includere un'associazione di input.

#### <a name="httpstartindexjs"></a>HttpStart/index.js (informazioni in inglese)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Utilizzare `df.getClient` per `DurableOrchestrationClient` ottenere un oggetto. Utilizzare il client per avviare un'orchestrazione. Può anche aiutare a restituire una risposta HTTP contenente gli URL per controllare lo stato della nuova orchestrazione.

---

## <a name="run-the-sample"></a>Eseguire l'esempio

Per eseguire `E1_HelloSequence` l'orchestrazione, inviare la `HttpStart` seguente richiesta HTTP POST alla funzione.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Il frammento di codice HTTP precedente presuppone che sia presente una voce nel file `host.json` che consente di rimuovere il prefisso predefinito `api/` da tutti gli URL di funzioni di trigger HTTP. È possibile trovare il markup per la configurazione nel file `host.json` negli esempi.

Ad esempio, se si esegue l'esempio in un'app per le funzioni denominata "myfunctionapp", sostituire "{host}" con "myfunctionapp.azurewebsites.net".

Il risultato è una risposta HTTP 202, simile alla seguente (tagliata per brevità):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

A questo punto, l'orchestrazione viene messa in coda e inizia ad avviarsi immediatamente. L'URL nell'intestazione `Location` può essere usato per controllare lo stato dell'esecuzione.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Il risultato è lo stato dell'orchestrazione. Si esegue e completa rapidamente e appare nello stato *Completato* con una risposta simile alla seguente (tagliata per brevità):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Come si può notare, il `runtimeStatus` dell'istanza è *Completato* e `output` contiene il risultato JSON serializzato dell'esecuzione della funzione di orchestrazione.

> [!NOTE]
> È possibile implementare una logica di avvio simile per altri tipi di trigger, ad esempio `queueTrigger`, `eventHubTrigger`, o `timerTrigger`.

Esaminare i log di esecuzione della funzione. La `E1_HelloSequence` funzione è stata avviata e completata più volte a causa del comportamento di riproduzione descritto nell'argomento [affidabilità dell'orchestrazione.](durable-functions-orchestrations.md#reliability) In compenso, si sono verificate solo tre esecuzioni di `E1_SayHello` da quando tali esecuzioni delle funzioni non vengono riprodotte.

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stata illustrata una semplice orchestrazione di concatenamento delle funzioni. Nell'esempio successivo viene illustrato come implementare il criterio di fan-out/fan-in.

> [!div class="nextstepaction"]
> [Eseguire l'esempio di Fan-out/fan-in](durable-functions-cloud-backup.md)
