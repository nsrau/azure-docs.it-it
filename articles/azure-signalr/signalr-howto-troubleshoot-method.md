---
title: Procedura di risoluzione dei problemi per il servizio Azure SignalR
description: Informazioni su come risolvere i problemi relativi alla connettività e al recapito dei messaggi
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 4b0b85b08c3f813440d556c61ba5e290ac200049
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686917"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Come risolvere i problemi relativi alla connettività e al recapito dei messaggi

Questa guida presenta diversi modi per semplificare la diagnosi automatica per individuare la causa radice direttamente o per circoscrivere il problema. Il risultato della diagnosi automatica è utile anche quando viene segnalato a Microsoft per un'ulteriore analisi.

Prima di tutto, è necessario verificare dalla portale di Azure quale [ServiceMode](https://docs.microsoft.com/azure/azure-signalr/concept-service-mode) è il servizio Azure SignalR (noto anche come **ASRS**) configurato per.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* Per la `Default` modalità, vedere [risoluzione dei problemi in modalità predefinita](#default_mode_tsg)

* Per la `Serverless` modalità, vedere [risoluzione dei problemi in modalità senza server](#serverless_mode_tsg)

* Per la `Classic` modalità, vedere la pagina relativa alla [risoluzione dei problemi in modalità classica](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>Risoluzione dei problemi in modalità predefinita

Quando **ASRS** è in modalità *predefinita* , sono disponibili **tre** ruoli: *client*, *Server* e *servizio*:

* *Client*: il *client* sta per i client connessi a **ASRS**. Le connessioni permanenti che connettono client e **ASRS** sono denominate *connessioni client* in questa guida.

* *Server*: *Server* sta per il server che serve la negoziazione client e ospita la `Hub` logica SignalR. Le connessioni permanenti tra *Server* e **ASRS** sono denominate *connessioni server* in questa guida.

* *Service*: *Service* è il nome breve per **ASRS** in questa guida.

Per un'introduzione dettagliata dell'intera architettura e del flusso di lavoro, vedere [Internals of Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Esistono diversi modi che consentono di limitare il problema. 

* Se il problema si verifica nel modo giusto o in cui è possibile eseguire la riproduzione, il modo più semplice consiste nel visualizzare il traffico in corso. 

* Se il problema è difficile da riprodurre, le tracce e i log possono essere utili.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Come visualizzare il traffico e restringere il problema

L'acquisizione del traffico in corso è il modo più semplice per circoscrivere il problema. È possibile acquisire le [tracce di rete](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#network-traces) usando le opzioni descritte di seguito:

* [Raccogliere una traccia di rete con Fiddler](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#network-traces)

* [Raccogliere una traccia di rete con tcpdump](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Raccogliere una traccia di rete nel browser](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Richieste client

Per una connessione persistente SignalR, prima `/negotiate` di tutto al server app ospitato, quindi viene reindirizzato al servizio Azure SignalR e quindi viene stabilita la connessione permanente reale al servizio Azure SignalR. Per la procedura dettagliata, vedere [Internals of Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) .

Con la traccia della rete lato client, controllare la richiesta con esito negativo con il codice di stato e la risposta e cercare soluzioni nella [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/azure-signalr/signalr-howto-troubleshoot-guide).

#### <a name="server-requests"></a>Richieste server

Il server *SignalR mantiene la* *connessione al server* tra il *server* e il *servizio*. All'avvio, il server app avvia la connessione **WebSocket** al servizio Azure SignalR. Tutti i traffici client vengono instradati tramite il servizio Azure SignalR a tali *connessioni server* e quindi inviate al `Hub` . Quando si elimina una *connessione al server* , i client instradati a questa *connessione al server* saranno interessati. Azure SignalR SDK ha una logica "Riprova sempre" per riconnettere la *connessione al server* con un ritardo massimo di 1 minuto per ridurre al minimo l'effetto.

La *connessione al server* può essere eliminata a causa di un'instabilità della rete o di una manutenzione regolare del servizio Azure SignalR o degli aggiornamenti o della manutenzione del server app ospitato. Fino a quando sul lato client è presente il meccanismo di disconnessione/riconnessione, l'effetto è minimo, come qualsiasi lato client causato da disconnessione.

Visualizzazione della traccia di rete sul lato server per individuare il codice di stato e i dettagli dell'errore perché la *connessione al server* viene eliminata o rifiutata dal *servizio* e cerca la causa radice nella [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/azure-signalr/signalr-howto-troubleshoot-guide).


### <a name="how-to-add-logs"></a>Come aggiungere log

I log possono essere utili per diagnosticare i problemi e monitorare lo stato di esecuzione.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Come abilitare il log lato client

L'esperienza di registrazione lato client è esattamente identica a quando si usa SignalR self-hosted.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Abilitare la registrazione lato client per `ASP.NET Core SignalR`

* [Registrazione client JavaScript](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [Registrazione client .NET](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Abilitare la registrazione lato client per `ASP.NET SignalR`

* [Client .NET](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Abilitazione della traccia nei client Windows Phone 8](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Abilitazione della traccia nel client JavaScript](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Come abilitare il log lato server

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Abilitare la registrazione lato server per `ASP.NET Core SignalR`

La registrazione lato server per si `ASP.NET Core SignalR` integra con la `ILogger` [registrazione](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) basata su fornita nel `ASP.NET Core` Framework. È possibile abilitare la registrazione lato server usando `ConfigureLogging` , un esempio di utilizzo come indicato di seguito:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Le categorie di logger per Azure SignalR iniziano sempre con `Microsoft.Azure.SignalR` . Per abilitare i log dettagliati da Azure SignalR, configurare i prefissi precedenti in modo da `Information` livellare il **appsettings.js** nel file come riportato di seguito:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Controllare se sono stati registrati log degli errori o avvisi anomali. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Abilita tracce lato server per `ASP.NET SignalR`

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

Controllare se sono stati registrati log degli errori o avvisi anomali. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Come abilitare i log nel servizio Azure SignalR

È anche possibile [abilitare i log di diagnostica](https://docs.microsoft.com/azure/azure-signalr/signalr-tutorial-diagnostic-logs) per il servizio Azure SignalR. questi log forniscono informazioni dettagliate su tutte le connessioni connesse al servizio Azure SignalR.

<a name="serverless_mode_tsg"></a>

## <a name="serverless-mode-troubleshooting"></a>Risoluzione dei problemi in modalità senza server

Quando **ASRS** è in modalità senza *server* , solo **ASP.NET Core SignalR** supporta `Serverless` la modalità e **ASP.NET SignalR** non **NOT** supporta questa modalità.

Per diagnosticare i problemi di connettività in `Serverless` modalità, il modo più semplice consiste nel [visualizzare il traffico sul lato client](#view_traffic_client). È anche possibile abilitare i log [lato client](#add_logs_client) e i [log sul lato servizio](#add_logs_server) .

<a name="classic_mode_tsg"></a>

## <a name="classic-mode-troubleshooting"></a>Risoluzione dei problemi in modalità classica

`Classic` la modalità è obsoleta e non è consigliata per l'uso di. In questa modalità, il servizio Azure SignalR usa le *connessioni del server* connesso per determinare se il servizio corrente è in `default` modalità o in `serverless` modalità. Questo può causare problemi di connettività dei client intermedi perché, quando si verifica un calo improvviso di tutte le *connessioni al server* connesso, ad esempio a causa dell'instabilità della rete, Azure SignalR ritiene che sia ora impostato sulla `serverless` modalità e i client connessi durante questo periodo non verranno mai indirizzati al server applicazioni ospitato. Abilitare i [log sul lato servizio](#add_logs_server) e verificare se sono presenti client registrati come `ServerlessModeEntered` se fosse stato ospitato il server app, ma alcuni client non raggiungono mai il lato server dell'app. Se è presente, [interrompere le connessioni client](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) e consentire al riavvio dei client di aiutarlo.

La risoluzione dei problemi relativi `classic` alla connettività in modalità e al recapito dei messaggi è simile alla [risoluzione dei problemi in modalità predefinita](#default_mode_tsg).

## <a name="service-health"></a>Service Health

È possibile controllare l'integrità del servizio nell'API per l'integrità.

* Richiesta: GET `https://{instance_name}.service.signalr.net/api/v1/health`

* Codice di stato della risposta:
  * 200: integro.
  * 503: il servizio non è integro. È possibile:
    * Attendere alcuni minuti per il recupero automatico.
    * Verificare che l'indirizzo IP sia uguale all'indirizzo IP del portale.
    * Oppure riavviare l'istanza.
    * Se tutte le opzioni sopra riportate non funzionano, contattaci aggiungendo una nuova richiesta di supporto in portale di Azure.

Altre informazioni sul [ripristino di emergenza](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-disaster-recovery).

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come risolvere i problemi relativi alla connettività e al recapito dei messaggi. È inoltre possibile apprendere come gestire i problemi comuni. 

> [!div class="nextstepaction"]
> [Guida per la risoluzione dei problemi](./signalr-howto-troubleshoot-guide.md)