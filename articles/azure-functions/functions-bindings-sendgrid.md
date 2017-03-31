---
title: Associazioni di SendGrid di Funzioni di Azure | Microsoft Docs
description: Riferimento per le associazioni di SendGrid di Funzioni di Azure
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/16/2017
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 0cd7e7c55e77863c142800cdc11d6ea144c38293
ms.lasthandoff: 03/18/2017


---
# <a name="azure-functions-sendgrid-bindings"></a>Associazioni di SendGrid di Funzioni di Azure

Questo articolo illustra come configurare e usare le associazioni SendGrid in Funzioni di Azure. Con Sendgrid è possibile usare Funzioni di Azure per inviare messaggi di posta elettronica personalizzati a livello programmatico.

Questo articolo contiene le informazioni di riferimento per gli sviluppatori di Funzioni di Azure. Se non si ha familiarità con le Funzioni di Azure, iniziare con le seguenti risorse:

[Creare la prima funzione di Azure](functions-create-first-azure-function.md). 
Informazioni di riferimento per sviluppatori su [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) o [Node](functions-reference-node.md).

## <a name="functionjson-for-sendgrid-bindings"></a>function.json per associazioni di SendGrid

Funzioni di Azure offre associazioni di output per SendGrid. L'associazione di output di SendGrid consente di creare e inviare messaggi di posta elettronica a livello programmatico. 

L'associazione SendGrid supporta le proprietà seguenti:

- `name`: obbligatoria. Nome della variabile usato nel codice della funzione per la richiesta o il corpo della richiesta. Questo valore è ```$return``` quando viene restituito un solo valore. 
- `type`: obbligatoria. Deve essere impostata su "sendGrid".
- `direction`: obbligatoria. Deve essere impostata su "out".
- `apiKey`: obbligatoria. Deve essere impostato sul nome della chiave API archiviato nelle impostazioni app dell'app per le funzioni.
- `to`: indirizzo di posta elettronica del destinatario.
- `from`: indirizzo di posta elettronica del mittente.
- `subject`: l'oggetto del messaggio di posta elettronica.
- `text`: il contenuto del messaggio di posta elettronica.

**function.json** di esempio:

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

> [!NOTE]
> Funzioni di Azure archivia le informazioni di connessione e le chiavi API come impostazioni dell'app in modo che non vengano controllate nel repository di controllo del codice sorgente. In questo modo viene garantita la protezione delle informazioni riservate.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>C# di esempio dell'associazione di output SendGrid

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
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

## <a name="node-example-of-the-sendgrid-output-binding"></a>Nodo di esempio dell'associazione di output SendGrid

```javascript
module.exports = function (context, input) {    
    var message = {
        to: "recipient@contoso.com",
        from: "sender@contoso.com",        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle altre associazioni e i trigger per Funzioni di Azure, vedere 
- [Guida di riferimento per gli sviluppatori di trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

- L'articolo [Procedure consigliate per le funzioni di Azure](functions-best-practices.md) elenca alcune procedure consigliate da usare durante la creazione di Funzioni di Azure.

- La [guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md) contiene informazioni di riferimento per programmatori in merito alla codifica delle funzioni e alla definizione di trigger e associazioni.
