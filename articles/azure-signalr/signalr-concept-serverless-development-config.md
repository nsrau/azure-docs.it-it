---
title: Sviluppare & configurare l'app funzioni di Azure-Azure SignalR
description: Informazioni dettagliate su come sviluppare e configurare applicazioni in tempo reale senza server usando funzioni di Azure e il servizio Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ca7d28e3f964d486d9f860c355e88132ebb897a2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327647"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Sviluppo e configurazione di Funzioni di Azure e con il Servizio Azure SignalR

Le applicazioni di funzioni di Azure possono sfruttare le [associazioni del servizio Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) per aggiungere funzionalità in tempo reale. Le applicazioni client usano gli SDK client disponibili in diverse lingue per connettersi al servizio Azure SignalR e ricevere messaggi in tempo reale.

Questo articolo descrive i concetti per lo sviluppo e la configurazione di un'app per le funzioni di Azure integrata con il servizio SignalR.

## <a name="signalr-service-configuration"></a>Configurazione del servizio SignalR

Il servizio Azure SignalR può essere configurato in modalità diverse. Se usato con funzioni di Azure, il servizio deve essere configurato in modalità senza *Server* .

Nella portale di Azure individuare la pagina delle *Impostazioni* della risorsa del servizio SignalR. Impostare la *modalità del servizio* su senza *Server*.

![Modalità del servizio SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Sviluppo di Funzioni di Azure

Un'applicazione serverless in tempo reale compilata con Funzioni di Azure e con il servizio Azure SignalR richiede in genere due istanze di Funzioni di Azure:

* Una funzione "negotiate" che il client chiama per ottenere un token di accesso valido e del servizio SignalR e un URL dell'endpoint di servizio
* Una o più funzioni che gestiscono i messaggi dal servizio SignalR e inviano messaggi o gestiscono l'appartenenza al gruppo

### <a name="negotiate-function"></a>Negotiate-funzione

Un'applicazione client richiede un token di accesso valido per la connessione al servizio Azure SignalR. Un token di accesso può essere anonimo o autenticato a un ID utente specificato. Le applicazioni del servizio SignalR senza server richiedono un endpoint HTTP denominato "Negotiate" per ottenere un token e altre informazioni di connessione, ad esempio l'URL dell'endpoint del servizio SignalR.

Usare una funzione di Azure attivata da HTTP e l'associazione di input di *SignalRConnectionInfo* per generare l'oggetto informazioni di connessione. La funzione deve avere una route HTTP che termina con `/negotiate` .

Con il [modello basato su classe](#class-based-model) in C#, non è necessaria l'associazione di input di *SignalRConnectionInfo* e l'aggiunta di attestazioni personalizzate è molto più semplice. Vedere l' [esperienza negoziata nel modello basato su classi](#negotiate-experience-in-class-based-model)

Per ulteriori informazioni su come creare la funzione Negotiate, vedere la Guida di [riferimento dell'associazione di input *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Per informazioni su come creare un token autenticato, vedere l'articolo relativo all' [uso dell'autenticazione del servizio app](#using-app-service-authentication).

### <a name="handle-messages-sent-from-signalr-service"></a>Gestire i messaggi inviati dal servizio SignalR

Usare l'associazione di *trigger SignalR* per gestire i messaggi inviati dal servizio SignalR. È possibile attivare l'attivazione quando i client inviano messaggi o i client vengono connessi o disconnessi.

Per altre informazioni, vedere riferimento dell'associazione di [ *trigger SignalR* ](../azure-functions/functions-bindings-signalr-service-trigger.md).

È anche necessario configurare l'endpoint della funzione come upstream, in modo che il servizio attiverà la funzione in cui è presente un messaggio dal client. Per ulteriori informazioni su come configurare upstream, consultare questo [documento](concept-upstream.md).

### <a name="sending-messages-and-managing-group-membership"></a>Invio di messaggi e gestione dell'appartenenza al gruppo

Usare l'associazione di output di *SignalR* per inviare messaggi ai client connessi al servizio Azure SignalR. È possibile trasmettere messaggi a tutti i client oppure è possibile inviarli a un subset di client autenticati con un ID utente specifico o aggiunti a un gruppo specifico.

Gli utenti possono essere aggiunti a uno o più gruppi. È anche possibile usare l'associazione di output di *SignalR* per aggiungere o rimuovere utenti da e verso gruppi.

Per altre informazioni, vedere riferimento dell'associazione di output di [ *SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Hub SignalR

SignalR ha un concetto di "hub". Ogni connessione client e ogni messaggio inviato da funzioni di Azure hanno come ambito un hub specifico. È possibile utilizzare gli hub come un modo per separare le connessioni e i messaggi in spazi dei nomi logici.

## <a name="class-based-model"></a>Modello basato su classi

Il modello basato su classe è dedicato a C#. Con il modello basato su classe può essere presente un'esperienza di programmazione lato server SignalR coerente. Sono disponibili le funzionalità seguenti.

* Minor lavoro di configurazione: il nome della classe viene usato come `HubName` , il nome del metodo viene usato come `Event` e l'oggetto `Category` viene deciso automaticamente in base al nome del metodo.
* Binding di parametri automatici: non `ParameterNames` è necessario né né attributo `[SignalRParameter]` . I parametri sono associati automaticamente agli argomenti del metodo di funzione di Azure in ordine.
* Semplice esperienza di output e negoziazione.

I codici seguenti illustrano queste funzionalità:

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

Tutte le funzioni che vogliono sfruttare il modello basato su classi devono essere il metodo della classe che eredita da **ServerlessHub**. Il nome della classe `SignalRTestHub` nell'esempio è il nome dell'hub.

### <a name="define-hub-method"></a>Definisci metodo Hub

Tutti i metodi dell'hub **devono** avere un argomento `InvocationContext` decorato dall' `[SignalRTrigger]` attributo e usare un costruttore senza parametri. Il **nome del metodo** viene quindi trattato come un **evento**Parameter.

Per impostazione predefinita, `category=messages` ad eccezione del nome del metodo è uno dei nomi seguenti:

* **OnConnected**: trattato come `category=connections, event=connected`
* **Disconnected**: trattato come `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>Esperienza di associazione di parametri

Nel modello basato su classi, `[SignalRParameter]` non è necessario perché tutti gli argomenti sono contrassegnati come `[SignalRParameter]` per impostazione predefinita, ad eccezione di una delle situazioni seguenti:

* L'argomento è decorato da un attributo di associazione.
* Il tipo dell'argomento è `ILogger` o `CancellationToken`
* L'argomento è decorato dall'attributo `[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>Negotiate Experience in modello basato su classi

Invece di usare l'associazione di input SignalR `[SignalR]` , la negoziazione nel modello basato su classi può essere più flessibile. La classe base `ServerlessHub` ha un metodo

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

Questa funzionalità viene personalizzata dall'utente `userId` o `claims` durante l'esecuzione della funzione.

## <a name="use-signalrfilterattribute"></a>Utilizzare `SignalRFilterAttribute`.

L'utente può ereditare e implementare la classe astratta `SignalRFilterAttribute` . Se le eccezioni vengono generate in `FilterAsync` , `403 Forbidden` verranno inviate nuovamente ai client.

Nell'esempio seguente viene illustrato come implementare un filtro del cliente che consente solo `admin` a di richiamare `broadcast` .

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

Utilizzare l'attributo per autorizzare la funzione.

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>Sviluppo client

Le applicazioni client SignalR possono sfruttare l'SDK client di SignalR in uno dei diversi linguaggi per connettersi e ricevere messaggi dal servizio Azure SignalR in modo semplice.

### <a name="configuring-a-client-connection"></a>Configurazione di una connessione client

Per connettersi al servizio SignalR, un client deve completare una negoziazione di connessione riuscita che prevede i passaggi seguenti:

1. Eseguire una richiesta all'endpoint HTTP *Negotiate* illustrato in precedenza per ottenere informazioni di connessione valide
1. Connettersi al servizio SignalR usando l'URL dell'endpoint del servizio e il token di accesso ottenuti dall'endpoint *Negotiate*

Gli SDK del client SignalR contengono già la logica necessaria per eseguire l'handshake di negoziazione. Passare l'URL dell'endpoint Negotiate, meno il `negotiate` segmento, all'SDK `HubConnectionBuilder` . Di seguito è riportato un esempio in JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Per convenzione, l'SDK aggiunge automaticamente `/negotiate` all'URL e lo usa per iniziare la negoziazione.

> [!NOTE]
> Se si usa JavaScript/TypeScript SDK in un browser, è necessario [abilitare la condivisione di risorse tra le origini (CORS)](#enabling-cors) nel app per le funzioni.

Per ulteriori informazioni su come utilizzare SignalR client SDK, consultare la documentazione relativa alla lingua:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Invio di messaggi da un client al servizio

Se è stato configurato [upstream](concept-upstream.md) per la risorsa SignalR, è possibile inviare messaggi dal client alle funzioni di Azure usando qualsiasi client SignalR. Di seguito è riportato un esempio in JavaScript:

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Configurazione di funzioni di Azure

Le app per le funzioni di Azure che si integrano con il servizio Azure SignalR possono essere distribuite come qualsiasi tipica app per le funzioni di Azure, usando tecniche come la [distribuzione continua](../azure-functions/functions-continuous-deployment.md), la [distribuzione zip](../azure-functions/deployment-zip-push.md)e l' [esecuzione da un pacchetto](../azure-functions/run-functions-from-deployment-package.md).

Tuttavia, esistono alcune considerazioni speciali per le app che usano le associazioni del servizio SignalR. Se il client viene eseguito in un browser, è necessario abilitare CORS. Se l'app richiede l'autenticazione, è possibile integrare l'endpoint Negotiate con l'autenticazione del servizio app.

### <a name="enabling-cors"></a>Abilitazione della condivisione CORS

Il client JavaScript/TypeScript esegue richieste HTTP alla funzione Negotiate per avviare la negoziazione della connessione. Quando l'applicazione client è ospitata in un dominio diverso dall'app per le funzioni di Azure, la condivisione di risorse tra le origini (CORS) deve essere abilitata nell'app per le funzioni oppure il browser bloccherà le richieste.

#### <a name="localhost"></a>Localhost

Quando si esegue l'app per le funzioni nel computer locale, è possibile aggiungere una `Host` sezione *local.settings.json* per abilitare CORS. Nella `Host` sezione aggiungere due proprietà:

* `CORS` -Immettere l'URL di base che rappresenta l'origine dell'applicazione client
* `CORSCredentials` -impostarla su `true` per consentire le richieste "withCredentials"

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

Nella sezione *origini consentite* aggiungere una voce con l'URL di base di origine dell'applicazione Web.

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

Funzioni di Azure offre l'autenticazione incorporata, che supporta i provider più diffusi, ad esempio Facebook, Twitter, account Microsoft, Google e Azure Active Directory. Questa funzionalità può essere integrata con l'associazione *SignalRConnectionInfo* per creare connessioni al servizio Azure SignalR autenticato a un ID utente. L'applicazione può inviare messaggi usando l'associazione di output *SignalR* destinata a tale ID utente.

Nella scheda *funzionalità della piattaforma* dell'app per le funzioni della portale di Azure aprire la finestra impostazioni di *autenticazione/autorizzazione* . Per configurare l'autenticazione tramite un provider di identità di propria scelta, seguire la documentazione per l' [autenticazione del servizio app](../app-service/overview-authentication-authorization.md) .

Una volta configurate, le richieste HTTP autenticate includeranno `x-ms-client-principal-name` `x-ms-client-principal-id` le intestazioni e contenenti rispettivamente il nome utente e l'ID utente dell'identità autenticata.

È possibile usare queste intestazioni nella configurazione dell'associazione *SignalRConnectionInfo* per creare connessioni autenticate. Di seguito è riportato un esempio di funzione di negoziazione C# che usa l' `x-ms-client-principal-id` intestazione.

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