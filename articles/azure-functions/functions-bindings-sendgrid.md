---
title: Associazioni di SendGrid di Funzioni di Azure
description: Riferimento per le associazioni di SendGrid di Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: e318e5f9b192b9f857a0b97d076ce4cc87cfb73d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710975"
---
# <a name="azure-functions-sendgrid-bindings"></a>Associazioni di SendGrid di Funzioni di Azure

Questo articolo illustra come inviare messaggi di posta elettronica tramite le associazioni di [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) in Funzioni di Azure. Funzioni di Azure supporta un'associazione di output per SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacchetti: Funzioni 1.x

Le associazioni di SendGrid sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), versione 2.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages-Functions 2. x e versioni successive

Le associazioni di SendGrid sono incluse nel pacchetto NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), versione 3.x. Il codice sorgente del pacchetto si trova nel repository GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Esempio

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L'esempio seguente mostra una [funzione C#](functions-dotnet-class-library.md) che usa un trigger della coda del bus di servizio e un'associazione di output SendGrid.

### <a name="synchronous"></a>Sincrono

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Asincrona

```cs
[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
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

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

L'esempio seguente mostra un'associazione di output di SendGrid in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione.

Ecco i dati di associazione nel file *function.json*:

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
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
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Nell'esempio seguente viene illustrata una funzione attivata tramite HTTP che invia un messaggio di posta elettronica utilizzando l'associazione SendGrid. È possibile specificare i valori predefiniti nella configurazione dell'associazione. Ad esempio, l'indirizzo di posta elettronica *da* viene configurato in *Function. JSON*. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

La funzione seguente mostra come è possibile fornire valori personalizzati per le proprietà facoltative.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="javatabjava"></a>[Java](#tab/java)

L'esempio seguente usa l'annotazione `@SendGridOutput` dalla [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime) per inviare un messaggio di posta elettronica usando l'associazione di output SendGrid.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs).

Per informazioni sulle proprietà degli attributi che è possibile configurare, vedere la sezione [Configurazione](#configuration). Di seguito è riportato un esempio dell'attributo `SendGrid` in una firma del metodo:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Per un esempio completo, vedere l'[esempio in C#](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Script](#tab/csharp-script)

Gli attributi non sono supportati C# dallo script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L'annotazione [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) consente di configurare in modo dichiarativo l'associazione SendGrid fornendo valori di configurazione. Per altri dettagli, vedere le sezioni di [esempio](#example) e di [configurazione](#configuration) .

---

## <a name="configuration"></a>Configurazione

La tabella seguente elenca le proprietà di configurazione dell'associazione disponibili nel file *Function. JSON* e l'attributo/annotazione `SendGrid`.

| *Function. JSON* (proprietà) | Proprietà attribute/annotation | Description | Facoltativo |
|--------------------------|-------------------------------|-------------|----------|
| type |N/D| Il valore deve essere impostato su `sendGrid`.| No |
| direction |N/D| Il valore deve essere impostato su `out`.| No |
| name |N/D| Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. Questo valore è `$return` quando viene restituito un solo valore. | No |
| apiKey | ApiKey | Il nome di un'impostazione dell'app che contiene la chiave API. Se non è impostato, il nome predefinito dell'impostazione dell'app è *AzureWebJobsSendGridApiKey*.| No |
| to| Per | Indirizzo e-mail del destinatario. | Sì |
| da| Da | Indirizzo di posta elettronica del mittente. |  Sì |
| subject| Argomento | Oggetto del messaggio di posta elettronica. | Sì |
| text| Testo | Contenuto del messaggio di posta elettronica. | Sì |

Le proprietà facoltative possono avere valori predefiniti definiti nell'associazione e aggiunti o sottoposti a override a livello di codice.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>impostazioni host.json

Questa sezione descrive le impostazioni di configurazione globali disponibili per questa associazione nelle versioni 2. x e successive. Il file host. JSON di esempio seguente contiene solo le impostazioni della versione 2. x + per questa associazione. Per ulteriori informazioni sulle impostazioni di configurazione globali nelle versioni 2. x e successive, vedere informazioni di [riferimento su host. JSON per funzioni di Azure](functions-host-json.md).

> [!NOTE]
> Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|da|N/D|Indirizzo di posta elettronica del mittente in tutte le funzioni.| 


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
