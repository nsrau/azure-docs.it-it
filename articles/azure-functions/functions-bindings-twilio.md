---
title: Associazione di Twilio in Funzioni di Azure
description: Informazioni su come usare le associazioni di Twilio in Funzioni di Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cc6ca29af1866c5d26d3b73b26121451440c4dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511807"
---
# <a name="twilio-binding-for-azure-functions"></a>Associazione di Twilio per Funzioni di Azure

In questo articolo viene illustrato come inviare messaggi di testo tramite associazioni di [Twilio](https://www.twilio.com/) in Funzioni di Azure. Funzioni di Azure supporta associazioni di output per Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni di Twilio sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) versione 1.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacchetti: Funzioni 2.x

Le associazioni di Twilio sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Esempio - Funzioni 1.x

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#c-example)
* [Script C# (file con estensione csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Esempio in C#

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che invia un messaggio di testo quando viene attivata da un messaggio in coda.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

Questo esempio usa l'attributo `TwilioSms` con valore restituito del metodo. In alternativa è possibile usare l'attributo con un parametro `out SMSMessage` o un parametro `ICollector<SMSMessage>` o `IAsyncCollector<SMSMessage>`.

### <a name="c-script-example"></a>Esempio di script C#

L'esempio seguente mostra un'associazione di output di Twilio in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione usa un parametro `out` per inviare un messaggio di testo.

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ecco il codice script C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Non è possibile usare parametri out nel codice sincrono. Ecco un esempio di codice script C# asincrono:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Esempio JavaScript

L'esempio seguente illustra un'associazione di output di Twilio in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>Esempio - Funzioni 2.x

Vedere l'esempio specifico per ciascun linguaggio:

* [C# 2.x](#2x-c-example)
* [Script C# 2.x (file con estensione csx)](#2x-c-script-example)
* [JavaScript 2.x](#2x-javascript-example)

### <a name="2x-c-example"></a>Esempio in C# 2.x

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che invia un messaggio di testo quando viene attivata da un messaggio in coda.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

Questo esempio usa l'attributo `TwilioSms` con valore restituito del metodo. In alternativa è possibile usare l'attributo con un parametro `out CreateMessageOptions` o un parametro `ICollector<CreateMessageOptions>` o `IAsyncCollector<CreateMessageOptions>`.

### <a name="2x-c-script-example"></a>Esempio di script C# 2.x

L'esempio seguente mostra un'associazione di output di Twilio in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione usa un parametro `out` per inviare un messaggio di testo.

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ecco il codice script C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Non è possibile usare parametri out nel codice sincrono. Ecco un esempio di codice script C# asincrono:

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>Esempio JavaScript 2.x

L'esempio seguente illustra un'associazione di output di Twilio in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

Function.json di esempio:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Ecco il codice JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Attributi

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs).

Per informazioni sulle proprietà degli attributi che è possibile configurare, vedere la sezione [Configurazione](#configuration). Di seguito è riportato un esempio dell'attributo `TwilioSms` in una firma del metodo:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Per un esempio completo, vedere l'[esempio in C#](#c-example).

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `TwilioSms`.

| Proprietà v1 function.json | Proprietà v2 function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|---------|----------------------|
|**type**|**type**| deve essere impostato su `twilioSms`.|
|**direction**|**direction**| deve essere impostato su `out`.|
|**name**|**name**| Nome della variabile usato nel codice della funzione per il messaggio SMS di Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Questo valore deve essere impostato sul nome di un'impostazione dell'app che contiene il SID account di Twilio, ad esempio TwilioAccountSid. Se non viene impostato, il nome predefinito dell'impostazione dell'app è "AzureWebJobsTwilioAccountSid". |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Questo valore deve essere impostato sul nome di un'impostazione dell'app che contiene il token di autenticazione di Twilio, ad esempio TwilioAccountAuthToken. Se non viene impostato, il nome predefinito dell'impostazione dell'app è "AzureWebJobsTwilioAuthToken". |
|**to**| N/D - specificare nel codice | **To**| Questo valore è impostato sul numero di telefono a cui viene inviato il messaggio SMS.|
|**from**|**from** | **From**| Questo valore è impostato sul numero di telefono da cui viene inviato il messaggio SMS.|
|**body**|**body** | **Corpo**| Questo valore può essere usato per impostare come hardcoded il messaggio SMS se non è necessario impostarlo dinamicamente nel codice per la funzione. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
