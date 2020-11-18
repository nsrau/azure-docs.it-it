---
title: Guida alla risoluzione dei problemi per Servizio Azure SignalR
description: Informazioni
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 5b8360134ace3852fb3c7fbe458ca2572f45b624
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663790"
---
# <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Questa guida è utile per fornire una guida alla risoluzione dei problemi in base ai problemi comuni che i clienti hanno soddisfatto e risolto negli ultimi anni.

## <a name="access-token-too-long"></a>Il token di accesso è troppo lungo

### <a name="possible-errors"></a>Possibili errori:

* Lato client `ERR_CONNECTION_`
* 414 - URI richiesta troppo lungo
* 413 Payload Too Large (413 Payload troppo grande)
* Il token di accesso non deve essere più lungo di 4K. 413 Entità della richiesta troppo grande

### <a name="root-cause"></a>Causa radice:

Per HTTP/2, la lunghezza massima per una singola intestazione è **4 K**, quindi se si usa browser per accedere al servizio di Azure, si verifica un errore `ERR_CONNECTION_` per questa limitazione.

Per i client HTTP/1.1 o C#, la lunghezza massima dell'URI è **12 k**, mentre la lunghezza massima dell'intestazione è **16 k**.

Con l'SDK versione **1.0.6** o successiva, `/negotiate` genererà un'eccezione `413 Payload Too Large` quando il token di accesso generato è maggiore di **4 K**.

### <a name="solution"></a>Soluzione:

Per impostazione predefinita, le attestazioni da `context.User.Claims` sono incluse durante la generazione del token **A** di accesso JWT in **ASRS**(Zure **s** ignal **R** **s** ervizio), in modo che le attestazioni vengano mantenute e possano essere passate da **ASRS** a `Hub` quando il client si connette a `Hub` .

In alcuni casi, `context.User.Claims` vengono utilizzate per archiviare numerose informazioni per il server applicazioni, la maggior parte delle quali non vengono utilizzate da `Hub` s ma da altri componenti.

Il token di accesso generato viene passato attraverso la rete e, per le connessioni WebSocket/SSE, i token di accesso vengono passati tramite stringhe di query. Quindi, come procedura consigliata, si consiglia di passare le attestazioni **necessarie** solo dal client tramite **ASRS** al server dell'app quando è necessario l'hub.

`ClaimsProvider`Per personalizzare le attestazioni, passare a **ASRS** all'interno del token di accesso.

Per ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

Per ASP.NET:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>TLS 1,2 obbligatorio

### <a name="possible-errors"></a>Possibili errori:

* Errore di ASP.NET "nessun server disponibile" [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "la connessione non è attiva. Impossibile inviare i dati al servizio". errore [#324](https://github.com/Azure/azure-signalr/issues/324)
* "Si è verificato un errore durante l'esecuzione della richiesta HTTP a https:// <API endpoint> . Questo errore potrebbe essere dovuto al fatto che il certificato del server non è configurato correttamente con HTTP.SYS nel caso HTTPS. Questo errore può anche essere causato da una mancata corrispondenza tra il client e il server e il binding di sicurezza.

### <a name="root-cause"></a>Causa radice:

Il servizio Azure supporta solo TLS 1.2 per motivi di sicurezza. Con .NET Framework è possibile che TLS 1.2 non sia il protocollo predefinito. Di conseguenza, non è possibile stabilire correttamente le connessioni server a ASRS.

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

1. Se questo errore può essere riprodotto localmente, deselezionare *Just My Code* e generare tutte le eccezioni CLR ed eseguire il debug del server app localmente per vedere quali eccezioni generano.
    * Deseleziona *Just My Code*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="Deseleziona Just My Code":::

    * Genera eccezioni CLR

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="Genera eccezioni CLR":::

    * Vedere le eccezioni generate durante il debug del codice sul lato server dell'app:
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="Eccezione generata":::

2. Per ASP.NET, è anche possibile aggiungere il codice seguente al `Startup.cs` per abilitare la traccia dettagliata e visualizzare gli errori del log.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>Soluzione:

Aggiungere il codice seguente all'avvio:
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>400 richiesta non valida restituita per le richieste client

### <a name="root-cause"></a>Causa radice

Controllare se la richiesta del client dispone di più `hub` stringhe di query. `hub` è un parametro di query mantenuto e 400 genera un'eccezione se il servizio ne rileva più di una `hub` nella query.

## <a name="401-unauthorized-returned-for-client-requests"></a>401 - Non autorizzata restituito per le richieste client

### <a name="root-cause"></a>Causa radice

Attualmente, il valore predefinito della durata del token JWT è 1 ora.

Per ASP.NET Core SignalR, quando si usa il tipo di trasporto WebSocket, è accettabile.

Per l'altro tipo di trasporto ASP.NET Core SignalR, SSE e il polling prolungato, questo significa che per impostazione predefinita la connessione può essere resa permanente per un'ora.

Per ASP.NET SignalR, il client invia una `/ping` richiesta KeepAlive al servizio di volta in volta, quando si verifica un `/ping` errore, il client **interrompe** la connessione e non si riconnette mai. Ciò significa che, per ASP.NET SignalR, la durata predefinita dei token rende la connessione dura **al massimo** 1 ora per tutto il tipo di trasporto.

### <a name="solution"></a>Soluzione

Per motivi di sicurezza, Extend TTL non è consigliato. Si consiglia di aggiungere la logica di riconnessione dal client per riavviare la connessione quando si verifica questo 401. Quando il client riavvia la connessione, verrà negoziata con il server app per ottenere nuovamente il token JWT e ottenere un token rinnovato.

Vedere [qui](#restart_connection) per come riavviare le connessioni client.

## <a name="404-returned-for-client-requests"></a>404 restituito per le richieste client

Per una connessione persistente SignalR, prima `/negotiate` di tutto al servizio Azure SignalR, quindi stabilisce la connessione reale al servizio Azure SignalR.

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

* Segue [come visualizzare le richieste in uscita](#view_request) per ottenere la richiesta dal client al servizio.
* Controllare l'URL della richiesta quando si verifica 404. Se l'URL è destinato all'app Web e simile a `{your_web_app}/hubs/{hubName}` , controllare se il client `SkipNegotiation` è `true` . Quando si usa Azure SignalR, il client riceve l'URL di reindirizzamento alla prima negoziazione con il server app. Il client **non** deve ignorare la negoziazione quando si usa Azure SignalR.
* Un altro 404 può verificarsi quando la richiesta di connessione viene gestita più di **5** secondi dopo la `/negotiate` chiamata a. Controllare il timestamp della richiesta del client e aprire un problema se la richiesta al servizio ha una risposta lenta.

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 restituito per la richiesta di riconnessione di ASP.NET SignalR

Per ASP.NET SignalR, quando la [connessione client](#client_connection_drop)viene rilasciata, si riconnette utilizzando la stessa `connectionId` per tre volte prima di arrestare la connessione. `/reconnect` può essere utile se la connessione viene eliminata a causa di problemi di rete intermittenti che `/reconnect` possono ristabilire correttamente la connessione permanente. In altre circostanze, ad esempio, la connessione client viene eliminata perché la connessione al server indirizzata viene eliminata, o il servizio SignalR presenta alcuni errori interni come il riavvio o il failover/distribuzione dell'istanza, la connessione non esiste più, quindi `/reconnect` restituisce `404` . Si tratta del comportamento previsto per `/reconnect` e dopo tre tentativi di arresto della connessione. Quando si interrompe la connessione, è consigliabile avere la logica di [riavvio della connessione](#restart_connection) .

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (numero eccessivo di richieste) restituito per le richieste client

429 restituisce se il numero di connessioni **simultanee** supera il limite.

Per le istanze **gratuite** , il limite del numero di connessioni **simultanee** è 20 per le istanze **standard** , il limite del numero di connessioni **simultanee** per **unità** è 1 K, che significa che Unit100 consente le connessioni simultanee 100-k.

Le connessioni includono connessioni client e server. fare clic [qui](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-messages-and-connections#how-connections-are-counted) per verificare la modalità di conteggio delle connessioni.

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 errore durante la negoziazione: il servizio Azure SignalR non è ancora connesso. riprovare più tardi.

### <a name="root-cause"></a>Causa radice

Questo errore viene segnalato quando non è presente alcuna connessione server al servizio Azure SignalR connesso.

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Abilitare la traccia sul lato server per trovare i dettagli dell'errore quando il server tenta di connettersi al servizio Azure SignalR.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Abilitare la registrazione lato server per ASP.NET Core SignalR

La registrazione lato server per ASP.NET Core SignalR si integra con la `ILogger` [registrazione](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) basata sulla ASP.NET Core Framework. È possibile abilitare la registrazione lato server usando `ConfigureLogging` , un esempio di utilizzo come indicato di seguito:
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Le categorie di logger per Azure SignalR iniziano sempre con `Microsoft.Azure.SignalR` . Per abilitare i log dettagliati da Azure SignalR, configurare i prefissi precedenti in modo da `Debug` livellare il **appsettings.js** nel file come riportato di seguito:
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Abilitare le tracce sul lato server per ASP.NET SignalR

Quando si usa la versione SDK >= `1.0.0` , è possibile abilitare le tracce aggiungendo quanto segue a `web.config` : ([Dettagli](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))
```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

## <a name="client-connection-drops"></a>Riduzioni connessione client

Quando il client è connesso ad Azure SignalR, la connessione permanente tra il client e Azure SignalR può talvolta essere eliminata per motivi diversi. Questa sezione descrive diverse possibilità causando tale calo della connessione e fornisce alcune indicazioni su come identificare la causa principale.

### <a name="possible-errors-seen-from-the-client-side"></a>Possibili errori visualizzati dal lato client

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>Causa radice:

Le connessioni client possono essere rilasciate in diverse circostanze:
* Quando `Hub` genera eccezioni con la richiesta in ingresso.
* Quando la connessione al server, che viene instradata dal client a, viene rilasciata, vedere la sezione seguente per informazioni dettagliate sulle [cadute di connessione del server](#server_connection_drop).
* Quando si verifica un problema di connettività di rete tra il servizio client e SignalR.
* Quando il servizio SignalR presenta alcuni errori interni come il riavvio dell'istanza, il failover, la distribuzione e così via.

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

1. Aprire il log lato server dell'app per verificare se si sono verificati anomalie
2. Controllare il registro eventi sul lato server dell'app per verificare se il server app è stato riavviato
3. Creare un problema per fornire l'intervallo di tempo e inviare tramite posta elettronica il nome della risorsa a noi


## <a name="client-connection-increases-constantly"></a>La connessione client aumenta costantemente

Il problema potrebbe essere causato da un utilizzo improprio della connessione client. Se un utente dimentica di arrestare/eliminare il client SignalR, la connessione rimane aperta.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Possibili errori visualizzati dalle metriche di SignalR nella sezione monitoraggio del menu portale di Azure Resource

Le connessioni client aumentano costantemente per molto tempo nelle metriche di Azure SignalR.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="Aumento costante della connessione client":::

### <a name="root-cause"></a>Causa radice:

`DisposeAsync`Non è mai possibile chiamare la connessione del client SignalR, la connessione viene aperta.

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

1. Controllare se il client SignalR **non** si chiude mai.

### <a name="solution"></a>Soluzione

Controllare se si chiude la connessione. Chiamare manualmente `HubConnection.DisposeAsync()` per arrestare la connessione dopo averla usata.

Ad esempio:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>Utilizzo comune della connessione client non corretto

#### <a name="azure-function-example"></a>Esempio di funzione di Azure 

Questo problema si verifica spesso quando un utente stabilisce una connessione del client SignalR nel metodo di funzione di Azure anziché renderlo un membro statico della classe function. È possibile che venga stabilita una sola connessione client, ma il numero di connessioni client aumenti costantemente in metriche che si trova nella sezione monitoraggio del menu portale di Azure risorse. tutte queste connessioni vengono eliminate solo dopo il riavvio della funzione di Azure o del servizio Azure SignalR. Questo perché per **ogni** richiesta, funzione di Azure crea **una** connessione client, se non si arresta la connessione client nel metodo di funzione, il client mantiene le connessioni attive al servizio Azure SignalR.

#### <a name="solution"></a>Soluzione

* Ricordarsi di chiudere la connessione client se si usano client SignalR in funzione di Azure o si usa il client SignalR come singleton.
* Invece di usare i client SignalR in funzione di Azure, è possibile creare i client SignalR in qualsiasi altra posizione e usare le [associazioni di funzioni di Azure per il servizio Azure SignalR](https://github.com/Azure/azure-functions-signalrservice-extension) per [negoziare](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) il client in Azure SignalR. Inoltre, è possibile utilizzare l'associazione per [inviare messaggi](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40). Gli esempi per negoziare il client e inviare messaggi sono disponibili [qui](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples). Altre informazioni sono disponibili [qui](https://github.com/Azure/azure-functions-signalrservice-extension).
* Quando si usano i client SignalR in funzioni di Azure, potrebbe esserci un'architettura migliore per lo scenario. Controllare se si progetta un'architettura senza server corretta. È possibile fare riferimento alle [applicazioni senza server in tempo reale con le associazioni del servizio SignalR in funzioni di Azure](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService).

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>Perdite di connessione al server

All'avvio del server app, in background, Azure SDK inizia a avviare le connessioni server al SignalR di Azure remoto. Come descritto in [Internals of Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md), Azure SignalR instrada i traffici dei client in ingresso a queste connessioni server. Una volta eliminata una connessione al server, verranno chiuse anche tutte le connessioni client che serviranno.

Poiché le connessioni tra il server app e il servizio SignalR sono connessioni permanenti, potrebbero verificarsi problemi di connettività di rete. Nell'SDK del server, abbiamo **sempre riconnesso** la strategia alle connessioni server. Come procedura consigliata, gli utenti possono anche aggiungere la logica di riconnessione continua ai client con un tempo di ritardo casuale per evitare richieste simultanee massicce al server.

A intervalli regolari, sono disponibili nuove versioni per il servizio Azure SignalR e talvolta l'applicazione di patch o aggiornamenti del sistema operativo a livello di Azure o l'interruzione occasionale dei servizi dipendenti. Questi possono comportare un breve periodo di ininterrottità del servizio, ma, a condizione che il lato client disponga del meccanismo di disconnessione/riconnessione, l'effetto è minimo come qualsiasi lato client ha causato la disconnessione.

In questa sezione vengono descritte diverse possibilità che comportano l'eliminazione della connessione al server e vengono fornite alcune indicazioni su come identificare la causa principale.

### <a name="possible-errors-seen-from-server-side"></a>Possibili errori visualizzati dal lato server:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>Causa radice:

**La** connessione del servizio Server è chiusa da **ASRS**(Zure **s** ignal **R** **s** ervizio).

### <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

1. Aprire il log lato server dell'app per verificare se si sono verificati anomalie
2. Controllare il registro eventi sul lato server dell'app per verificare se il server app è stato riavviato
3. Creare un problema per fornire l'intervallo di tempo e inviare tramite posta elettronica il nome della risorsa a noi

## <a name="tips"></a>Suggerimenti

<a name="view_request"></a>

* Come visualizzare la richiesta in uscita dal client
Prendere ASP.NET Core uno, ad esempio (ASP.NET uno è simile):
    * Dal browser:

        Usare Chrome come esempio, è possibile usare **F12** per aprire la finestra della console e passare alla scheda **rete** . Potrebbe essere necessario aggiornare la pagina utilizzando **F5** per acquisire la rete dall'inizio.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Rete visualizzazione Chrome":::

    * Da client C#:

        È possibile visualizzare i traffici Web locali usando [Fiddler](https://www.telerik.com/fiddler). I traffici WebSocket sono supportati a partire da Fiddler 4,5.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Rete di visualizzazione Fiddler" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* Come riavviare la connessione client
    
    Ecco i [codici di esempio](https://github.com/Azure/azure-signalr/tree/dev/samples) che contengono il riavvio della logica di connessione con la strategia di *ripetizione dei tentativi* :

    * [ASP.NET Core client C#](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core client JavaScript](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [Client C# ASP.NET](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [Client JavaScript ASP.NET](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

