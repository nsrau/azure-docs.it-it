---
title: Sviluppare e configurare le applicazioni di servizio SignalR funzioni di Azure
description: Informazioni dettagliate su come sviluppare e configurare applicazioni in tempo reale senza server con funzioni di Azure e servizio Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569156"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Lo sviluppo di funzioni di Azure e configurazione con il servizio Azure SignalR

Le applicazioni le funzioni di Azure possono sfruttare il [associazioni di servizio Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) per aggiungere le funzionalità in tempo reale. Le applicazioni client usano gli SDK del client disponibili in varie lingue per connettersi al servizio Azure SignalR e ricevere messaggi in tempo reale.

Questo articolo descrive i concetti di sviluppo e configurazione di un'app per le funzioni di Azure integrato con SignalR Service.

## <a name="signalr-service-configuration"></a>Configurazione di SignalR Service

Azure SignalR Service può essere configurato in modalità diverse. Se usato con funzioni di Azure, il servizio deve essere configurato *Serverless* modalità.

Nel portale di Azure, individuare il *impostazioni* pagina della risorsa SignalR Service. Impostare il *Service mode* al *Serverless*.

![Modalità di servizio SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Sviluppo di funzioni di Azure

Un'applicazione in tempo reale senza server compilata con funzioni di Azure e servizio Azure SignalR in genere richiede due funzioni di Azure:

* Una funzione "negotiate" che il client chiama per ottenere un valido di SignalR Service token di accesso e URL dell'endpoint di servizio
* Una o più funzioni che inviano messaggi o gestire l'appartenenza al gruppo

### <a name="negotiate-function"></a>negoziazione (funzione)

Un'applicazione client richiede un token di accesso valido per la connessione al servizio Azure SignalR. Un token di accesso può essere anonimi o autenticati a un ID utente specificato. Applicazioni senza server di SignalR Service richiedono che un endpoint HTTP denominata "negotiate" per ottenere un token e altre informazioni di connessione, ad esempio l'URL dell'endpoint SignalR Service.

Usare HTTP attivato funzione di Azure e il *SignalRConnectionInfo* associazione per generare un oggetto informazioni di connessione di input. La funzione deve contenere una route HTTP che termina con `/negotiate`.

Per altre informazioni su come creare la funzione negotiate, vedere la [ *SignalRConnectionInfo* riferimento dell'associazione di input](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Per altre informazioni su come creare un token autenticato, fare riferimento a [usando l'autenticazione del servizio App](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>L'invio di messaggi e la gestione di appartenenza al gruppo

Usare la *SignalR* associazione di output per inviare messaggi ai client connessi al servizio Azure SignalR. È possibile trasmettere i messaggi a tutti i client oppure è possibile inviarli a un sottoinsieme di client vengono autenticati con un ID utente specifico o sono stati aggiunti a un gruppo specifico.

Gli utenti possono essere aggiunti a uno o più gruppi. È anche possibile usare la *SignalR* binding per aggiungere o rimuovere utenti da e verso i gruppi di output.

Per altre informazioni, vedere la [ *SignalR* riferimento dell'associazione di output](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Hub SignalR

SignalR è un concetto di "hub". Ogni connessione di client e ogni messaggio inviato da funzioni di Azure ha come ambito un hub specifico. È possibile utilizzare hub come un modo per separare le connessioni e i messaggi in spazi dei nomi logici.

## <a name="client-development"></a>Sviluppo client

Le applicazioni client SignalR possono sfruttare il SDK in uno dei numerosi linguaggi per connettersi con facilità e ricevere messaggi dal servizio Azure SignalR del client SignalR.

### <a name="configuring-a-client-connection"></a>Configurare una connessione client

Per connettersi a SignalR Service, un client deve completare una negoziazione di connessione riuscita che prevede questi passaggi:

1. Effettua una richiesta per il *negoziare* endpoint HTTP illustrata in precedenza per ottenere le informazioni di connessione valida
1. Connettersi a SignalR Service usando l'URL dell'endpoint servizio e token di accesso ottenuto dal *negoziare* endpoint

Gli SDK del client SignalR contengono già la logica necessaria per eseguire l'handshake di negoziazione. Passare l'URL dell'endpoint negotiate, meno il `negotiate` segmento, il SDK `HubConnectionBuilder`. Di seguito è riportato un esempio in JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Per convenzione, il SDK aggiunge automaticamente `/negotiate` all'URL e lo usa per avviare la negoziazione.

> [!NOTE]
> Se si usa il SDK JavaScript/TypeScript in un browser, è necessario [cross-origin resource sharing (CORS) abilitare](#enabling-cors) su App per le funzioni.

Per altre informazioni su come usare il SDK del client SignalR, vedere la documentazione per la lingua:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>L'invio di messaggi da un client al servizio

Anche se il SDK di SignalR consente alle applicazioni richiamare logica di back-end in un hub SignalR client, questa funzionalità non è supportata ancora quando si usa SignalR Service con funzioni di Azure. Richieste usano HTTP per richiamare funzioni di Azure.

## <a name="azure-functions-configuration"></a>Configurazione di funzioni di Azure

Azure App per le funzioni che si integrano con il servizio Azure SignalR può essere distribuita come qualsiasi tipiche app per le funzioni di Azure, usando tecniche come [distribuzione continua](../azure-functions/functions-continuous-deployment.md), [zip distribuzione](../azure-functions/deployment-zip-push.md)e [viene eseguita dal pacchetto](../azure-functions/run-functions-from-deployment-package.md).

Tuttavia, esistono un paio di considerazioni speciali per le app che usano le associazioni di SignalR Service. Se il client viene eseguito in un browser, è necessario abilitare CORS. E se l'app richiede l'autenticazione, è possibile integrare l'endpoint negotiate con l'autenticazione del servizio App.

### <a name="enabling-cors"></a>Abilitazione della condivisione CORS

Il client JavaScript/TypeScript effettua le richieste HTTP per la funzione negotiate per avviare la negoziazione della connessione. Quando l'applicazione client è ospitato in un dominio diverso da app per le funzioni di Azure, cross-origin resource sharing (CORS) deve essere abilitato in app per le funzioni o il browser si bloccherà le richieste.

#### <a name="localhost"></a>Localhost

Quando si esegue l'app per le funzioni nel computer locale, è possibile aggiungere un `Host` alla sezione *Local* per abilitare CORS. Nel `Host` sezione, aggiungere due proprietà:

* `CORS` -Immettere l'URL di base che rappresenta l'origine dell'applicazione client
* `CORSCredentials` -impostarla su `true` per consentire le richieste di "withCredentials"

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

#### <a name="azure"></a>Azure

Per abilitare CORS in un'app per le funzioni di Azure, passare alla schermata di configurazione di CORS con il *funzionalità della piattaforma* scheda dell'app per le funzioni nel portale di Azure.

Per il client SignalR chiamare la funzione negotiate, è necessario abilitare CORS con accesso-controllo-Allow-Credentials. Selezionare la casella di controllo per abilitarlo.

Nel *origini consentite* sezione, aggiungere una voce con l'URL di base di origine dell'applicazione web.

![Configurazione di CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>Usando l'autenticazione del servizio App

Funzioni di Azure offre l'autenticazione predefinita, il supporto di popolari provider, ad esempio Facebook, Twitter, Account Microsoft, Google e Azure Active Directory. Questa funzionalità può essere integrata con il *SignalRConnectionInfo* associazione per creare connessioni al servizio Azure SignalR, che sono state autenticate a un ID utente. L'applicazione può inviare messaggi mediante il *SignalR* output associazione che sono destinati a tale ID utente.

Nel portale di Azure, nella finestra dell'app per le funzioni *funzionalità della piattaforma* scheda, aprire il *autenticazione/autorizzazione* finestra delle impostazioni. Seguire la documentazione per [l'autenticazione del servizio App](../app-service/overview-authentication-authorization.md) per configurare l'autenticazione tramite un provider di identità preferito.

Una volta configurato, le richieste autenticate HTTP includerà `x-ms-client-principal-name` e `x-ms-client-principal-id` intestazioni contenente il nome utente dell'identità autenticata e l'ID utente, rispettivamente.

È possibile utilizzare queste intestazioni nel *SignalRConnectionInfo* configurazione dell'associazione per creare le connessioni autenticate. Di seguito è riportato un esempio C# negoziare la funzione che usa il `x-ms-client-principal-id` intestazione.

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

È quindi possibile inviare messaggi a tale utente impostando il `UserId` proprietà di un messaggio SignalR.

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

Per informazioni su altri linguaggi, vedere la [associazioni di servizio Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) per fare riferimento a funzioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, si è appreso come sviluppare e configurare applicazioni SignalR Service senza server mediante funzioni di Azure. Provare a creare un'applicazione utilizzando una delle guide introduttive o esercitazioni sul [pagina di panoramica di SignalR Service](index.yml).