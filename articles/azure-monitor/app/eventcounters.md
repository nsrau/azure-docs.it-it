---
title: Contatori degli eventi in Application Insights | Microsoft Docs
description: È possibile monitorare i contatori degli eventi di sistema e di .NET/.NET Core personalizzati in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: f8ae36545eecbbad2a6695ca979fb7da8380e8cc
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657017"
---
# <a name="eventcounters-introduction"></a>Introduzione di EventCounter

`EventCounter` è un meccanismo di .NET/.NET Core per pubblicare e utilizzare contatori o statistiche. [Questo](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento fornisce una panoramica di `EventCounters` ed esempi su come pubblicare e utilizzare questi metodi. Il supporto per EventCounter è disponibile in tutte le piattaforme del sistema operativo, ovvero Windows, Linux e macOS. Possono essere considerati un equivalente multipiattaforma per [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) che è supportato solo nei sistemi Windows.

Sebbene gli utenti possano pubblicare qualsiasi personalizzata `EventCounters` per soddisfare le proprie esigenze, .NET Core 3,0 e versioni successive di runtime pubblica un set di questi contatori per impostazione predefinita. In questo documento vengono illustrati i passaggi necessari per raccogliere e visualizzare `EventCounters` (definito dal sistema o definito dall'utente) in applicazione Azure Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Uso di Application Insights per raccogliere EventCounters

Application Insights supporta `EventCounters` la raccolta con la relativa `EventCounterCollectionModule` , che fa parte del nuovo pacchetto NuGet [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` viene abilitato automaticamente quando si usa [AspNetCore](asp-net-core.md) o [WorkerService](worker-service.md). `EventCounterCollectionModule` raccoglie contatori con una frequenza di raccolta non configurabile pari a 60 secondi. Non sono richieste autorizzazioni speciali per raccogliere EventCounters.

## <a name="default-counters-collected"></a>Contatori predefiniti raccolti

Per le app in esecuzione in .NET Core 3,0 o versioni successive, i contatori seguenti vengono raccolti automaticamente dall'SDK. Il formato del nome dei contatori sarà "Categoria|Contatore".

|Category | Contatore|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |

> [!NOTE]
> A partire dalla versione 2.15.0-beta3 di [ASPNETCORE SDK](asp-net-core.md) o [WorkerService SDK](worker-service.md), per impostazione predefinita non viene raccolto alcun contatore. Il modulo stesso è abilitato, quindi gli utenti possono semplicemente aggiungere i contatori desiderati per raccoglierli.

## <a name="customizing-counters-to-be-collected"></a>Personalizzazione dei contatori da raccogliere

L'esempio seguente illustra come aggiungere/rimuovere contatori. Questa personalizzazione viene eseguita nel metodo `ConfigureServices` dell'applicazione dopo l'abilitazione della telemetria di Application Insights con `AddApplicationInsightsTelemetry()` o `AddApplicationInsightsWorkerService()`. Di seguito è disponibile un esempio di codice da un'applicazione ASP.NET Core. Per altri tipi di applicazioni, vedere [questo](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>Disabilitazione del modulo della raccolta EventCounter

`EventCounterCollectionModule` può essere disabilitato usando `ApplicationInsightsServiceOptions` . Di seguito è riportato un esempio di utilizzo di ASP.NET Core SDK.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

Un approccio simile può essere usato anche per WorkerService SDK, ma lo spazio dei nomi deve essere modificato come illustrato nell'esempio riportato di seguito.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Contatori degli eventi nello strumento di esplorazione metriche

Per visualizzare le metriche di EventCounter nello [strumento di esplorazione metriche](../platform/metrics-charts.md), selezionare la risorsa di Application Insights e scegliere le metriche basate su log come spazio dei nomi per la metrica. Le metriche di EventCounter vengono visualizzate nella categoria Personalizzata.

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application Insights Esplora metriche](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contatori degli eventi in Analytics

È anche possibile cercare e visualizzare report sui contatori degli eventi in [Analytics](../log-query/log-query-overview.md) nella scheda **customMetrics**.

Eseguire ad esempio la query seguente per visualizzare i contatori raccolti e disponibili per le query:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application Insights Analytics](./media/event-counters/analytics-event-counters.png)

Per ottenere un grafico di un contatore specifico, ad esempio `ThreadPool Completed Work Item Count`, nel periodo recente, eseguire la query seguente.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Grafico di un singolo contatore in Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Come altri dati di telemetria, **customMetrics** contiene anche una colonna `cloud_RoleInstance` che indica l'identità dell'istanza del server host in cui viene eseguita l'app. La query precedente mostra il valore del contatore per ogni istanza e può essere usata per confrontare le prestazioni di diverse istanze del server.

## <a name="alerts"></a>Avvisi
Come per altre metriche, è possibile [impostare un avviso](../platform/alerts-log.md) per ricevere una notifica se un contatore degli eventi supera un limite specificato. Aprire il riquadro Avvisi e fare clic su Aggiungi avviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-see-eventcounters-in-live-metrics"></a>È possibile visualizzare EventCounters in Metriche attive?

In Metriche attive non vengono mostrati EventCounters a oggi. Usare lo strumento di esplorazione metriche o Analytics per visualizzare i dati di telemetria.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Application Insights è stato abilitato dal portale di App Web di Azure. Non è tuttavia possibile visualizzare EventCounters.

 L'[estensione di Application Insights](./azure-web-apps.md) per ASP.NET Core non supporta ancora questa funzionalità. Questo documento verrà aggiornato quando la funzionalità sarà supportata.

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi

* [Rilevamento delle dipendenze](./asp-net-dependencies.md)

