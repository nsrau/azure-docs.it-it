---
title: Monitoraggio delle dipendenze in Azure Application Insights | Microsoft Docs
description: Monitorare le chiamate di dipendenza dall'applicazione Web locale o Microsoft Azure con Application Insights.Monitor dependency calls from your on-premises or Microsoft Azure web application with Application Insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1e30d8036c1fc624d39f027f38e314c6c57360f6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731504"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rilevamento delle dipendenze in Azure Application InsightsDependenc y Tracking in Azure Application Insights 

Una *dipendenza* è un componente esterno chiamato dall'applicazione. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. [Application Insights](../../azure-monitor/app/app-insights-overview.md) misura la durata delle chiamate alle dipendenze, indipendentemente dal fatto che l'errore non riesca o meno, insieme a informazioni aggiuntive come il nome della dipendenza e così via. È possibile analizzare chiamate di dipendenza specifiche e correlarle a richieste ed eccezioni.

## <a name="automatically-tracked-dependencies"></a>Dipendenze rilevate automaticamente

Gli SDK di Application Insights per .NET `DependencyTrackingTelemetryModule` e .NET Core vengono fornito con il quale è un modulo di telemetria che raccoglie automaticamente le dipendenze. Questa raccolta di dipendenze viene abilitata automaticamente per [le applicazioni ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e ASP.NET [Core,](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) se configurata in base ai documenti ufficiali collegati. `DependencyTrackingTelemetryModule` viene spedito come [pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet e viene portato `Microsoft.ApplicationInsights.Web` automaticamente `Microsoft.ApplicationInsights.AspNetCore`quando si utilizza uno dei pacchetti NuGet o .

 `DependencyTrackingTelemetryModule`attualmente tiene traccia automaticamente delle seguenti dipendenze:

|Dependencies |Dettagli|
|---------------|-------|
|Http/Https (informazioni in inglese) | Chiamate http/https locali o remote |
|Chiamate WCFWCF calls| Rilevati automaticamente solo se vengono utilizzati binding basati su Http.|
|SQL | Chiamate effettuate con `SqlClient`. Vedere [questo](#advanced-sql-tracking-to-get-full-sql-query) per l'acquisizione di query SQL.  |
|[Archiviazione di Azure (BLOB, tabella, coda)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chiamate effettuate con il client di archiviazione di Azure.Calls made with Azure Storage Client. |
|[EventHub Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versione 1.1.0 e successive. |
|[SDK Client di ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versione 3.0.0 e successive. |
|Azure Cosmos DB | Monitorato automaticamente solo se viene utilizzato HTTP/HTTPS. La modalità TCP non verrà acquisita da Application Insights. |

Se manca una dipendenza o si utilizza un SDK diverso, assicurarsi che sia presente nell'elenco delle [dipendenze raccolte automaticamente.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) Se la dipendenza non viene raccolta automaticamente, è comunque possibile tenere traccia manualmente con una chiamata di [dipendenza di traccia.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurare il rilevamento automatico delle dipendenze nelle app console

Per tenere traccia automaticamente delle dipendenze dalle app `Microsoft.ApplicationInsights.DependencyCollector`console `DependencyTrackingTelemetryModule` .NET, installare il pacchetto Nuget e inizializzare come segue:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Per le app console di .NET Core TelemetryConfiguration.Active è obsoleto. Fare riferimento alle indicazioni nella [documentazione](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) del servizio di lavoro e alla [documentazione](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) sul monitoraggio di ASP.NET Core

### <a name="how-automatic-dependency-monitoring-works"></a>Come funziona il monitoraggio automatico delle dipendenze?

Le dipendenze vengono raccolte automaticamente utilizzando una delle tecniche seguenti:

* Utilizzo della strumentazione del codice byte intorno ai metodi di selezione. (InstrumentationEngine da StatusMonitor o Estensione di App Web di Azure)
* Callback EventSource
* Callback DiagnosticSource (negli SDK di .NET/.NET Core più recenti)

## <a name="manually-tracking-dependencies"></a>Monitoraggio manuale delle dipendenze

Di seguito sono riportati alcuni esempi di dipendenze, che non vengono raccolte automaticamente e che pertanto richiedono il rilevamento manuale.

* viene tenuta automaticamente traccia di Azure Cosmos DB solo se è usato [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking). La modalità TCP non verrà acquisita da Application Insights.
* Redis

Per tali dipendenze non raccolte automaticamente dall'SDK, è possibile tenere traccia manualmente usando [l'API TrackDependency](api-custom-events-metrics.md#trackdependency) usata dai moduli di raccolta automatica standard.

Ad esempio, se si compila il codice con un assembly non scritto personalmente, sarà possibile misurare il tempo necessario per tutte le chiamate all'assembly, per individuare il contributo dell'assembly ai tempi di risposta. Per visualizzare i dati nei grafici relativi alle dipendenze in Application Insights, inviarli mediante `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

In alternativa, `TelemetryClient` fornisce `StartOperation` `StopOperation` metodi di estensione e che possono essere utilizzati per tenere traccia manualmente delle dipendenze, come illustrato [di seguito](custom-operations-tracking.md#outgoing-dependencies-tracking)

Se si desidera disattivare il modulo di rilevamento delle dipendenze standard, rimuovere il riferimento a DependencyTrackingTelemetryModule in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) per ASP.NET applicazioni. Per le applicazioni ASP.NET Core, seguire le istruzioni [riportate di seguito.](asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="tracking-ajax-calls-from-web-pages"></a>Monitoraggio delle chiamate AJAX dalle pagine Web

Per le pagine Web, Application Insights JavaScript SDK raccoglie automaticamente le chiamate AJAX come dipendenze.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rilevamento SQL avanzato per ottenere query SQL completeAdvanced SQL tracking to get full SQL Query

Per le chiamate SQL, il nome del server e del `DependencyTelemetry`database viene sempre raccolto e archiviato come nome dell'oggetto raccolto. C'è un campo aggiuntivo chiamato 'dati', che può contenere il testo completo della query SQL.

Per le applicazioni ASP.NET Core, non è necessario alcun passaggio aggiuntivo per ottenere la query SQL completa.

Per le applicazioni ASP.NET, la query SQL completa viene raccolta con l'aiuto della strumentazione del codice byte, che richiede il motore di strumentazione. Sono necessari ulteriori passaggi specifici della piattaforma, come descritto di seguito.

| Piattaforma | Passaggi necessari per ottenere una query SQL completaStep(s) Needed to get full SQL Query |
| --- | --- |
| App Web di Azure |Nel pannello di controllo dell'app Web [aprire il pannello Application Insights](../../azure-monitor/app/azure-web-apps.md) e abilitare i comandi SQL in .NET |
| Server IIS (VM di Azure, locale e così via). | Utilizzare il modulo PowerShell di monitoraggio dello stato per installare il motore di [strumentazione](../../azure-monitor/app/status-monitor-v2-api-reference.md) e riavviare IIS. |
| Servizio cloud di Azure | Aggiungere [l'attività di avvio per installare StatusMonitorAdd startup task to install StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> L'app deve essere installata in ApplicationInsights SDK in fase di compilazione installando pacchetti NuGet per [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) o [ASP.NET applicazioni Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Non supportate

Nei casi precedenti, il modo corretto di convalidare che il motore di `DependencyTelemetry` strumentazione sia installato correttamente consiste nel verificare che la versione SDK di raccolta sia 'rddp'. 'rdddsd' o 'rddf' indica che le dipendenze vengono raccolte tramite i callback DiagnosticSource o EventSource e pertanto la query SQL completa non verrà acquisita.

## <a name="where-to-find-dependency-data"></a>Dove trovare i dati sulle dipendenze

* La [mappa delle applicazioni](app-map.md) visualizza le dipendenze tra l'app e i componenti adiacenti.
* [Diagnostica transazioni](transaction-diagnostics.md) mostra dati del server unificati e correlati.
* [La scheda Browser](javascript.md) mostra le chiamate AJAX dai browser degli utenti.
* Fare clic sulle richieste lente o non riuscite per controllare le chiamate alle dipendenze.
* L'[analisi](#logs-analytics) può essere usata per effettuare una query dei dati sulle dipendenze.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Diagnosticare le richieste lente

Ogni evento di richiesta è associato alle chiamate di dipendenza, alle eccezioni e ad altri eventi registrati durante l'elaborazione della richiesta da parte dell'app. Pertanto, se alcune richieste stanno eseguendo male, è possibile scoprire se è a causa di risposte lente da una dipendenza.

### <a name="tracing-from-requests-to-dependencies"></a>Traccia dalle richieste alle dipendenze

Aprire la scheda **Prestazioni** e passare alla scheda **Dipendenze** nella parte superiore accanto alle operazioni.

Fare clic su un **nome di dipendenza** in generale. Dopo aver selezionato una dipendenza, un grafico della distribuzione delle durate di tale dipendenza verrà visualizzato a destra.

![Nella scheda Prestazioni fare clic sulla scheda Dipendenza in alto, quindi su Un nome di dipendenza nel grafico](./media/asp-net-dependencies/2-perf-dependencies.png)

Fare clic sul pulsante blu **Campioni** in basso a destra e quindi su un esempio per visualizzare i dettagli della transazione end-to-end.

![Fare clic su un esempio per visualizzare i dettagli delle transazioni end-to-end](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilatura del sito live

Se non si riesce a capire perché trascorre così tanto tempo, Il profiler di [Application Insights](../../azure-monitor/app/profiler.md) traccia le chiamate HTTP al sito attivo e mostra le funzioni nel codice che hanno richiesto più tempo.

## <a name="failed-requests"></a>Richieste non riuscite

Le richieste non riuscite possono anche essere associate a chiamate non riuscite a dipendenze.

Possiamo andare alla scheda **Errori** a sinistra e quindi fare clic sulla scheda **Dipendenze** in alto.

![Fare clic sul grafico delle richieste non riuscite](./media/asp-net-dependencies/4-fail.png)

Qui si sarà in grado di vedere il conteggio delle dipendenze non riuscite. Per ottenere ulteriori dettagli su un'occorrenza non riuscita, provare a fare clic su un nome di dipendenza nella tabella inferiore. È possibile fare clic sul pulsante blu **Dipendenze** in basso a destra per ottenere i dettagli della transazione end-to-end.

## <a name="logs-analytics"></a>Log (Analisi)

È possibile tenere traccia delle dipendenze nel [linguaggio di query Kusto](/azure/kusto/query/). Di seguito sono riportati alcuni esempi.

* Trovare eventuali chiamate alle dipendenze non riuscite:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Trovare le chiamate AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Trovare le chiamate alle dipendenze associate alle richieste:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Trovare le chiamate AJAX associate alle visualizzazioni di pagina:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*In che modo le chiamate non riuscite dell'agente di raccolta delle dipendenze automatiche alle dipendenze segnalano le chiamate non riuscite?*

* Le chiamate di dipendenza non riuscite avranno il campo 'success' impostato su False.Failed dependency calls will have 'success' field set to False. `DependencyTrackingTelemetryModule`non riporta `ExceptionTelemetry`. Il modello di dati completo per le dipendenze è descritto [qui](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK open source
Come ogni Application Insights SDK, anche il modulo di raccolta delle dipendenze è open source. Leggere e contribuire al codice, o segnalare problemi presso [il repository ufficiale GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Passaggi successivi

* [Eccezioni](../../azure-monitor/app/asp-net-exceptions.md)
* [Dati utente e di pagina](../../azure-monitor/app/javascript.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
