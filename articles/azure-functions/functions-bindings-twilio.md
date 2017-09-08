---
title: Binding di Twilio in Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare i binding di Twilio in Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, calcolo dinamico, architettura senza server
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e8c5e8f2dfedae26486e1c8afbe0cec3f3228e86
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>Inviare messaggi SMS da Funzioni di Azure usando il binding di output di Twilio
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Questo articolo illustra come configurare e usare i binding di Twilio in Funzioni di Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Funzioni di Azure supporta i binding di output di Twilio per consentire alle funzioni di inviare messaggi SMS con poche righe di codice e un account di [Twilio](https://www.twilio.com/). 

## <a name="functionjson-for-the-twilio-output-binding"></a>File function.json per il binding di output di Twilio
Il file function.json specifica le proprietà seguenti:

|Proprietà  |Descrizione  |
|---------|---------|
|**nome**| Nome della variabile usato nel codice della funzione per il messaggio SMS di Twilio. |
|**type**| deve essere impostato su `twilioSms`.|
|**accountSid**| Questo valore deve essere impostato sul nome di un'impostazione app che contiene il SID dell'account Twilio.|
|**authToken**| Questo valore deve essere impostato sul nome di un'impostazione app che contiene il token di autenticazione di Twilio.|
|**to**| Questo valore è impostato sul numero di telefono a cui viene inviato il messaggio SMS.|
|**from**| Questo valore è impostato sul numero di telefono da cui viene inviato il messaggio SMS.|
|**direction**| deve essere impostato su `out`.|
|**body**| Questo valore può essere usato per impostare come hardcoded il messaggio SMS se non è necessario impostarlo dinamicamente nel codice per la funzione. |

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


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Esempio di trigger della coda C# con il binding di output di Twilio
#### <a name="synchronous"></a>Sincrono
Questo esempio di codice sincrono per un trigger della coda di Archiviazione di Azure usa un parametro out per inviare un messaggio di testo a un cliente che ha effettuato un ordine.

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

#### <a name="asynchronous"></a>Asincrono
Questo esempio di codice asincrono per un trigger della coda di Archiviazione di Azure invia un messaggio di testo a un cliente che ha effettuato un ordine.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

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

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Esempio di trigger della coda Node.js con il binding di output di Twilio
Questo esempio di Node.js invia un messaggio di testo a un cliente che ha effettuato un ordine.

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

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


