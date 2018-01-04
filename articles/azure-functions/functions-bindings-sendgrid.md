---
title: Associazioni di SendGrid di Funzioni di Azure
description: Riferimento per le associazioni di SendGrid di Funzioni di Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/29/2017
ms.author: tdykstra
ms.openlocfilehash: aa64bbc764fe96d7c15d62660114222458e5acf4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="azure-functions-sendgrid-bindings"></a>Associazioni di SendGrid di Funzioni di Azure

Questo articolo illustra come inviare messaggi di posta elettronica tramite le associazioni di [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) in Funzioni di Azure. Funzioni di Azure supporta un'associazione di output per SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Esempio

Vedere l'esempio specifico per ciascun linguaggio:

* [C#](#c-example)
* [Script c# (con estensione csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Esempio in C#

Nell'esempio seguente un [funzione c#](functions-dotnet-class-library.md) che utilizza una coda del Bus di servizio trigger e un SendGrid associazione di output.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

È possibile omettere l'impostazione della proprietà `ApiKey` dell'attributo se si dispone della chiave API in un'impostazione applicazione denominata "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>Esempio di script C#

L'esempio seguente mostra un'associazione di output di SendGrid in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

```json 
{
    "bindings": [
        {
            "name": "message",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice script C#:

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

### <a name="javascript-example"></a>Esempio JavaScript

L'esempio seguente mostra un'associazione di output di SendGrid in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

Queste proprietà sono descritte nella sezione [configuration](#configuration).

Ecco il codice JavaScript:

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Attributi

In [librerie di classi c#](functions-dotnet-class-library.md), utilizzare il [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) attributo, che è definito nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid).

Per informazioni sulle proprietà degli attributi che è possibile configurare, vedere la sezione [Configurazione](#configuration). Di seguito è riportato un esempio dell'attributo `SendGrid` in una firma del metodo:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Per un esempio completo, vedere [esempio c#](#c-example).

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `SendGrid`.

|Proprietà di function.json | Proprietà dell'attributo |DESCRIZIONE|
|---------|---------|----------------------|
|**type**|| Obbligatoria. Deve essere impostata su `sendGrid`.|
|**direction**|| Obbligatoria. Deve essere impostata su `out`.|
|**nome**|| Obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. Questo valore è ```$return``` quando viene restituito un solo valore. |
|**apiKey**|**ApiKey**| Il nome di un'impostazione dell'app che contiene la chiave API. Se non viene impostato, il nome dell'impostazione dell'app predefinita è "AzureWebJobsSendGridApiKey".|
|**to**|**To**| Indirizzo e-mail del destinatario. |
|**from**|**From**| Indirizzo e-mail del mittente. |
|**subject**|**Oggetto**| Oggetto del messaggio di posta elettronica. |
|**text**|**Text**| Contenuto del messaggio di posta elettronica. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)