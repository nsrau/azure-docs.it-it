---
title: Risoluzione dei problemi in Servizi di comunicazione di Azure
description: Come raccogliere le informazioni necessarie per risolvere i problemi relativi alla soluzione Servizi di comunicazione.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754666"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Risoluzione dei problemi in Servizi di comunicazione di Azure

Questo documento illustra come raccogliere le informazioni necessarie per risolvere i problemi relativi alla soluzione Servizi di comunicazione.

## <a name="getting-help"></a>Risorse della Guida

Gli sviluppatori sono invitati a inviare domande, suggerire funzionalità e segnalare problemi nel [repository GitHub](https://github.com/Azure/communication) di Servizi di comunicazione. Altri forum sono:

* [Domane e risposte Microsoft](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

A seconda del [piano di supporto](https://azure.microsoft.com/support/plans/) della sottoscrizione di Azure, è possibile inviare un ticket di supporto direttamente tramite il [portale di Azure](https://azure.microsoft.com/support/create-ticket/).

Per poter risolvere determinati tipi di problemi, è possibile che vengano richieste le informazioni seguenti:

* **ID MS-CV** : questo ID viene usato per la risoluzione dei problemi relativi a chiamate e messaggi. 
* **ID chiamata** : questo ID viene usato per identificare le chiamate a Servizi di comunicazione.
* **ID del messaggio SMS** : questo ID viene usato per identificare i messaggi SMS.

## <a name="access-your-ms-cv-id"></a>Accedere all'ID MS-CV

È possibile accedere all'ID MS-CV configurando la diagnostica nell'istanza dell'oggetto `clientOptions` durante l'inizializzazione delle librerie client. La diagnostica può essere configurata per qualsiasi libreria client di Azure, tra cui Chat, Amministrazione e Chiamate VoIP.

### <a name="client-options-example"></a>Esempio di opzioni client

I frammenti di codice seguenti illustrano la configurazione della diagnostica. Quando si usano le librerie client con la diagnostica abilitata, i dettagli di diagnostica vengono emessi nel listener di eventi configurato:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Accedere all'ID chiamata

Quando si compila una richiesta di supporto relativa a problemi delle chiamate nel portale di Azure, potrebbe essere chiesto di fornire l'ID della chiamata in questione. Questo ID è accessibile tramite la libreria client per le chiamate:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Accedere all'ID del messaggio SMS

Per i problemi relativi agli SMS, è possibile ottenere l'ID del messaggio dall'oggetto risposta.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Informazioni correlate
- [Log e diagnostica](logging-and-diagnostics.md)
- [Metrics](metrics.md) (Metriche)
