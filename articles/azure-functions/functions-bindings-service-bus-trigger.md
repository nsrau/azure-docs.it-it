---
title: Associazioni del bus di servizio di Azure per Funzioni di Azure
description: Informazioni su come eseguire una funzione di Azure quando vengono creati messaggi del bus di servizio di Azure.Learn to run an Azure Function when as Azure Service Bus messages are created.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273557"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Service Bus trigger for Azure Functions

Usare il trigger di bus di servizio per rispondere a messaggi da una coda o da un argomento del bus di servizio.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](functions-bindings-service-bus-output.md).

## <a name="example"></a>Esempio

# <a name="c"></a>[C #](#tab/csharp)

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che legge i [metadati del messaggio](#message-metadata) e registra un messaggio della coda del bus di servizio:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
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

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger del bus di servizio in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione legge i [metadati del messaggio](#message-metadata) e registra un messaggio della coda del bus di servizio.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

L'esempio seguente mostra un'associazione di trigger del bus di servizio in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione legge i [metadati del messaggio](#message-metadata) e registra un messaggio della coda del bus di servizio. 

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

# <a name="python"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrato come leggere un messaggio della coda del bus di servizio tramite un trigger.

Un binding del bus di servizio *type* viene definito `serviceBusTrigger`in *function.json* dove type è impostato su .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Il codice in `func.ServiceBusMessage` * _ \_init_\_.py* dichiara un parametro come , che consente di leggere il messaggio della coda nella funzione.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

La funzione Java `@ServiceBusQueueTrigger` seguente utilizza l'annotazione dalla libreria di runtime delle [funzioni Java](/java/api/overview/azure/functions/runtime) per descrivere la configurazione per un trigger della coda del bus di servizio. La funzione acquisisce il messaggio inserito nella coda e lo aggiunge ai registri.

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

Le funzioni Java possono essere attivate anche quando un messaggio viene aggiunto a un argomento del bus di servizio. Nell'esempio seguente `@ServiceBusTopicTrigger` viene utilizzata l'annotazione per descrivere la configurazione del trigger.

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

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C #](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare gli attributi seguenti per configurare un trigger del bus di servizio:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Il costruttore dell'attributo accetta il nome della coda o l'argomento e la sottoscrizione. In Funzioni di Azure versione 1.x è anche possibile specificare i diritti di accesso della connessione. Se i diritti di accesso non vengono specificati, l'impostazione predefinita è `Manage`. Per altre informazioni, vedere la sezione [Trigger - configurazione](#configuration).

  Ecco un esempio che mostra l'attributo usato con un parametro di stringa:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Puoi impostare `Connection` la proprietà per specificare il nome di un'impostazione dell'app che contiene la stringa di connessione del bus di servizio da usare, come illustrato nell'esempio seguente:You can set the property to specify the name of an app setting that contains the Service Bus connection string to use, as shown in the following example:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Per un esempio completo, vedere [Trigger - esempio](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

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

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati dallo script di C.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

L'annotazione `ServiceBusQueueTrigger` consente di creare una funzione che viene eseguita quando viene creato un messaggio della coda del bus di servizio. Le opzioni di configurazione disponibili includono il nome della coda e il nome della stringa di connessione.

L'annotazione `ServiceBusTopicTrigger` consente di designare un argomento e una sottoscrizione per i dati di destinazione che attivano la funzione.

Vedere [l'esempio](#example) di trigger per ulteriori dettagli.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente vengono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `ServiceBusTrigger` .

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su "serviceBusTrigger". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure.|
|**direction** | n/d | Il valore deve essere impostato su "in". Questa proprietà viene impostata automaticamente quando si crea il trigger nel portale di Azure. |
|**name** | n/d | Nome della variabile che rappresenta il messaggio della coda o dell'argomento nel codice della funzione. |
|**Queuename**|**QueueName**|Nome della coda da monitorare.  Impostare questa proprietà solo quando si monitora una coda, non un argomento.
|**topicName**|**NomeArgomento**|Nome dell'argomento da monitorare. Impostare questa proprietà solo quando si monitora un argomento, non una coda.|
|**subscriptionName (nomesottoscrizione)**|**NomeSottoscrizione**|Nome della sottoscrizione da monitorare. Impostare questa proprietà solo quando si monitora un argomento, non una coda.|
|**Connessione**|**Connessione**|Nome di un'impostazione dell'app che contiene la stringa di connessione del bus di servizio da usare per questa associazione. Se il nome dell'impostazione dell'app inizia con "AzureWebJobs", è possibile specificare solo la parte restante del nome. Ad esempio, se `connection` si imposta su "MyServiceBus", il runtime di Funzioni cerca un'impostazione dell'app denominata "AzureWebJobsMyServiceBus". Se si lascia vuoto `connection`, il runtime di Funzioni di Azure usa la stringa di connessione del bus di servizio predefinita nell'impostazione dell'app denominata "AzureWebJobsServiceBus".<br><br>Per ottenere una stringa di connessione, seguire i passaggi indicati in [Ottenere le credenziali di gestione](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). La stringa di connessione deve essere relativa a uno spazio dei nomi del bus di servizio e non limitata a una coda o un argomento specifico. |
|**Accessrights**|**Accesso**|Diritti di accesso per la stringa di connessione. I valori disponibili sono `manage` e `listen`. Il valore predefinito è `manage`, che indica che `connection` dispone dell'autorizzazione **Gestisci**. Se si usa una stringa di connessione priva dell'autorizzazione **Gestisci**, impostare `accessRights` su "listen". In caso contrario, il runtime di Funzioni potrebbe non riuscire a eseguire operazioni che richiedono diritti di gestione. In Funzioni di Azure versione 2.x e successive, questa proprietà non è disponibile perché la versione più recente di Service Bus SDK non supporta le operazioni di gestione.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`se ci si connette a una coda o a una sottoscrizione in grado di riconoscere [la sessione.](../service-bus-messaging/message-sessions.md) `false`in caso contrario, che è il valore predefinito.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Per il messaggio della coda o dell'argomento sono disponibili i seguenti tipi di parametro:

* `string`: se il messaggio è costituito da testo.
* `byte[]`: utile per i dati binari.
* Un tipo personalizzato: se il messaggio contiene JSON, Funzioni di Azure tenta di deserializzare i dati JSON.
* `BrokeredMessage`- Fornisce il messaggio deserializzato con il metodo [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Questi tipi di parametro sono per Funzioni di Azure versione 1.x.These parameter types are for Azure Functions version 1.x; per 2.x e [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) versioni `BrokeredMessage`successive, utilizzare al posto di .

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Per il messaggio della coda o dell'argomento sono disponibili i seguenti tipi di parametro:

* `string`: se il messaggio è costituito da testo.
* `byte[]`: utile per i dati binari.
* Un tipo personalizzato: se il messaggio contiene JSON, Funzioni di Azure tenta di deserializzare i dati JSON.
* `BrokeredMessage`- Fornisce il messaggio deserializzato con il metodo [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Questi parametri sono per Funzioni di Azure versione 1.x.These parameters are for Azure Functions version 1.x; per 2.x e [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) versioni `BrokeredMessage`successive, utilizzare al posto di .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Accedere al messaggio relativo `context.bindings.<name from function.json>`alla coda o all'argomento utilizzando . Il messaggio del bus di servizio viene passato alla funzione come stringa o oggetto JSON.

# <a name="python"></a>[Python](#tab/python)

Il messaggio della coda è disponibile per `func.ServiceBusMessage`la funzione tramite un parametro tipizzato come . Il messaggio del bus di servizio viene passato alla funzione come stringa o oggetto JSON.

# <a name="java"></a>[Java](#tab/java)

Il messaggio del bus di `ServiceBusQueueMessage` `ServiceBusTopicMessage` servizio in ingresso è disponibile tramite un parametro o .

[Vedere l'esempio per i dettagli](#example).

---

## <a name="poison-messages"></a>Messaggi non elaborabili

La gestione dei messaggi non elaborabili non può essere controllata o configurata in Funzioni di Azure. I messaggi non elaborabili vengono gestiti dal bus di servizio stesso.

## <a name="peeklock-behavior"></a> Comportamento di PeekLock

Il runtime di Funzioni di Azure riceve un messaggio in [modalità PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Chiama quindi `Complete` nel messaggio se la funzione viene completata correttamente oppure chiama `Abandon` se la funzione non riesce. Se il tempo di esecuzione della funzione supera il timeout di `PeekLock`, il blocco viene rinnovato automaticamente finché la funzione è in esecuzione. 

Il valore `maxAutoRenewDuration` può essere configurato in *host.json*, che esegue il mapping a [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Il valore massimo consentito per questa impostazione è 5 minuti in base alla documentazione del bus di servizio, ma è possibile aumentare il limite temporale di Funzioni di Azure dal valore predefinito di 5 minuti a 10 minuti. Per le funzioni del bus di servizio non si voleva eseguire questa operazione in quel momento perché sarebbe stato superato il limite di rinnovo del bus di servizio.

## <a name="message-metadata"></a>Metadati del messaggioMessage metadata

Il trigger del bus di servizio fornisce diverse [proprietà di metadati](./functions-bindings-expressions-patterns.md#trigger-metadata). Queste proprietà possono essere usate come parte delle espressioni di associazione in altre associazioni o come parametri nel codice. Queste proprietà sono membri della classe [BrokeredMessage.These](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) properties are members of the BrokeredMessage class.

|Proprietà|Type|Descrizione|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Il numero di recapiti.|
|`DeadLetterSource`|`string`|La coda di messaggi non recapitabili.|
|`ExpiresAtUtc`|`DateTime`|L'ora di scadenza in formato UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Il tempo di accodamento in formato UTC.|
|`MessageId`|`string`|Valore definito dall'utente che il bus di servizio può usare per identificare i messaggi duplicati, se abilitato.|
|`ContentType`|`string`|Identificatore del tipo di contenuto utilizzato dal mittente e dal destinatario per la logica specifica dell'applicazione.|
|`ReplyTo`|`string`|La risposta all'indirizzo della coda.|
|`SequenceNumber`|`Int64`|Il numero univoco assegnato a un messaggio dal bus di servizio.|
|`To`|`string`|L'indirizzo di destinazione.|
|`Label`|`string`|Etichetta specifica dell'applicazione.|
|`CorrelationId`|`string`|L'ID di correlazione.|

Vedere gli [esempi di codice](#example) che usano queste proprietà in precedenza in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

- [Inviare messaggi del bus di servizio di Azure da Funzioni di Azure (associazione di output)Send Azure Service Bus messages from Azure Functions (Output binding)](./functions-bindings-service-bus-output.md)
