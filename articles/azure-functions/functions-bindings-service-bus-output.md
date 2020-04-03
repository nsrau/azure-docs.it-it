---
title: Associazioni del bus di servizio di Azure per Funzioni di Azure
description: Informazioni su come inviare messaggi del bus di servizio di Azure da Funzioni di Azure.Learn to send Azure Service Bus messages from Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582246"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus output binding for Azure Functions

Usare l'associazione di output del bus di servizio di Azure per inviare messaggi della coda o dell'argomento.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-service-bus-output.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che invia un messaggio della coda del bus di servizio:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente mostra un'associazione di output del bus di servizio in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione usa un trigger timer per inviare un messaggio della coda ogni 15 secondi.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Ecco il codice script C# che crea un singolo messaggio:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Ecco il codice script C# che crea più messaggi:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

L'esempio seguente mostra un'associazione di output del bus di servizio in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione usa un trigger timer per inviare un messaggio della coda ogni 15 secondi.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Ecco il codice script JavaScript che crea un singolo messaggio:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Ecco il codice script JavaScript che crea più messaggi:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come scrivere in una coda del bus di servizio in Python.The following example demonstrates how to write out to a Service Bus queue in Python.

Una definizione di associazione del *type* bus di `serviceBus`servizio viene definita in *function.json* dove type è impostato su .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

In `set` init *\_.py è possibile scrivere un messaggio nella coda passando un valore al metodo . _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Nell'esempio seguente viene illustrata una funzione Java `myqueue` che invia un messaggio a una coda del bus di servizio quando viene attivata da una richiesta HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) usare `@QueueOutput` l'annotazione per i parametri di funzione il cui valore viene scritto in una coda di bus di servizio.  Il tipo di parametro deve essere `OutputBinding<T>`, dove T corrisponde a un qualsiasi tipo Java nativo di un oggetto POJO.

Le funzioni Java possono anche scrivere in un argomento del bus di servizio. Nell'esempio seguente `@ServiceBusTopicOutput` viene utilizzata l'annotazione per descrivere la configurazione per l'associazione di output. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Il costruttore dell'attributo accetta il nome della coda o l'argomento e la sottoscrizione. È anche possibile specificare i diritti di accesso della connessione. Nella sezione [Output - Configurazione](#configuration) viene spiegato come scegliere l'impostazione dei diritti di accesso. Ecco un esempio che mostra l'attributo applicato al valore restituito della funzione:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Puoi impostare `Connection` la proprietà per specificare il nome di un'impostazione dell'app che contiene la stringa di connessione del bus di servizio da usare, come illustrato nell'esempio seguente:You can set the property to specify the name of an app setting that contains the Service Bus connection string to use, as shown in the following example:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio](#example).

È possibile usare l'attributo `ServiceBusAccount` per specificare l'account per il bus di servizio da usare a livello di classe, metodo o parametro.  Per altre informazioni, vedere [Trigger - attributi](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Le `ServiceBusQueueOutput` `ServiceBusTopicOutput` annotazioni e sono disponibili per scrivere un messaggio come output della funzione. Il parametro decorato con queste annotazioni deve essere dichiarato come `OutputBinding<T>` where `T` è il tipo corrispondente al tipo del messaggio.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `ServiceBus` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "serviceBus". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Deve essere impostato su "out". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**name** | n/d | Nome della variabile che rappresenta il messaggio della coda o dell'argomento nel codice della funzione. Impostare su "$return" per fare riferimento al valore restituito della funzione. |
|**Queuename**|**QueueName**|Nome della coda.  Impostare questa proprietà solo se si inviano messaggi della coda, non dell'argomento.
|**topicName**|**NomeArgomento**|Nome dell'argomento. Impostare questa proprietà solo se si inviano messaggi dell'argomento, non della coda.|
|**Connessione**|**Connessione**|Nome di un'impostazione dell'app che contiene la stringa di connessione del bus di servizio da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo la parte restante del nome. Ad esempio, se `connection` si imposta su "MyServiceBus", il runtime di Funzioni cerca un'impostazione dell'app denominata "AzureWebJobsMyServiceBus". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione del bus di servizio predefinita nell'impostazione dell'app denominata "AzureWebJobsServiceBus".<br><br>Per ottenere una stringa di connessione, seguire i passaggi indicati in [Ottenere le credenziali di gestione](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico.|
|**Accessrights**|**Accesso**|Diritti di accesso per la stringa di connessione. I valori disponibili sono `manage` e `listen`. Il valore predefinito è `manage`, che indica che `connection` dispone dell'autorizzazione **Gestisci**. Se si usa una stringa di connessione priva dell'autorizzazione **Gestisci**, impostare `accessRights` su "listen". In caso contrario, il runtime di Funzioni potrebbe non riuscire a eseguire operazioni che richiedono diritti di gestione. In Funzioni di Azure versione 2.x e successive, questa proprietà non è disponibile perché la versione più recente di Service Bus SDK non supporta le operazioni di gestione.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

In Funzioni di Azure 1.x il runtime crea la coda se inesistente e se il parametro `accessRights` è stato impostato su `manage`. In Funzioni versione 2.x e successive, la coda o l'argomento deve già esistere; se si specifica una coda o un argomento che non esiste, la funzione avrà esito negativo. 

# <a name="c"></a>[C#](#tab/csharp)

Utilizzare i seguenti tipi di parametro per l'associazione di output:

* `out T paramName` - `T` può essere qualsiasi tipo serializzabile con JSON. Se il valore del parametro è null quando la funzione termina, Funzioni di Azure crea il messaggio con un oggetto null.
* `out string`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `out byte[]`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `out BrokeredMessage`- Se il valore del parametro è null quando la funzione viene chiusa, Funzioni non crea un messaggio (per funzioni 1.x)
* `out Message`- Se il valore del parametro è null quando la funzione viene chiusa, Funzioni non crea un messaggio (per funzioni 2.x e versioni successive)- If the parameter value is null when the function exits, Functions does not create a message (for Functions 2.x and higher)
* `ICollector<T>` o `IAsyncCollector<T>`: per la creazione di più messaggi. Quando si chiama il metodo `Add` viene creato un messaggio.

Quando si lavora con le funzioni di C:

* Le funzioni asincrone `IAsyncCollector` richiedono un `out` valore restituito o invece di un parametro.

* Per accedere all'ID di [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessione, `sessionId` eseguire l'associazione a un tipo e utilizzare la proprietà .

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Utilizzare i seguenti tipi di parametro per l'associazione di output:

* `out T paramName` - `T` può essere qualsiasi tipo serializzabile con JSON. Se il valore del parametro è null quando la funzione termina, Funzioni di Azure crea il messaggio con un oggetto null.
* `out string`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `out byte[]`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `out BrokeredMessage`- Se il valore del parametro è null quando la funzione viene chiusa, Funzioni non crea un messaggio (per funzioni 1.x)
* `out Message`- Se il valore del parametro è null quando la funzione viene chiusa, Funzioni non crea un messaggio (per funzioni 2.x e versioni successive)- If the parameter value is null when the function exits, Functions does not create a message (for Functions 2.x and higher)
* `ICollector<T>` o `IAsyncCollector<T>`: per la creazione di più messaggi. Quando si chiama il metodo `Add` viene creato un messaggio.

Quando si lavora con le funzioni di C:

* Le funzioni asincrone `IAsyncCollector` richiedono un `out` valore restituito o invece di un parametro.

* Per accedere all'ID di [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessione, `sessionId` eseguire l'associazione a un tipo e utilizzare la proprietà .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Accedere alla coda o `context.bindings.<name from function.json>`all'argomento utilizzando . È possibile assegnare una stringa, una matrice di byte o un `context.binding.<name>`oggetto JavaScript (deserializzato in JSON) a .

# <a name="python"></a>[Python](#tab/python)

Usare [l'SDK](https://docs.microsoft.com/azure/service-bus-messaging) del bus di servizio di Azure anziché l'associazione di output predefinita.

# <a name="java"></a>[Java](#tab/java)

Usare [l'SDK](https://docs.microsoft.com/azure/service-bus-messaging) del bus di servizio di Azure anziché l'associazione di output predefinita.

---

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione | Informazioni di riferimento |
|---|---|
| Bus di servizio | [Codici di errore del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Bus di servizio | [Limiti del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>impostazioni host.json

In questa sezione vengono descritte le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2.x e successive. Il file host.json di esempio riportato di seguito contiene solo le impostazioni per questa associazione. Per altre informazioni sulle impostazioni di configurazione globali, vedere informazioni di riferimento su host.json per la versione di Funzioni di Azure.For more information about global configuration settings, see [host.json reference for Azure Functions version.](functions-host-json.md)

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|prefetchCount|0|Ottiene o imposta il numero di messaggi che il destinatario dei messaggi può richiedere contemporaneamente.|
|maxAutoRenewDuration|00:05:00|La durata massima entro il quale il blocco del messaggio verrà rinnovato automaticamente.|
|autoComplete|true|Se il trigger deve contrassegnare immediatamente il messaggio come completato (completamento automatico) o attendere che la funzione venga chiusa correttamente per completare la chiamata.|
|maxConcurrentCalls|16|Il numero massimo di chiamate simultanee al callback che il message pump deve avviare. Per impostazione predefinita, il runtime di Funzioni elabora più messaggi contemporaneamente. Per fare in modo che il runtime elabori un solo messaggio della coda o dell'argomento alla volta, impostare `maxConcurrentCalls` su 1. |

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione quando viene creata una coda del bus di servizio o un messaggio di argomento (Trigger)Run a function when a Service Bus queue or topic message is created (Trigger)](./functions-bindings-service-bus-trigger.md)
