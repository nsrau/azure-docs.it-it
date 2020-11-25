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
ms.openlocfilehash: 775075765c8c8eaa94541c0f094c1f7743fe59d9
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886788"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Risoluzione dei problemi in Servizi di comunicazione di Azure

Questo documento consente di risolvere i problemi che potrebbero verificarsi all'interno della soluzione Servizi di comunicazione. Per la risoluzione dei problemi del servizio SMS, è possibile [abilitare la creazione di report di recapito con Griglia di eventi](../quickstarts/telephony-sms/handle-sms-events.md) per acquisire i dettagli di recapito SMS.

## <a name="getting-help"></a>Risorse della Guida

Gli sviluppatori sono invitati a inviare domande, suggerire funzionalità e segnalare problemi nel [repository GitHub](https://github.com/Azure/communication) di Servizi di comunicazione. Altri forum sono:

* [Domane e risposte Microsoft](/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

A seconda del [piano di supporto](https://azure.microsoft.com/support/plans/) della sottoscrizione di Azure, è possibile inviare un ticket di supporto direttamente tramite il [portale di Azure](https://azure.microsoft.com/support/create-ticket/).

Per poter risolvere determinati tipi di problemi, è possibile che vengano richieste le informazioni seguenti:

* **ID MS-CV**: questo ID viene usato per la risoluzione dei problemi relativi a chiamate e messaggi. 
* **ID chiamata**: questo ID viene usato per identificare le chiamate a Servizi di comunicazione.
* **ID del messaggio SMS**: questo ID viene usato per identificare i messaggi SMS.
* **Registri chiamate**: questi registri contengono informazioni dettagliate che possono essere usate per risolvere i problemi di chiamata e di rete.


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

## <a name="enable-and-access-call-logs"></a>Abilitare e accedere ai registri chiamate




# <a name="javascript"></a>[JavaScript](#tab/javascript)

Il codice seguente può essere usato per configurare `AzureLogger` in modo da restituire i registri alla console usando la libreria client JavaScript:

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

Quando si sviluppa per iOS, i registri vengono archiviati nei file `.blog`. Si noti che non è possibile visualizzare i registri direttamente perché sono crittografati.

È possibile accedervi aprendo Xcode. Passare a Windows > Dispositivi e simulatori > Dispositivi. Selezionare il dispositivo. In App installate selezionare l'applicazione e fare clic su "Scarica contenitore". 

Verrà restituito un file `xcappdata`. Fare clic con il pulsante destro del mouse su questo file e selezionare "Mostra contenuto del pacchetto". Verranno quindi visualizzati i file di `.blog` che è possibile allegare alla richiesta al supporto tecnico di Azure.

# <a name="android"></a>[Android](#tab/android)

Quando si sviluppa per Android, i registri vengono archiviati nei file `.blog`. Si noti che non è possibile visualizzare i registri direttamente perché sono crittografati.

In Android Studio passare a Device File Explorer selezionando View > Tool Windows > Device File Explorer dal simulatore e dal dispositivo. Il file `.blog` si troverà all'interno della directory dell'applicazione, che dovrebbe essere simile a `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`. Il file può essere allegato alla richiesta di supporto. 
   

---


## <a name="related-information"></a>Informazioni correlate
- [Log e diagnostica](logging-and-diagnostics.md)
- [Metrics](metrics.md) (Metriche)