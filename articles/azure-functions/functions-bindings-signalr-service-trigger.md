---
title: Binding del trigger del servizio SignalR di funzioni di Azure
description: Informazioni su come inviare messaggi del servizio SignalR da funzioni di Azure.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: e2651afbcdc3bae71bb531aa0e821f83264c295d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212583"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Binding del trigger del servizio SignalR per funzioni di Azure

Usare l'associazione di trigger *SignalR* per rispondere ai messaggi inviati dal servizio Azure SignalR. Quando viene attivata la funzione, i messaggi passati alla funzione vengono analizzati come un oggetto JSON.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-signalr-service.md).

## <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata una funzione che riceve un messaggio utilizzando l'associazione di trigger e registra il messaggio.

# <a name="c"></a>[C#](#tab/csharp)

L'associazione del trigger del servizio SignalR per C# include due modelli di programmazione. Modello basato su classi e modello tradizionale. Il modello basato su classi può offrire un'esperienza di programmazione lato server SignalR coerente. E il modello tradizionale offre maggiore flessibilità e analogie con altre associazioni di funzioni.

### <a name="with-class-based-model"></a>Con modello basato su classe

Per informazioni dettagliate, vedere [modello basato su classi](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) .

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Con il modello tradizionale

Il modello tradizionale rispetta la convenzione della funzione di Azure sviluppata da C#. Se non si ha familiarità con esso, è possibile imparare da [documenti](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Usare l'attributo `[SignalRParameter]` per semplificare `ParameterNames`

Poiché è piuttosto complesso da usare `ParameterNames` , `SignalRParameter` viene fornito per ottenere lo stesso scopo.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Ecco il codice script C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Ecco il codice Python:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Configurazione

### <a name="signalrtrigger"></a>SignalRTrigger

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `SignalRTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type**| n/d | Il valore deve essere impostato su `SignalRTrigger`.|
|**direction**| n/d | Il valore deve essere impostato su `in`.|
|**nome**| n/d | Nome della variabile usato nel codice della funzione per l'oggetto contesto di chiamata del trigger. |
|**hubName**|**HubName**| Questo valore deve essere impostato sul nome dell'hub SignalR per la funzione da attivare.|
|**category**|**Categoria**| Questo valore deve essere impostato come categoria di messaggi per la funzione da attivare. La categoria può essere uno dei valori seguenti: <ul><li>**connessioni**: inclusi gli eventi *connessi* e *disconnessi*</li><li>**messaggi**: inclusi tutti gli altri eventi ad eccezione di quelli nella categoria *connessioni*</li></ul> |
|**event**|**Event**| Questo valore deve essere impostato come evento dei messaggi per la funzione da attivare. Per la categoria *messaggi* , l'evento è la *destinazione* nel [messaggio di chiamata](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) che i client inviano. Per la categoria *connessioni* , viene utilizzata solo la *connessione* e la *disconnessione* . |
|**parameterNames**|**ParameterNames**| Opzionale Elenco di nomi associato ai parametri. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nome dell'impostazione app contenente la stringa di connessione al servizio SignalR (valore predefinito:"AzureSignalRConnectionString") |

## <a name="payload"></a>Payload

Il tipo di input del trigger viene dichiarato come `InvocationContext` o come tipo personalizzato. Se si sceglie `InvocationContext` di ottenere l'accesso completo al contenuto della richiesta. Per un tipo personalizzato, il runtime cerca di analizzare il corpo della richiesta JSON per impostare le proprietà dell'oggetto.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext contiene tutto il contenuto del messaggio inviato dal servizio SignalR.

|Proprietà in InvocationContext | Descrizione|
|------------------------------|------------|
|Argomenti| Disponibile per la categoria *messaggi* . Contiene *argomenti* nel [messaggio di chiamata](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Errore| Disponibile per l'evento *disconnesso* . Può essere vuoto se la connessione è chiusa senza errori o contiene i messaggi di errore.|
|Hub| Nome dell'hub a cui appartiene il messaggio.|
|Category| Categoria del messaggio.|
|Evento| Evento del messaggio.|
|ConnectionId| ID connessione del client che invia il messaggio.|
|UserId| Identità utente del client che invia il messaggio.|
|Headers| Intestazioni della richiesta.|
|Query| Query della richiesta quando i client si connettono al servizio.|
|Attestazioni| Attestazioni del client.|

## <a name="using-parameternames"></a>Uso di `ParameterNames`

La proprietà `ParameterNames` in `SignalRTrigger` consente di associare gli argomenti dei messaggi di chiamata ai parametri delle funzioni. Che offre un modo più pratico per accedere agli argomenti di `InvocationContext` .

Supponiamo di avere un client SignalR JavaScript che tenta di richiamare il metodo `broadcast` nella funzione di Azure con due argomenti.

```javascript
await connection.invoke("broadcast", message1, message2);
```

È possibile accedere a questi due argomenti dal parametro, oltre a assegnare il tipo di parametro tramite `ParameterNames` .

### <a name="remarks"></a>Commenti

Per l'associazione di parametri, l'ordine è importante. Se si utilizza `ParameterNames` , l'ordine in `ParameterNames` corrisponde all'ordine degli argomenti richiamati nel client. Se si usa l'attributo `[SignalRParameter]` in C#, l'ordine degli argomenti nei metodi della funzione di Azure corrisponde all'ordine degli argomenti nei client.

`ParameterNames` l'attributo e `[SignalRParameter]` **non può** essere usato contemporaneamente oppure si otterrà un'eccezione.

## <a name="send-messages-to-signalr-service-trigger-binding"></a>Inviare messaggi al binding del trigger del servizio SignalR

Funzione di Azure genera un URL per l'associazione del trigger del servizio SignalR ed è formattato come segue:

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

`API_KEY`Viene generato dalla funzione di Azure. È possibile ottenere `API_KEY` dal portale di Azure quando si usa l'associazione del trigger del servizio SignalR.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Chiave API":::

È necessario impostare questo URL in `UrlTemplate` nelle impostazioni upstream del servizio SignalR.

## <a name="next-steps"></a>Passaggi successivi

* [Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Esempio di associazione del trigger del servizio SignalR](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)
