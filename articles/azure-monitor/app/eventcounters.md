---
title: 'Contatori di eventi in Application Insights : Documenti Microsoft'
description: Monitorare il sistema e gli eventCounters .NET/.NET Core personalizzati in Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663590"
---
# <a name="eventcounters-introduction"></a>EventCounters introduzione

`EventCounter`è il meccanismo .NET/.NET Core per pubblicare e utilizzare contatori o statistiche. [Questo](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) documento fornisce `EventCounters` una panoramica ed esempi su come pubblicarli e utilizzarli. EventCounters sono supportati in tutte le piattaforme del sistema operativo- Windows, Linux e macOS. Può essere considerato come un equivalente multipiattaforma per [il PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) che è supportato solo nei sistemi Windows.

Mentre gli utenti `EventCounters` possono pubblicare qualsiasi personalizzato per soddisfare le proprie esigenze, il runtime di .NET Core 3.0 pubblica un set di questi contatori per impostazione predefinita. Il documento illustra i passaggi necessari `EventCounters` per raccogliere e visualizzare (definito dal sistema o definito dall'utente) in Azure Application Insights.The document will walk through the steps required to collect and view (system defined or user defined) in Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Utilizzo di Application Insights per raccogliere EventCountersUsing Application Insights to collect EventCounters

Application Insights supporta `EventCounters` la `EventCounterCollectionModule`raccolta con il relativo , che fa parte del pacchetto nuget appena rilasciato [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`viene abilitato automaticamente quando si utilizza [AspNetCore](asp-net-core.md) o [WorkerService](worker-service.md). `EventCounterCollectionModule`raccoglie i contatori con una frequenza di raccolta non configurabile di 60 secondi. Non sono necessarie autorizzazioni speciali per raccogliere EventCounters.There are no special permissions required to collect EventCounters.

## <a name="default-counters-collected"></a>Contatori predefiniti raccolti

Per le app in esecuzione in .NET Core 3.0, i contatori seguenti vengono raccolti automaticamente dall'SDK. Il nome dei contatori avrà il formato "Categoria Contatore".

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
> I contatori della categoria Microsoft.AspNetCore.Hosting vengono aggiunti solo in ASP.NET applicazioni principali.

## <a name="customizing-counters-to-be-collected"></a>Personalizzazione dei contatori da raccogliere

Nell'esempio seguente viene illustrato come aggiungere/rimuovere contatori. Questa personalizzazione verrà `ConfigureServices` eseguita nel metodo dell'applicazione dopo `AddApplicationInsightsTelemetry()` l'abilitazione della raccolta di dati di Application Insights tramite o `AddApplicationInsightsWorkerService()`. Di seguito è riportato un codice di esempio da un'applicazione ASP.NET Core.Following is an example code from an ASP.NET Core application. Per altri tipi di applicazioni, fare riferimento a [questo](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

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

## <a name="event-counters-in-metric-explorer"></a>Contatori di eventi in Esplora metriche

Per visualizzare le metriche EventCounter in [Esplora metriche,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)selezionare Risorsa di Application Insights e scegliere Metriche basate su log come spazio dei nomi delle metriche. Le metriche EventCounter vengono quindi visualizzate in Categoria personalizzata.

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application InsightsEvent counters reported in Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contatori di eventi in Analytics

Puoi anche cercare e visualizzare i rapporti sui contatori degli eventi in [Analytics,](../../azure-monitor/app/analytics.md)nella tabella **customMetrics.**

Ad esempio, eseguire la query seguente per vedere quali contatori vengono raccolti e disponibili per la query:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contatori di eventi segnalati in Application InsightsEvent counters reported in Application Insights](./media/event-counters/analytics-event-counters.png)

Per ottenere un grafico di un `ThreadPool Completed Work Item Count`contatore specifico (ad esempio: ) nel periodo recente, eseguire la query seguente.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat di un singolo contatore in Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Analogamente ad altri dati `cloud_RoleInstance` di telemetria, **customMetrics** include anche una colonna che indica l'identità dell'istanza del server host in cui è in esecuzione l'app. La query precedente mostra il valore del contatore per istanza e può essere utilizzata per confrontare le prestazioni di diverse istanze del server.

## <a name="alerts"></a>Avvisi
Analogamente ad altre metriche, puoi [impostare un avviso](../../azure-monitor/app/alerts.md) per avvisarti se un contatore di eventi non rientra in un limite specificato. Aprire il riquadro Avvisi e fare clic su Aggiungi avviso.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-see-eventcounters-in-live-metrics"></a>È possibile visualizzare EventCounters in Live Metrics?

Le metriche live non mostrano EventCounters a partire da oggi. Usare Esplora metriche o Analytics per visualizzare i dati di telemetria.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Quali piattaforme è possibile visualizzare l'elenco predefinito dei contatori di .NET Core 3.0?

EventCounter non richiede autorizzazioni speciali ed è supportato in tutte le piattaforme .NET Core 3.0 è supportato. ad esempio:

* **Sistema operativo**: Windows, Linux o macOS.
* **Metodo**di hosting : In corso o fuori processo.
* **Metodo di distribuzione:** dipendente dal framework o autonomo.
* **Server Web**: IIS (Internet Information Server) o Kestrel.
* **Piattaforma di hosting:** la funzionalità App Web del servizio app di Azure, della macchina virtuale di Azure, di Docker, del servizio Azure Kubernetes (AKS) e così via.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Ho abilitato Application Insights dal portale di App Web di Azure.Ho abilitato Application Insights dal portale di App Web di Azure.Ho abilitato Application Insights dal portale di App Web di Azure Ma non riesco a vedere EventCounters.?

 [L'estensione di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) per ASP.NET Core non supporta ancora questa funzionalità. Questo documento verrà aggiornato quando questa funzionalità è supportata.

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi

* [Rilevamento delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md)
