---
title: Sviluppare e configurare le applicazioni del servizio SignalR di funzioni di Azure
description: Informazioni dettagliate su come sviluppare e configurare applicazioni in tempo reale senza server usando funzioni di Azure e il servizio Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: be77704f562a1e05485e6f3704dff265635b1dc2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882314"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Sviluppo e configurazione di funzioni di Azure con il servizio Azure SignalR

Le applicazioni di funzioni di Azure possono sfruttare le [associazioni del servizio Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) per aggiungere funzionalità in tempo reale. Le applicazioni client usano gli SDK client disponibili in diverse lingue per connettersi al servizio Azure SignalR e ricevere messaggi in tempo reale.

Questo articolo descrive i concetti per lo sviluppo e la configurazione di un'app per le funzioni di Azure integrata con il servizio SignalR.

## <a name="signalr-service-configuration"></a>Configurazione del servizio SignalR

Il servizio Azure SignalR può essere configurato in modalità diverse. Se usato con funzioni di Azure, il servizio deve essere configurato in modalità senza *Server* .

Nella portale di Azure individuare la pagina delle *Impostazioni* della risorsa del servizio SignalR. Impostare la *modalità del servizio* su senza *Server*.

![Modalità del servizio SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Sviluppo di funzioni di Azure

Un'applicazione in tempo reale senza server compilata con funzioni di Azure e il servizio Azure SignalR richiede in genere due funzioni di Azure:

* Funzione "Negotiate" chiamata dal client per ottenere un token di accesso del servizio SignalR e un URL dell'endpoint di servizio validi
* Una o più funzioni che inviano messaggi o gestiscono l'appartenenza ai gruppi

### <a name="negotiate-function"></a>Negotiate-funzione

Un'applicazione client richiede un token di accesso valido per la connessione al servizio Azure SignalR. Un token di accesso può essere anonimo o autenticato a un ID utente specificato. Le applicazioni del servizio SignalR senza server richiedono un endpoint HTTP denominato "Negotiate" per ottenere un token e altre informazioni di connessione, ad esempio l'URL dell'endpoint del servizio SignalR.

Usare una funzione di Azure attivata da HTTP e l'associazione di input di *SignalRConnectionInfo* per generare l'oggetto informazioni di connessione. La funzione deve avere una route HTTP che termina con `/negotiate`.

Per ulteriori informazioni su come creare la funzione Negotiate, vedere la Guida di [riferimento dell'associazione di input *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Per informazioni su come creare un token autenticato, vedere l'articolo relativo all' [uso dell'autenticazione del servizio app](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Invio di messaggi e gestione dell'appartenenza al gruppo

Usare l' associazione di output di SignalR per inviare messaggi ai client connessi al servizio Azure SignalR. È possibile trasmettere messaggi a tutti i client oppure è possibile inviarli a un subset di client autenticati con un ID utente specifico o aggiunti a un gruppo specifico.

Gli utenti possono essere aggiunti a uno o più gruppi. È anche possibile usare l' associazione di output di SignalR per aggiungere o rimuovere utenti da e verso gruppi.

Per altre informazioni, vedere riferimento dell'associazione di output di [ *SignalR* ](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Hub SignalR

SignalR ha un concetto di "hub". Ogni connessione client e ogni messaggio inviato da funzioni di Azure hanno come ambito un hub specifico. È possibile utilizzare gli hub come un modo per separare le connessioni e i messaggi in spazi dei nomi logici.

## <a name="client-development"></a>Sviluppo client

Le applicazioni client SignalR possono sfruttare l'SDK client di SignalR in uno dei diversi linguaggi per connettersi e ricevere messaggi dal servizio Azure SignalR in modo semplice.

### <a name="configuring-a-client-connection"></a>Configurazione di una connessione client

Per connettersi al servizio SignalR, un client deve completare una negoziazione di connessione riuscita che prevede i passaggi seguenti:

1. Eseguire una richiesta all'endpoint http Negotiate illustrato in precedenza per ottenere informazioni di connessione valide
1. Connettersi al servizio SignalR usando l'URL dell'endpoint del servizio e il token di accesso ottenuti dall'endpoint Negotiate

Gli SDK del client SignalR contengono già la logica necessaria per eseguire l'handshake di negoziazione. Passare l'URL dell'endpoint Negotiate, meno `negotiate` il segmento, all' `HubConnectionBuilder`SDK. Di seguito è riportato un esempio in JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Per convenzione, l'SDK aggiunge `/negotiate` automaticamente all'URL e lo usa per iniziare la negoziazione.

> [!NOTE]
> Se si usa JavaScript/TypeScript SDK in un browser, è necessario [abilitare la condivisione di risorse tra le origini (CORS)](#enabling-cors) nel app per le funzioni.

Per ulteriori informazioni su come utilizzare SignalR client SDK, consultare la documentazione relativa alla lingua:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Invio di messaggi da un client al servizio

Anche se SignalR SDK consente alle applicazioni client di richiamare la logica di back-end in un hub SignalR, questa funzionalità non è ancora supportata quando si usa il servizio SignalR con funzioni di Azure. Usare le richieste HTTP per richiamare funzioni di Azure.

## <a name="azure-functions-configuration"></a>Configurazione di funzioni di Azure

Le app per le funzioni di Azure che si integrano con il servizio Azure SignalR possono essere distribuite come qualsiasi tipica app per le funzioni di Azure, usando tecniche come la [distribuzione continua](../azure-functions/functions-continuous-deployment.md), la [distribuzione zip](../azure-functions/deployment-zip-push.md)e l' [esecuzione da un pacchetto](../azure-functions/run-functions-from-deployment-package.md).

Tuttavia, esistono alcune considerazioni speciali per le app che usano le associazioni del servizio SignalR. Se il client viene eseguito in un browser, è necessario abilitare CORS. Se l'app richiede l'autenticazione, è possibile integrare l'endpoint Negotiate con l'autenticazione del servizio app.

### <a name="enabling-cors"></a>Abilitazione della condivisione CORS

Il client JavaScript/TypeScript esegue richieste HTTP alla funzione Negotiate per avviare la negoziazione della connessione. Quando l'applicazione client è ospitata in un dominio diverso dall'app per le funzioni di Azure, la condivisione di risorse tra le origini (CORS) deve essere abilitata nell'app per le funzioni oppure il browser bloccherà le richieste.

#### <a name="localhost"></a>Localhost

Quando si esegue l'app per le funzioni nel computer locale, è possibile `Host` aggiungere una sezione a *local. Settings. JSON* per abilitare CORS. `Host` Nella sezione aggiungere due proprietà:

* `CORS`-Immettere l'URL di base che rappresenta l'origine dell'applicazione client
* `CORSCredentials`-impostarla su `true` per consentire le richieste "withCredentials"

Esempio:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Cloud-funzioni di Azure CORS

Per abilitare CORS in un'app per le funzioni di Azure, passare alla schermata di configurazione di CORS nella scheda *funzionalità della piattaforma* dell'app per le funzioni nella portale di Azure.

> [!NOTE]
> La configurazione di CORS non è ancora disponibile nel piano a consumo Linux di funzioni di Azure. Usare [gestione API di Azure](#cloud---azure-api-management) per abilitare CORS.

Per chiamare la funzione Negotiate, è necessario abilitare CORS con Access-Control-Allow-Credentials per il client SignalR. Selezionare la casella di controllo per abilitarla.

Nella sezione *origini* consentite aggiungere una voce con l'URL di base di origine dell'applicazione Web.

![Configurazione di CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud-gestione API di Azure

Gestione API di Azure offre un gateway API che consente di aggiungere funzionalità ai servizi back-end esistenti. È possibile usarlo per aggiungere CORS all'app per le funzioni. Offre un livello di consumo con prezzi con pagamento in base all'azione e una concessione mensile gratuita.

Per informazioni su come [importare un'app](../api-management/import-function-app-as-api.md)per le funzioni di Azure, vedere la documentazione di gestione API. Una volta importato, è possibile aggiungere un criterio in ingresso per abilitare CORS con il supporto di Access-Control-Allow-Credentials.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Configurare i client SignalR per l'uso dell'URL di gestione API.

### <a name="using-app-service-authentication"></a>Uso dell'autenticazione del servizio app

Funzioni di Azure offre l'autenticazione incorporata, che supporta i provider più diffusi, ad esempio Facebook, Twitter, account Microsoft, Google e Azure Active Directory. Questa funzionalità può essere integrata con l'associazione *SignalRConnectionInfo* per creare connessioni al servizio Azure SignalR autenticato a un ID utente. L'applicazione può inviare messaggi usando l' associazione di output SignalR destinata a tale ID utente.

Nella scheda *funzionalità della piattaforma* dell'app per le funzioni della portale di Azure aprire la finestra impostazioni di *autenticazione/autorizzazione* . Per configurare l'autenticazione tramite un provider di identità di propria scelta, seguire la documentazione per l' [autenticazione del servizio app](../app-service/overview-authentication-authorization.md) .

Una volta configurate, le richieste HTTP `x-ms-client-principal-name` autenticate includeranno le intestazioni e `x-ms-client-principal-id` contenenti rispettivamente il nome utente e l'ID utente dell'identità autenticata.

È possibile usare queste intestazioni nella configurazione dell'associazione *SignalRConnectionInfo* per creare connessioni autenticate. Di seguito è riportato C# un esempio di funzione Negotiate che usa l' `x-ms-client-principal-id` intestazione.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

È quindi possibile inviare messaggi a tale utente impostando la `UserId` proprietà di un messaggio SignalR.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Per informazioni su altri linguaggi, vedere le [associazioni del servizio Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) per le funzioni di riferimento di funzioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come sviluppare e configurare applicazioni del servizio SignalR senza server usando funzioni di Azure. Provare a creare un'applicazione manualmente usando una delle guide introduttive o le esercitazioni nella [pagina Panoramica del servizio SignalR](index.yml).