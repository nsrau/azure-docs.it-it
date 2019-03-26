---
title: Associazioni del bus di servizio di Azure per Funzioni di Azure
description: Informazioni su come usare trigger e associazioni del bus di servizio di Azure in Funzioni di Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 9955068fbc0d6493add83c6c92390413b3975106
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437172"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Associazioni del bus di servizio di Azure per Funzioni di Azure

Questo articolo descrive come usare le associazioni del bus di servizio in Funzioni di Azure. Funzioni di Azure supporta il trigger e le associazioni di output per le code e gli argomenti del bus di servizio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni del bus di servizio sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) versione 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni del bus di servizio sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Trigger

Usare il trigger di bus di servizio per rispondere a messaggi da una coda o da un argomento del bus di servizio. 

## <a name="trigger---example"></a>Trigger - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#trigger---c-example)
* [Script C# (file con estensione csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Trigger - esempio in C#

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che legge i [metadati del messaggio](#trigger---message-metadata) e registra un messaggio della coda del bus di servizio:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

Questo esempio riguarda Funzioni di Azure versione 1.x. Per correggere questo codice per la versione 2.x:

- [Omettere il parametro dei diritti di accesso](#trigger---configuration)
- Modificare il tipo del parametro di log da `TraceWriter` a `ILogger`
- Modificare `log.Info` in `log.LogInformation`

### <a name="trigger---c-script-example"></a>Trigger - esempio di script C#

L'esempio seguente mostra un'associazione di trigger del bus di servizio in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione legge i [metadati del messaggio](#trigger---message-metadata) e registra un messaggio della coda del bus di servizio.

Ecco i dati di associazione nel file *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Ecco il codice script C#:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Trigger - Esempio in F#

L'esempio seguente mostra un'associazione di trigger del bus di servizio in un file *function.json* e una [funzione F#](functions-reference-fsharp.md) che usa l'associazione. La funzione registra un messaggio della coda del bus di servizio. 

Ecco i dati di associazione nel file *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Ecco il codice script F#:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Trigger - Esempio Java

La funzione di Java seguente usa il `@ServiceBusQueueTrigger` annotazione dal [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) per descrivere la configurazione per un trigger della coda del Bus di servizio. La funzione recupera il messaggio inserito nella coda e lo aggiunge ai log.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Funzioni Java possono essere attivate anche quando un messaggio viene aggiunto a un argomento del Bus di servizio. L'esempio seguente usa il `@ServiceBusTopicTrigger` annotazioni per descrivere la configurazione del trigger.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Trigger - esempio JavaScript

L'esempio seguente mostra un'associazione di trigger del bus di servizio in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione legge i [metadati del messaggio](#trigger---message-metadata) e registra un messaggio della coda del bus di servizio. 

Ecco i dati di associazione nel file *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Ecco il codice script JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

## <a name="trigger---attributes"></a>Trigger - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare gli attributi seguenti per configurare un trigger del bus di servizio:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Il costruttore dell'attributo accetta il nome della coda o l'argomento e la sottoscrizione. In Funzioni di Azure versione 1.x è anche possibile specificare i diritti di accesso della connessione. Se i diritti di accesso non vengono specificati, l'impostazione predefinita è `Manage`. Per altre informazioni, vedere la sezione [Trigger - configurazione](#trigger---configuration).

  Ecco un esempio che mostra l'attributo usato con un parametro di stringa:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  È possibile impostare la proprietà `Connection` per specificare l'account per il bus di servizio da usare, come mostrato nell'esempio seguente:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Per un esempio completo, vedere [Trigger - esempio in C#](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Offre un altro modo per specificare l'account per il bus di servizio da usare. Il costruttore accetta il nome di un'impostazione dell'app che contiene una stringa di connessione del bus di servizio. L'attributo può essere applicato a livello di parametro, metodo o classe. L'esempio seguente illustra il livello classe e il livello metodo:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

L'account per il bus di servizio da usare è determinato nell'ordine seguente:

* La proprietà `Connection` dell'attributo `ServiceBusTrigger`.
* L'attributo `ServiceBusAccount` applicato allo stesso parametro dell'attributo `ServiceBusTrigger`.
* L'attributo `ServiceBusAccount` applicato alla funzione.
* L'attributo `ServiceBusAccount` applicato alla classe.
* L'impostazione dell'app "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Trigger - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `ServiceBusTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "serviceBusTrigger". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su "in". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta il messaggio della coda o dell'argomento nel codice della funzione. Impostare su "$return" per fare riferimento al valore restituito della funzione. |
|**queueName**|**QueueName**|Nome della coda da monitorare.  Impostare questa proprietà solo quando si monitora una coda, non un argomento.
|**topicName**|**topicName**|Nome dell'argomento da monitorare. Impostare questa proprietà solo quando si monitora un argomento, non una coda.|
|**subscriptionName**|**SubscriptionName**|Nome della sottoscrizione da monitorare. Impostare questa proprietà solo quando si monitora un argomento, non una coda.|
|**connessione**|**Connection**|Nome di un'impostazione dell'app che contiene la stringa di connessione del bus di servizio da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo la parte restante del nome. Ad esempio, se si imposta `connection` su "MyServiceBus", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyServiceBus". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione del bus di servizio predefinita nell'impostazione dell'app denominata "AzureWebJobsServiceBus".<br><br>Per ottenere una stringa di connessione, seguire i passaggi indicati in [Ottenere le credenziali di gestione](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#get-the-connection-string). La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico. |
|**accessRights**|**Accedere**|Diritti di accesso per la stringa di connessione. I valori disponibili sono `manage` e `listen`. Il valore predefinito è `manage`, che indica che `connection` dispone dell'autorizzazione **Gestisci**. Se si usa una stringa di connessione priva dell'autorizzazione **Gestisci**, impostare `accessRights` su "listen". In caso contrario, il runtime di Funzioni potrebbe non riuscire a eseguire operazioni che richiedono diritti di gestione. In Funzioni di Azure versione 2.x questa proprietà non è disponibile perché la versione più recente di Storage SDK non supporta le operazioni di gestione.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger - uso

In C# e nello script C# è possibile usare i tipi di parametro seguenti per il messaggio della coda o dell'argomento:

* `string`: se il messaggio è costituito da testo.
* `byte[]`: utile per i dati binari.
* Un tipo personalizzato: se il messaggio contiene JSON, Funzioni di Azure tenta di deserializzare i dati JSON.
* `BrokeredMessage`: visualizza il messaggio deserializzato con il metodo [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1).

Questi parametri sono per le versioni di Funzioni di Azure 1.x. Per le versioni 2.x, usare [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) invece di `BrokeredMessage`.

In JavaScript accedere al messaggio della coda o dell'argomento usando `context.bindings.<name from function.json>`. Il messaggio del bus di servizio viene passato alla funzione come stringa o oggetto JSON.

## <a name="trigger---poison-messages"></a>Trigger - messaggi non elaborabili

La gestione dei messaggi non elaborabili non può essere controllata o configurata in Funzioni di Azure. I messaggi non elaborabili vengono gestiti dal bus di servizio stesso.

## <a name="trigger---peeklock-behavior"></a>Trigger - Comportamento di PeekLock

Il runtime di Funzioni di Azure riceve un messaggio in [modalità PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Chiama quindi `Complete` nel messaggio se la funzione viene completata correttamente oppure chiama `Abandon` se la funzione non riesce. Se il tempo di esecuzione della funzione supera il timeout di `PeekLock`, il blocco viene rinnovato automaticamente finché la funzione è in esecuzione. 

Il valore `maxAutoRenewDuration` può essere configurato in *host.json*, che esegue il mapping a [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Il valore massimo consentito per questa impostazione è 5 minuti in base alla documentazione del bus di servizio, ma è possibile aumentare il limite temporale di Funzioni di Azure dal valore predefinito di 5 minuti a 10 minuti. Per le funzioni del bus di servizio non si voleva eseguire questa operazione in quel momento perché sarebbe stato superato il limite di rinnovo del bus di servizio.

## <a name="trigger---message-metadata"></a>Trigger - metadati del messaggio

Il trigger del bus di servizio fornisce diverse [proprietà di metadati](./functions-bindings-expressions-patterns.md#trigger-metadata). Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Queste sono le proprietà della classe [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).

|Proprietà|Type|DESCRIZIONE|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Il numero di recapiti.|
|`DeadLetterSource`|`string`|La coda di messaggi non recapitabili.|
|`ExpiresAtUtc`|`DateTime`|L'ora di scadenza in formato UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Il tempo di accodamento in formato UTC.|
|`MessageId`|`string`|Valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato.|
|`ContentType`|`string`|Un identificatore del tipo di contenuto usato dal mittente e dal destinatario per la logica specifica dell'applicazione.|
|`ReplyTo`|`string`|La risposta all'indirizzo della coda.|
|`SequenceNumber`|`Int64`|Il numero univoco assegnato a un messaggio dal bus di servizio.|
|`To`|`string`|L'indirizzo di destinazione.|
|`Label`|`string`|L'etichetta specifica dell'applicazione.|
|`CorrelationId`|`string`|L'ID di correlazione.|
|`UserProperties`|`IDictionary<String,Object>`|Le proprietà del messaggio specifiche dell'applicazione.|

> [!NOTE]
> Attualmente il trigger funziona solo con le code e le sottoscrizioni che non usano sessioni. Tenere traccia delle [informazioni riportate in questo argomento](https://github.com/Azure/azure-functions-host/issues/563) per altri aggiornamenti su questa funzionalità. 

Vedere gli [esempi di codice](#trigger---example) che usano queste proprietà in precedenza in questo articolo.

## <a name="trigger---hostjson-properties"></a>Trigger - proprietà di host.json

Il file [host.json](functions-host-json.md#servicebus) contiene le impostazioni che controllano il comportamento del trigger del bus di servizio.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------|
|maxConcurrentCalls|16|Il numero massimo di chiamate simultanee al callback che il message pump deve avviare. Per impostazione predefinita, il runtime di Funzioni elabora più messaggi contemporaneamente. Per fare in modo che il runtime elabori un solo messaggio della coda o dell'argomento alla volta, impostare `maxConcurrentCalls` su 1. |
|prefetchCount|n/d|Il valore predefinito di PrefetchCount che verrà utilizzato per il MessageReceiver sottostante.|
|maxAutoRenewDuration|00:05:00|La durata massima entro il quale il blocco del messaggio verrà rinnovato automaticamente.|

## <a name="output"></a>Output

Usare l'associazione di output del bus di servizio di Azure per inviare messaggi della coda o dell'argomento.

## <a name="output---example"></a>Output - esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#output---c-example)
* [Script C# (file con estensione csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Output - esempio in C#

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

### <a name="output---c-script-example"></a>Output - esempio di script C#

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
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Output - esempio in F#

L'esempio seguente mostra un'associazione di output del bus di servizio in un file *function.json* e una [funzione script F#](functions-reference-fsharp.md) che usa l'associazione. La funzione usa un trigger timer per inviare un messaggio della coda ogni 15 secondi.

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

Ecco il codice script F# che crea un singolo messaggio:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Output - Esempio Java

L'esempio seguente illustra una funzione Java che invia un messaggio alla coda di un bus di servizio `myqueue` quando la funzione viene attivata da una richiesta HTTP.

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

Funzioni Java anche possono scrivere un argomento del Bus di servizio. L'esempio seguente usa il `@ServiceBusTopicOutput` annotazioni per descrivere la configurazione per l'associazione di output. 

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

### <a name="output---javascript-example"></a>Output - esempio JavaScript

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

## <a name="output---attributes"></a>Output - attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Il costruttore dell'attributo accetta il nome della coda o l'argomento e la sottoscrizione. È anche possibile specificare i diritti di accesso della connessione. Nella sezione [Output - Configurazione](#output---configuration) viene spiegato come scegliere l'impostazione dei diritti di accesso. Ecco un esempio che mostra l'attributo applicato al valore restituito della funzione:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

È possibile impostare la proprietà `Connection` per specificare l'account per il bus di servizio da usare, come mostrato nell'esempio seguente:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Per un esempio completo, vedere [Output - esempio in C#](#output---c-example).

È possibile usare l'attributo `ServiceBusAccount` per specificare l'account per il bus di servizio da usare a livello di classe, metodo o parametro.  Per altre informazioni, vedere [Trigger - attributi](#trigger---attributes).

## <a name="output---configuration"></a>Output - configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `ServiceBus`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "serviceBus". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Deve essere impostato su "out". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Nome della variabile che rappresenta la coda o l'argomento nel codice della funzione. Impostare su "$return" per fare riferimento al valore restituito della funzione. |
|**queueName**|**QueueName**|Nome della coda.  Impostare questa proprietà solo se si inviano messaggi della coda, non dell'argomento.
|**topicName**|**topicName**|Nome dell'argomento da monitorare. Impostare questa proprietà solo se si inviano messaggi dell'argomento, non della coda.|
|**connessione**|**Connection**|Nome di un'impostazione dell'app che contiene la stringa di connessione del bus di servizio da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo la parte restante del nome. Ad esempio, se si imposta `connection` su "MyServiceBus", il runtime di Funzioni di Azure cerca un'impostazione dell'app denominata "AzureWebJobsMyServiceBus". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione del bus di servizio predefinita nell'impostazione dell'app denominata "AzureWebJobsServiceBus".<br><br>Per ottenere una stringa di connessione, seguire i passaggi indicati in [Ottenere le credenziali di gestione](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#get-the-connection-string). La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico.|
|**accessRights**|**Accedere**|Diritti di accesso per la stringa di connessione. I valori disponibili sono `manage` e `listen`. Il valore predefinito è `manage`, che indica che `connection` dispone dell'autorizzazione **Gestisci**. Se si usa una stringa di connessione priva dell'autorizzazione **Gestisci**, impostare `accessRights` su "listen". In caso contrario, il runtime di Funzioni potrebbe non riuscire a eseguire operazioni che richiedono diritti di gestione. In Funzioni di Azure versione 2.x questa proprietà non è disponibile perché la versione più recente di Storage SDK non supporta le operazioni di gestione.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Output - uso

In Funzioni di Azure 1.x il runtime crea la coda se inesistente e se il parametro `accessRights` è stato impostato su `manage`. In Funzioni versione 2.x la coda o l'argomento deve esistere già. Se si specifica una coda o un argomento inesistente, la funzione non viene eseguita. 

In C# e nello script C# è possibile usare i tipi di parametro seguenti per l'associazione di output:

* `out T paramName` - `T` può essere qualsiasi tipo serializzabile con JSON. Se il valore del parametro è null quando la funzione termina, Funzioni di Azure crea il messaggio con un oggetto null.
* `out string`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `out byte[]`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `out BrokeredMessage`: se il valore del parametro è null quando la funzione termina, Funzioni di Azure non crea un messaggio.
* `ICollector<T>` o `IAsyncCollector<T>`: per la creazione di più messaggi. Quando si chiama il metodo `Add` viene creato un messaggio.

Nelle funzioni asincrone usare il valore restituito o `IAsyncCollector` anziché un parametro `out`.

Questi parametri sono per le versioni di Funzioni di Azure 1.x. Per le versioni 2.x, usare [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) invece di `BrokeredMessage`.

In JavaScript accedere alla coda o all'argomento usando `context.bindings.<name from function.json>`. È possibile assegnare una stringa, una matrice di byte o un oggetto Javascript (deserializzato in JSON) a `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Eccezioni e codici restituiti

| Associazione | Riferimenti |
|---|---|
| Bus di servizio | [Codici di errore del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Bus di servizio | [Limiti del bus di servizio](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nella versione 2.x. Il file host.json di esempio seguente contiene solo le impostazioni della versione 2.x per questa associazione. Per altre informazioni sulle impostazioni di configurazione globali nella versione 2.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure versione 2.x](functions-host-json.md).

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
            }
        }
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|La durata massima entro il quale il blocco del messaggio verrà rinnovato automaticamente.|
|autoComplete|true|Indica se il trigger deve contrassegnare immediatamente come completato (completamento automatico) oppure attendere che venga eseguita la chiamata a complete.|
|maxConcurrentCalls|16|Il numero massimo di chiamate simultanee al callback che il message pump deve avviare. Per impostazione predefinita, il runtime di Funzioni elabora più messaggi contemporaneamente. Per fare in modo che il runtime elabori un solo messaggio della coda o dell'argomento alla volta, impostare `maxConcurrentCalls` su 1. |
|prefetchCount|n/d|Il valore predefinito di PrefetchCount che verrà utilizzato per il MessageReceiver sottostante.|


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
