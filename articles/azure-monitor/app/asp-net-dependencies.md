---
title: Monitoraggio delle dipendenze in Azure Application Insights | Microsoft Docs
description: Monitorare le chiamate alle dipendenze dall'applicazione Web locale o Microsoft Azure con Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: 858508e949f8a880498e1a3d983dc76224010c31
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534608"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Rilevamento delle dipendenze in applicazione Azure Insights 

Una *dipendenza* è un componente esterno chiamato dall'app. In genere è un servizio chiamato con il protocollo HTTP, oppure un database o un file system. [Application Insights](../../azure-monitor/app/app-insights-overview.md) misura la durata delle chiamate alle dipendenze, indipendentemente dal fatto che si verifichino o meno, insieme a informazioni aggiuntive come il nome della dipendenza e così via. È possibile analizzare chiamate di dipendenza specifiche e metterle in correlazione a richieste ed eccezioni.

## <a name="automatically-tracked-dependencies"></a>Dipendenze rilevate automaticamente

Application Insights SDK per .NET e .NET Core viene fornito `DependencyTrackingTelemetryModule` con un modulo di telemetria che raccoglie automaticamente le dipendenze. Questa raccolta delle dipendenze viene abilitata automaticamente per le applicazioni [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) e [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) , se configurata in base alla documentazione ufficiale collegata. viene fornito come [questo](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pacchetto NuGet e viene portato automaticamente quando si usa uno dei pacchetti `Microsoft.ApplicationInsights.Web` NuGet o. `Microsoft.ApplicationInsights.AspNetCore` `DependencyTrackingTelemetryModule`

 `DependencyTrackingTelemetryModule`attualmente rileva automaticamente le dipendenze seguenti:

|Dependencies |Dettagli|
|---------------|-------|
|Http/https | Chiamate http/https locali o remote |
|Chiamate WCF| Viene rilevata automaticamente solo se vengono utilizzate associazioni basate su http.|
|SQL | Chiamate effettuate con `SqlClient`. Vedere [questo per l'](#advanced-sql-tracking-to-get-full-sql-query) acquisizione della query SQL.  |
|[Archiviazione di Azure (BLOB, tabelle, code)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Chiamate effettuate con il client di archiviazione di Azure. |
|[SDK client EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versione 1.1.0 e successive. |
|[SDK Client di ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versione 3.0.0 e successive. |
|Azure Cosmos DB | Viene rilevata automaticamente solo se si usa HTTP/HTTPS. La modalità TCP non verrà acquisita da Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurare il rilevamento automatico delle dipendenze nelle app console

Per tenere traccia automaticamente delle dipendenze dalle app console .NET/.NET Core, installare `Microsoft.ApplicationInsights.DependencyCollector`il pacchetto NuGet `DependencyTrackingTelemetryModule` e inizializzare come segue:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

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

In alternativa, `TelemetryClient` fornisce metodi `StartOperation` di estensione `StopOperation` e che possono essere usati per tenere traccia manualmente delle dipendenze, come illustrato di [seguito](custom-operations-tracking.md#outgoing-dependencies-tracking) .

Se si vuole disattivare il modulo standard di rilevamento delle dipendenze, rimuovere il riferimento a DependencyTrackingTelemetryModule in [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) per le applicazioni ASP.NET. Per ASP.NET Core applicazioni, seguire le istruzioni riportate [qui](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Verifica delle chiamate AJAX da pagine Web

Per le pagine Web, Application Insights JavaScript SDK raccoglie automaticamente le chiamate AJAX come dipendenze.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Rilevamento SQL avanzato per ottenere una query SQL completa

Per le chiamate SQL, il nome del server e del database viene sempre raccolto e archiviato come nome della raccolta `DependencyTelemetry`. È presente un campo aggiuntivo denominato "data", che può contenere il testo completo della query SQL.

Per ASP.NET Core applicazioni, non sono necessari passaggi aggiuntivi per ottenere la query SQL completa.

Per le applicazioni ASP.NET, la query SQL completa viene raccolta con l'ausilio della strumentazione del codice byte, che richiede il motore di strumentazione. Sono necessari passaggi aggiuntivi specifici della piattaforma, come descritto di seguito.

| Piattaforma | Passaggi necessari per ottenere la query SQL completa |
| --- | --- |
| App Web di Azure |Nel pannello di controllo dell'app Web [aprire il pannello Application Insights](../../azure-monitor/app/azure-web-apps.md) e abilitare i comandi SQL in .NET |
| Server IIS (VM di Azure, locale e così via) | Usare il modulo Status Monitor PowerShell per [installare il motore di strumentazione](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md) e riavviare IIS. |
| Servizio cloud di Azure | Aggiungere un' [attività di avvio per installare StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> L'app deve essere caricata in ApplicationInsights SDK in fase di compilazione installando pacchetti NuGet per applicazioni [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) o [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Non supportate

Nei casi precedenti, il modo corretto per convalidare il motore di strumentazione è installato correttamente consiste nel confermare che la versione dell' `DependencyTelemetry` SDK raccolta è' rddp '. ' rdddsd ' o ' rddf ' indica che le dipendenze vengono raccolte tramite callback DiagnosticSource o EventSource e pertanto la query SQL completa non verrà acquisita.

## <a name="where-to-find-dependency-data"></a>Dove trovare i dati sulle dipendenze

* La [mappa delle applicazioni](app-map.md) visualizza le dipendenze tra l'app e i componenti adiacenti.
* La [diagnostica delle transazioni](transaction-diagnostics.md) Mostra dati server unificati e correlati.
* [Scheda browser](javascript.md) Mostra le chiamate AJAX dai browser degli utenti.
* Fare clic sulle richieste lente o non riuscite per controllare le chiamate alle dipendenze.
* L'[analisi](#logs-analytics) può essere usata per effettuare una query dei dati sulle dipendenze.

## <a name="diagnosis"></a> Diagnosticare le richieste lente

Ogni evento di richiesta è associato alle chiamate di dipendenza, alle eccezioni e ad altri eventi che vengono rilevati durante l'elaborazione della richiesta da parte dell'app. Quindi, se alcune richieste vengono eseguite in modo errato, è possibile verificare se sono dovute a risposte lente da una dipendenza.

### <a name="tracing-from-requests-to-dependencies"></a>Traccia dalle richieste alle dipendenze

Aprire la scheda **prestazioni** e passare alla scheda **dipendenze** nella parte superiore accanto a Operations.

Fare clic su un **nome di dipendenza** in generale. Dopo aver selezionato una dipendenza, a destra viene visualizzato un grafico della distribuzione di durata della dipendenza.

![Nella scheda prestazioni fare clic sulla scheda dipendenza nella parte superiore e quindi su un nome di dipendenza nel grafico](./media/asp-net-dependencies/2-perf-dependencies.png)

Fare clic sul pulsante **esempi** blu in basso a destra e quindi su un esempio per visualizzare i dettagli della transazione end-to-end.

![Fare clic su un esempio per visualizzare i dettagli della transazione end-to-end](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilatura del sito live

Se non si riesce a capire perché trascorre così tanto tempo, Il profiler di [Application Insights](../../azure-monitor/app/profiler.md) traccia le chiamate http al sito Live e Mostra le funzioni nel codice che hanno richiesto più tempo.

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

* Le chiamate alle dipendenze non riuscite avranno il campo ' success ' impostato su false. `DependencyTrackingTelemetryModule`non segnala `ExceptionTelemetry`. Il modello di dati completo per la dipendenza è descritto [qui](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK open source
Come ogni Application Insights SDK, anche il modulo di raccolta delle dipendenze è open source. Leggere e contribuire al codice o segnalare problemi nel [repository GitHub ufficiale](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Passaggi successivi

* [Eccezioni](../../azure-monitor/app/asp-net-exceptions.md)
* [Dati utente e di pagina](../../azure-monitor/app/javascript.md)
* [Disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
