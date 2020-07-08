---
title: Contatori degli eventi in Application Insights | Microsoft Docs
description: È possibile monitorare i contatori degli eventi di sistema e di .NET/.NET Core personalizzati in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 37d0e1e741548986788be78860830f36add1f5a8
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700443"
---
# <a name="eventcounters-introduction"></a>Introduzione di EventCounter

`EventCounter` è un meccanismo di .NET/.NET Core per pubblicare e utilizzare contatori o statistiche. [Questo](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento fornisce una panoramica di `EventCounters` ed esempi su come pubblicare e utilizzare questi metodi. Il supporto per EventCounter è disponibile in tutte le piattaforme del sistema operativo, ovvero Windows, Linux e macOS. Possono essere considerati un equivalente multipiattaforma per [PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) che è supportato solo nei sistemi Windows.

Anche se gli utenti possono pubblicare qualsiasi `EventCounters` personalizzato per soddisfare le proprie esigenze, il runtime di .NET Core 3.0 pubblica un set di questi contatori per impostazione predefinita. Il documento illustrerà in modo dettagliato i passaggi necessari per raccogliere e visualizzare `EventCounters`, definiti dal sistema o definiti dall'utente, in Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Uso di Application Insights per raccogliere EventCounters

Application Insights supporta la raccolta di `EventCounters` con `EventCounterCollectionModule`, che è incluso nel pacchetto NuGet [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector) rilasciato di recente. `EventCounterCollectionModule` viene abilitato automaticamente quando si usa [AspNetCore](asp-net-core.md) o [WorkerService](worker-service.md). `EventCounterCollectionModule` raccoglie contatori con una frequenza di raccolta non configurabile pari a 60 secondi. Non sono richieste autorizzazioni speciali per raccogliere EventCounters.

## <a name="default-counters-collected"></a>Contatori predefiniti raccolti

Per le app in esecuzione in .NET Core 3.0 vengono raccolti automaticamente dall'SDK i contatori seguenti. Il formato del nome dei contatori sarà "Categoria|Contatore".

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
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> I contatori della categoria Microsoft.AspNetCore.Hosting vengono aggiunti solo in applicazioni ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Personalizzazione dei contatori da raccogliere

L'esempio seguente illustra come aggiungere/rimuovere contatori. Questa personalizzazione viene eseguita nel metodo `ConfigureServices` dell'applicazione dopo l'abilitazione della telemetria di Application Insights con `AddApplicationInsightsTelemetry()` o `AddApplicationInsightsWorkerService()`. Di seguito è disponibile un esempio di codice da un'applicazione ASP.NET Core. Per altri tipi di applicazioni, vedere [questo](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
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

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Contatori degli eventi nello strumento di esplorazione metriche

Per visualizzare le metriche di EventCounter nello [strumento di esplorazione metriche](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), selezionare la risorsa di Application Insights e scegliere le metriche basate su log come spazio dei nomi per la metrica. Le metriche di EventCounter vengono visualizzate nella categoria Personalizzata.

> [!div class="mx-imgBorder"]
> ![Contatori degli eventi segnalati in Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contatori degli eventi in Analytics

È anche possibile cercare e visualizzare report sui contatori degli eventi in [Analytics](../../azure-monitor/app/analytics.md) nella scheda **customMetrics**.

Eseguire ad esempio la query seguente per visualizzare i contatori raccolti e disponibili per le query:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contatori degli eventi segnalati in Application Insights](./media/event-counters/analytics-event-counters.png)

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
Come per altre metriche, è possibile [impostare un avviso](../../azure-monitor/platform/alerts-log.md) per ricevere una notifica se un contatore degli eventi supera un limite specificato. Aprire il riquadro Avvisi e fare clic su Aggiungi avviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-see-eventcounters-in-live-metrics"></a>È possibile visualizzare EventCounters in Metriche attive?

In Metriche attive non vengono mostrati EventCounters a oggi. Usare lo strumento di esplorazione metriche o Analytics per visualizzare i dati di telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>In quali piattaforme è possibile visualizzare l'elenco predefinito di contatori .NET Core 3.0?

EventCounter non richiede autorizzazioni speciali ed è supportato in tutte le piattaforme in cui è supportato .NET Core 3.0. ad esempio:

* **Sistema operativo**: Windows, Linux o macOS.
* **Metodo di hosting**: interno o esterno al processo.
* **Metodo di distribuzione**: dipendente dal framework o autonomo.
* **Server Web**: IIS (Internet Information Server) o Kestrel.
* **Piattaforma di hosting**: la funzionalità App Web del Servizio app di Azure, Macchina virtuale di Azure, Docker, servizio Azure Kubernetes e così via.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Application Insights è stato abilitato dal portale di App Web di Azure. Non è tuttavia possibile visualizzare EventCounters.

 L'[estensione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) per ASP.NET Core non supporta ancora questa funzionalità. Questo documento verrà aggiornato quando la funzionalità sarà supportata.

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi

* [Rilevamento delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md)
