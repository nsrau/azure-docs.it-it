---
title: Monitoraggio delle dipendenze in Azure Application Insights | Microsoft Docs
description: Monitorare le chiamate alle dipendenze dall'applicazione Web locale o Microsoft Azure con Application Insights.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: df13042656aa077b30bf144aab0a47d9fc0a0662
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263930"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rilevamento delle dipendenze in applicazione Azure Insights 

Una *dipendenza* è un componente chiamato dall'applicazione. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. [Application Insights](./app-insights-overview.md) misura la durata delle chiamate alle dipendenze, indipendentemente dal fatto che si verifichino o meno, insieme a informazioni aggiuntive come il nome della dipendenza e così via. È possibile analizzare chiamate di dipendenza specifiche e metterle in correlazione a richieste ed eccezioni.

## <a name="automatically-tracked-dependencies"></a>Dipendenze rilevate automaticamente

Application Insights SDK per .NET e .NET Core viene fornito con `DependencyTrackingTelemetryModule` , ovvero un modulo di telemetria che raccoglie automaticamente le dipendenze. Questa raccolta delle dipendenze viene abilitata automaticamente per le applicazioni [ASP.NET](./asp-net.md) e [ASP.NET Core](./asp-net-core.md) , se configurata in base alla documentazione ufficiale collegata. `DependencyTrackingTelemetryModule` viene fornito come [questo](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacchetto NuGet e viene portato automaticamente quando si usa uno dei pacchetti NuGet `Microsoft.ApplicationInsights.Web` o `Microsoft.ApplicationInsights.AspNetCore` .

 `DependencyTrackingTelemetryModule` attualmente rileva automaticamente le dipendenze seguenti:

|Dependencies |Dettagli|
|---------------|-------|
|Http/https | Chiamate http/https locali o remote |
|Chiamate WCF| Viene rilevata automaticamente solo se vengono utilizzate associazioni basate su http.|
|SQL | Chiamate effettuate con `SqlClient` . Vedere [questo per l'](#advanced-sql-tracking-to-get-full-sql-query) acquisizione della query SQL.  |
|[Archiviazione di Azure (BLOB, tabelle, code)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chiamate effettuate con il client di archiviazione di Azure. |
|[SDK client EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versione 1.1.0 e successive. |
|[SDK Client di ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versione 3.0.0 e successive. |
|Azure Cosmos DB | Viene rilevata automaticamente solo se si usa HTTP/HTTPS. La modalità TCP non verrà acquisita da Application Insights. |

Se manca una dipendenza o se si usa un SDK diverso, assicurarsi che sia presente nell'elenco di [dipendenze raccolte automaticamente](./auto-collect-dependencies.md). Se la dipendenza non viene raccolta automaticamente, è comunque possibile monitorarla manualmente con una [chiamata di rilevamento delle dipendenze](./api-custom-events-metrics.md#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurare il rilevamento automatico delle dipendenze nelle app console

Per tenere traccia automaticamente delle dipendenze dalle app console .NET, installare il pacchetto NuGet `Microsoft.ApplicationInsights.DependencyCollector` e inizializzare `DependencyTrackingTelemetryModule` come segue:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Per le app console .NET Core TelemetryConfiguration. Active è obsoleto. Vedere le indicazioni contenute nella [documentazione del servizio Worker](./worker-service.md) e nella [documentazione sul monitoraggio ASP.NET Core](./asp-net-core.md)

### <a name="how-automatic-dependency-monitoring-works"></a>Funzionamento del monitoraggio delle dipendenze automatico

Le dipendenze vengono raccolte automaticamente utilizzando una delle tecniche seguenti:

* Uso della strumentazione del codice byte intorno ai metodi Select. (InstrumentationEngine da StatusMonitor o dall'estensione app Web di Azure)
* Callback EventSource
* Callback DiagnosticSource (negli SDK più recenti di .NET/.NET Core)

## <a name="manually-tracking-dependencies"></a>Rilevamento manuale delle dipendenze

Di seguito sono riportati alcuni esempi di dipendenze, che non vengono raccolte automaticamente e richiedono quindi il rilevamento manuale.

* viene tenuta automaticamente traccia di Azure Cosmos DB solo se è usato [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking). La modalità TCP non verrà acquisita da Application Insights.
* Redis

Per le dipendenze non raccolte automaticamente da SDK, è possibile tenerne traccia manualmente usando l' [API TrackDependency](api-custom-events-metrics.md#trackdependency) usata dai moduli di raccolta automatica standard.

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

In alternativa, `TelemetryClient` fornisce metodi `StartOperation` di estensione e `StopOperation` che possono essere usati per tenere traccia manualmente delle dipendenze, come illustrato di [seguito](custom-operations-tracking.md#outgoing-dependencies-tracking) .

Se si vuole disattivare il modulo standard di rilevamento delle dipendenze, rimuovere il riferimento a DependencyTrackingTelemetryModule in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) per le applicazioni ASP.NET. Per ASP.NET Core applicazioni, seguire le istruzioni riportate [qui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Verifica delle chiamate AJAX da pagine Web

Per le pagine Web, Application Insights JavaScript SDK raccoglie automaticamente le chiamate AJAX come dipendenze.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rilevamento SQL avanzato per ottenere una query SQL completa

Per le chiamate SQL, il nome del server e del database viene sempre raccolto e archiviato come nome della raccolta `DependencyTelemetry` . È presente un campo aggiuntivo denominato "data", che può contenere il testo completo della query SQL.

Per ASP.NET Core applicazioni, è ora necessario acconsentire esplicitamente alla raccolta di testo SQL utilizzando
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

Per le applicazioni ASP.NET, il testo completo della query SQL viene raccolto con l'ausilio della strumentazione del codice byte, che richiede l'uso del motore di strumentazione o il pacchetto NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) anziché la libreria System. Data. SqlClient. I passaggi specifici della piattaforma per abilitare la raccolta completa di query SQL sono descritti di seguito:

| Piattaforma | Passaggi necessari per ottenere la query SQL completa |
| --- | --- |
| App Web di Azure |Nel pannello di controllo dell'app Web [aprire il pannello Application Insights](../../azure-monitor/app/azure-web-apps.md) e abilitare i comandi SQL in .NET |
| Server IIS (VM di Azure, locale e così via) | Usare il pacchetto NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) o usare il modulo Status Monitor PowerShell per [installare il motore di strumentazione](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine) e riavviare IIS. |
| Servizio cloud di Azure | Aggiungere un' [attività di avvio per installare StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> L'app deve essere caricata in ApplicationInsights SDK in fase di compilazione installando pacchetti NuGet per applicazioni [ASP.NET](./asp-net.md) o [ASP.NET Core](./asp-net-core.md) |
| IIS Express | Usare il pacchetto NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .
| Processi Web di Azure | Usare il pacchetto NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .

Oltre ai passaggi specifici della piattaforma descritti in precedenza, è **necessario acconsentire esplicitamente all'abilitazione della raccolta dei comandi SQL** modificando il file applicationInsights.config con quanto segue:

```xml
<Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
<EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
</Add>
```

Nei casi precedenti, il modo corretto per convalidare il motore di strumentazione è installato correttamente consiste nel confermare che la versione dell'SDK raccolta `DependencyTelemetry` è' rddp '. ' rdddsd ' o ' rddf ' indica che le dipendenze vengono raccolte tramite callback DiagnosticSource o EventSource e pertanto la query SQL completa non verrà acquisita.

## <a name="where-to-find-dependency-data"></a>Dove trovare i dati sulle dipendenze

* La [mappa delle applicazioni](app-map.md) visualizza le dipendenze tra l'app e i componenti adiacenti.
* La [diagnostica delle transazioni](transaction-diagnostics.md) Mostra dati server unificati e correlati.
* [Scheda browser](javascript.md) Mostra le chiamate AJAX dai browser degli utenti.
* Fare clic sulle richieste lente o non riuscite per controllare le chiamate alle dipendenze.
* L'[analisi](#logs-analytics) può essere usata per effettuare una query dei dati sulle dipendenze.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Diagnosticare le richieste lente

Ogni evento di richiesta è associato alle chiamate di dipendenza, alle eccezioni e ad altri eventi che vengono rilevati durante l'elaborazione della richiesta da parte dell'app. Quindi, se alcune richieste vengono eseguite in modo errato, è possibile verificare se sono dovute a risposte lente da una dipendenza.

### <a name="tracing-from-requests-to-dependencies"></a>Traccia dalle richieste alle dipendenze

Aprire la scheda **prestazioni** e passare alla scheda **dipendenze** nella parte superiore accanto a Operations.

Fare clic su un **nome di dipendenza** in generale. Dopo aver selezionato una dipendenza, a destra viene visualizzato un grafico della distribuzione di durata della dipendenza.

![Nella scheda prestazioni fare clic sulla scheda dipendenza nella parte superiore e quindi su un nome di dipendenza nel grafico](./media/asp-net-dependencies/2-perf-dependencies.png)

Fare clic sul pulsante **esempi** blu in basso a destra e quindi su un esempio per visualizzare i dettagli della transazione end-to-end.

![Fare clic su un esempio per visualizzare i dettagli della transazione end-to-end](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilatura del sito live

Se non si riesce a capire perché trascorre così tanto tempo, Il [Profiler di Application Insights](../../azure-monitor/app/profiler.md) traccia le chiamate http al sito Live e Mostra le funzioni nel codice che hanno richiesto più tempo.

## <a name="failed-requests"></a>Richieste non riuscite

Le richieste non riuscite possono anche essere associate a chiamate non riuscite a dipendenze.

È possibile passare alla scheda **errori** a sinistra e quindi fare clic sulla scheda **dipendenze** nella parte superiore.

![Fare clic sul grafico delle richieste non riuscite](./media/asp-net-dependencies/4-fail.png)

Qui sarà possibile visualizzare il numero di dipendenze non riuscite. Per ottenere ulteriori informazioni su un'occorrenza non riuscita, provare a fare clic su un nome di dipendenza nella tabella inferiore. È possibile fare clic sul pulsante **dipendenze** blu nella parte inferiore destra per ottenere i dettagli della transazione end-to-end.

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

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*In che modo l'agente di raccolta dipendenze automatico segnala le chiamate alle dipendenze?*

* Le chiamate alle dipendenze non riuscite avranno il campo ' success ' impostato su false. `DependencyTrackingTelemetryModule` non segnala `ExceptionTelemetry` . Il modello di dati completo per la dipendenza è descritto [qui](data-model-dependency-telemetry.md).

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*Ricerca per categorie calcolare la latenza di inserimento per la telemetria delle dipendenze?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*Ricerca per categorie determinare l'ora in cui è stata avviata la chiamata di dipendenza?*

Nella visualizzazione Query Log Analytics `timestamp` rappresenta il momento in cui è stata avviata la chiamata a TrackDependency () che si verifica immediatamente dopo la ricezione della risposta alla chiamata di dipendenza. Per calcolare l'ora di inizio della chiamata di dipendenza, è necessario `timestamp` e sottrarre il registrato `duration` della chiamata di dipendenza.

## <a name="open-source-sdk"></a>SDK open source
Come ogni Application Insights SDK, anche il modulo di raccolta delle dipendenze è open source. Leggere e contribuire al codice o segnalare problemi nel [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Passaggi successivi

* [Eccezioni](./asp-net-exceptions.md)
* [Dati utente e di pagina](./javascript.md)
* [Disponibilità](./monitor-web-app-availability.md)

