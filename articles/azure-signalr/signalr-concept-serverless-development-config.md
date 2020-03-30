---
title: Sviluppare & configurare l'app Funzioni di Azure - Azure SignalRDevelop to configure Azure Functions app - Azure SignalR
description: Dettagli su come sviluppare e configurare applicazioni in tempo reale senza server usando Funzioni di Azure e Il servizio SignalR di AzureDetails on how to develop and configure serverless real-time applications using Azure Functions and Azure SignalR Service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523171"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR

Le applicazioni Funzioni di Azure possono [sfruttare le associazioni del servizio SignalR di Azure](../azure-functions/functions-bindings-signalr-service.md) per aggiungere funzionalità in tempo reale. Le applicazioni client usano SDK client disponibili in diverse lingue per connettersi al servizio SignalR di Azure e ricevere messaggi in tempo reale.

Questo articolo descrive i concetti per lo sviluppo e la configurazione di un'app per le funzioni di Azure integrata con SignalR Service.This article describes the concepts for developing and configuring an Azure Function app that is integrated with SignalR Service.

## <a name="signalr-service-configuration"></a>Configurazione del servizio SignalR

Il servizio SignalR di Azure può essere configurato in modalità diverse. Se usato con Funzioni di Azure, il servizio deve essere configurato in modalità *Senza server.*

Nel portale di Azure individuare la pagina *Impostazioni* della risorsa del servizio SignalR. Impostare la *modalità servizio* *su Senza server*.

![Modalità del servizio SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Sviluppo di Funzioni di Azure

Un'applicazione serverless in tempo reale compilata con Funzioni di Azure e con il servizio Azure SignalR richiede in genere due istanze di Funzioni di Azure:

* Una funzione "negotiate" che il client chiama per ottenere un token di accesso valido e del servizio SignalR e un URL dell'endpoint di servizio
* Una o più funzioni che inviano messaggi o gestiscono l'appartenenza ai gruppi

### <a name="negotiate-function"></a>funzione di negoziazione

A client application requires a valid access token to connect to Azure SignalR Service. Un token di accesso può essere anonimo o autenticato in un determinato ID utente. Le applicazioni del servizio SignalR senza server richiedono un endpoint HTTP denominato "negotiate" per ottenere un token e altre informazioni di connessione, ad esempio l'URL dell'endpoint del servizio SignalR.Serverless SignalR Service applications require an HTTP endpoint named "negotiate" to obtain a token and other connection information, such as the SignalR Service endpoint URL.

Usare una funzione di Azure attivata da HTTP e l'associazione di input *SignalRConnectionInfo* per generare l'oggetto informazioni di connessione. La funzione deve avere una `/negotiate`route HTTP che termina con .

Per ulteriori informazioni su come creare la funzione di negoziazione, vedere il riferimento all'associazione di input [ *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Per informazioni su come creare un token autenticato, vedere [Utilizzo dell'autenticazione](#using-app-service-authentication)del servizio app .

### <a name="sending-messages-and-managing-group-membership"></a>Invio di messaggi e gestione dell'appartenenza ai gruppi

Usare l'associazione di output SignalR per inviare messaggi ai client connessi al servizio SignalR di Azure.Use the *SignalR* output binding to send messages to clients connected to Azure SignalR Service. È possibile trasmettere messaggi a tutti i client oppure inviarli a un sottoinsieme di client autenticati con un ID utente specifico o aggiunti a un gruppo specifico.

Gli utenti possono essere aggiunti a uno o più gruppi. È inoltre possibile utilizzare l'associazione di output *SignalR* per aggiungere o rimuovere utenti da/verso i gruppi.

Per ulteriori informazioni, vedere il riferimento all'associazione di output [ *SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Hub SignalR

SignalR ha un concetto di "hub". Ogni connessione client e ogni messaggio inviato da Funzioni di Azure hanno come ambito un hub specifico. È possibile usare gli hub come un modo per separare le connessioni e i messaggi in spazi dei nomi logici.

## <a name="client-development"></a>Sviluppo client

SignalR client applications can leverage the SignalR client SDK in one of several languages to easily connect to and receive messages from Azure SignalR Service.

### <a name="configuring-a-client-connection"></a>Configurazione di una connessione client

Per connettersi al servizio SignalR, un client deve completare una negoziazione di connessione con esito positivo che consiste nei passaggi seguenti:To connect to SignalR Service, a client must complete a successful connection negotiation that consists of these steps:

1. Effettuare una richiesta all'endpoint HTTP *di negoziazione* descritto in precedenza per ottenere informazioni di connessione valide
1. Connettersi al servizio SignalR usando l'URL dell'endpoint del servizio e il token di accesso ottenuti dall'endpoint di *negoziazioneConnect* to SignalR Service using the service endpoint and access token obtained from the negotiate endpoint

Gli SDK del client SignalR contengono già la logica necessaria per eseguire l'handshake di negoziazione. Passare l'URL dell'endpoint `negotiate` di negoziazione, meno `HubConnectionBuilder`il segmento, all'indirizzo SDK. Ecco un esempio in JavaScript:Here is an example in JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Per convenzione, l'SDK aggiunge automaticamente `/negotiate` all'URL e lo utilizza per avviare la negoziazione.

> [!NOTE]
> Se si utilizza JavaScript/TypeScript SDK in un browser, è necessario abilitare la condivisione delle [risorse tra origini (CORS)](#enabling-cors) nell'app per le funzioni.

Per ulteriori informazioni su come utilizzare SignalR client SDK, fare riferimento alla documentazione per la lingua:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Invio di messaggi da un client al servizio

Anche se SignalR SDK consente alle applicazioni client di richiamare la logica back-end in un hub SignalR, questa funzionalità non è ancora supportata quando si usa SignalR Service con Funzioni di Azure.Although the SignalR SDK allows client applications to invoke backend logic in a SignalR hub, this functionality is not yet supported when you use SignalR Service with Azure Functions. Usare le richieste HTTP per richiamare funzioni di Azure.Use HTTP requests to invoke Azure Functions.

## <a name="azure-functions-configuration"></a>Configurazione di Funzioni di AzureAzure Functions configuration

Le app per funzioni di Azure che si integrano con il servizio SignalR di Azure possono essere distribuite come qualsiasi app Function di Azure tipica, usando tecniche come [distribuzione continua,](../azure-functions/functions-continuous-deployment.md) [distribuzione zip](../azure-functions/deployment-zip-push.md)ed [eseguito dal pacchetto.](../azure-functions/run-functions-from-deployment-package.md)

Tuttavia, esistono un paio di considerazioni speciali per le app che usano i binding del servizio SignalR.However, there are a couple of special considerations for apps that use the SignalR Service bindings. Se il client viene eseguito in un browser, CORS deve essere abilitato. E se l'app richiede l'autenticazione, è possibile integrare l'endpoint di negoziazione con l'autenticazione del servizio app.

### <a name="enabling-cors"></a>Abilitazione della condivisione CORS

Il client JavaScript/TypeScript effettua richieste HTTP alla funzione di negoziazione per avviare la negoziazione della connessione. Quando l'applicazione client è ospitata in un dominio diverso dall'app Funzione di Azure, la condivisione delle risorse tra origini (CORS) deve essere abilitata nell'app Function o il browser bloccherà le richieste.

#### <a name="localhost"></a>Localhost

Quando si esegue l'app per le `Host` funzioni nel computer locale, è possibile aggiungere una sezione a *local.settings.json* per abilitare CORS. Nella `Host` sezione aggiungere due proprietà:

* `CORS`- immettere l'URL di base che è l'origine dell'applicazione client
* `CORSCredentials`- impostarlo `true` su per consentire le richieste "withCredentials"

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

#### <a name="cloud---azure-functions-cors"></a>Cloud - Funzioni di Azure CORS

Per abilitare CORS in un'app delle funzioni di Azure, passare alla schermata di configurazione CORS nella scheda *Funzionalità della piattaforma* dell'app Funzioni nel portale di Azure.To enable CORS on an Azure Function app, go to the CORS configuration screen under the Platform features tab of your Function app in the Azure portal.

> [!NOTE]
> La configurazione CORS non è ancora disponibile nel piano di consumo Linux di Funzioni di Azure.CORS configuration is not yet available in Azure Functions Linux Consumption plan. Usare [Gestione API](#cloud---azure-api-management) di Azure per abilitare CORS.

CORS con Access-Control-Allow-Credentials deve essere abilitato per il client SignalR per chiamare la funzione di negoziazione. Selezionare la casella di controllo per attivarla.

Nella sezione *Origini consentite* aggiungere una voce con l'URL di base di origine dell'applicazione Web.

![Configurazione di CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud - Gestione API di Azure

Gestione API di Azure offre un gateway API che aggiunge funzionalità ai servizi back-end esistenti. Puoi usarlo per aggiungere CORS alla tua app per le funzioni. Offre un livello di consumo con prezzi pay-per-action e una sovvenzione mensile gratuita.

Fare riferimento alla documentazione di Gestione API per informazioni su come [importare un'app per](../api-management/import-function-app-as-api.md)le funzioni di Azure. Una volta importato, è possibile aggiungere un criterio in ingresso per abilitare CORS con il supporto Access-Control-Allow-Credentials.

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

Configurare i client SignalR per l'utilizzo dell'URL di gestione API.

### <a name="using-app-service-authentication"></a>Uso dell'autenticazione del servizio appUsing App Service Authentication

Funzioni di Azure include l'autenticazione incorporata, che supporta provider popolari come Facebook, Twitter, account Microsoft, Google e Azure Active Directory. Questa funzionalità può essere integrata con l'associazione *SignalRConnectionInfo* per creare connessioni al servizio SignalR di Azure che sono state autenticate a un ID utente. L'applicazione può inviare messaggi usando l'associazione di output *SignalR* destinata a tale ID utente.

Nel portale di Azure, nella scheda *Funzionalità piattaforma* dell'app Per le funzioni, aprire la finestra Impostazioni *di autenticazione/autorizzazione.* Seguire la documentazione per [l'autenticazione](../app-service/overview-authentication-authorization.md) del servizio app per configurare l'autenticazione usando un provider di identità di propria scelta.

Una volta configurate, le `x-ms-client-principal-name` `x-ms-client-principal-id` richieste HTTP autenticate includeranno e le intestazioni contenenti rispettivamente il nome utente e l'ID utente dell'identità autenticata.

È possibile utilizzare queste intestazioni nella configurazione di binding *SignalRConnectionInfo* per creare connessioni autenticate. Di seguito è riportato un `x-ms-client-principal-id` esempio di funzione di negoziazione in C, che usa l'intestazione.

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

È quindi possibile inviare messaggi `UserId` a tale utente impostando la proprietà di un messaggio SignalR.You can then send messages to that user by setting the property of a SignalR message.

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

Per altre informazioni su altri linguaggi, vedere le associazioni del servizio SignalR di Azure per funzioni di Azure.For information on other languages, see [the Azure SignalR Service bindings](../azure-functions/functions-bindings-signalr-service.md) for Azure Functions reference.

## <a name="next-steps"></a>Passaggi successivi

In this article, you have learned how to develop and configure serverless SignalR Service applications using Azure Functions. Provare a creare un'applicazione manualmente utilizzando una delle guide introduttive o esercitazioni nella pagina panoramica del [servizio SignalR](index.yml).