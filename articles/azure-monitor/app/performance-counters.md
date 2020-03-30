---
title: Contatori delle prestazioni in Application Insights | Documentazione Microsoft
description: Sistema di monitoraggio e contatori delle prestazioni .NET personalizzati in Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: 94d2520c17867f6d70caffd002a76365a425986f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669880"
---
# <a name="system-performance-counters-in-application-insights"></a>Contatori delle prestazioni di sistema in Application Insights

Windows offre un'ampia gamma di [contatori delle prestazioni](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters), ad esempio su occupazione della CPU, memoria, disco e utilizzo di rete. È anche possibile definire contatori delle prestazioni personalizzati. La raccolta dei contatori delle prestazioni è supportata finché l'applicazione è in esecuzione in IIS in un host locale o in una macchina virtuale a cui si ha accesso amministrativo. Anche se le applicazioni in esecuzione come app Web di Azure non hanno accesso diretto ai contatori delle prestazioni, un sottoinsieme di contatori disponibili viene raccolto da Application Insights.Though applications running as Azure Web Apps don't have direct access to performance counters, a subset of available counters are collected by Application Insights.

## <a name="view-counters"></a>Visualizzare i contatori

Il riquadro Metrica mostra un set predefinito di contatori delle prestazioni.

![Contatori delle prestazioni segnalati in Application Insights](./media/performance-counters/performance-counters.png)

I contatori predefiniti correnti configurati per la raccolta per le applicazioni Web ASP.NET/ASP.NET Core sono:
- %\\tempo processore di processo
- %\\tempo processore normalizzato
- Byte\\disponibili per la memoria
- Richieste ASP.NET al secondo
- Eccezioni CLR .NET generate/sec
- ASP.NET ApplicationsRequest Durata esecuzione
- Elabora\\byte privati
- Byte\\dati I/O processo/secProcess IO Data Bytes/sec
- richieste\\di applicazioni ASP.NET nella coda dell'applicazione
- Processore(_Total)\\% Tempo processore

## <a name="add-counters"></a>Aggiungere contatori

Se il contatore delle prestazioni desiderato non è incluso nell'elenco delle metriche, è possibile aggiungerlo.

1. È possibile identificare i contatori disponibili nel server usando questo comando di PowerShell nel server locale:

    `Get-Counter -ListSet *`

    (Vedere [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Aprire ApplicationInsights.config.

   * Se Application Insights è stato aggiunto all'app durante lo sviluppo, modificare ApplicationInsights.config nel progetto e quindi distribuirlo di nuovo nei server.
3. Modificare la direttiva dell'agente di raccolta delle prestazioni:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> ASP.NET applicazioni Core `ApplicationInsights.config`non dispongono di , pertanto il metodo precedente non è valido per ASP.NET applicazioni principali.

È possibile acquisire contatori standard e quelli implementati manualmente. `\Objects\Processes` è un esempio di contatore standard, disponibile in tutti i sistemi Windows. `\Sales(photo)\# Items Sold` è un esempio di contatore personalizzato che può essere implementato in un servizio Web.

Il formato è `\Category(instance)\Counter"` oppure, per categorie non associate a istanze, solo `\Category\Counter`.

`ReportAs` è necessario per i nomi dei contatori che non corrispondono a `[a-zA-Z()/-_ \.]+`, ovvero che contengono caratteri che non sono inclusi in questi set: lettere, parentesi tonde, barra, trattino, carattere di sottolineatura, spazio e punto.

Se si specifica un'istanza, questa verrà raccolta come dimensione "CounterInstanceName" della metrica indicata.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Raccolta dei contatori delle prestazioni nel codice per ASP.NET applicazioni Web o applicazioni console .NET/.NET CoreCollecting performance counters in code for ASP.NET Web Applications or .NET/.NET Core Console Applications
Per raccogliere i contatori delle prestazioni di sistema e inviarli ad Application Insights, è possibile adattare il frammento di codice seguente:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

In alternativa, è possibile eseguire la stessa operazione con le metriche personalizzate create:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Raccolta dei contatori delle prestazioni nel codice per ASP.NET applicazioni Web di baseCollecting performance counters in code for ASP.NET Core Web Applications

Modificare `ConfigureServices` il `Startup.cs` metodo nella classe come indicato di seguito.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Contatori delle prestazioni in Analytics
È possibile cercare e visualizzare report dei contatori delle prestazioni in [Analytics](../../azure-monitor/app/analytics.md).

Lo schema **performanceCounters** `category`espone `counter` i `instance` nomi , e il nome di ogni contatore delle prestazioni.  Nei dati di telemetria per ogni applicazione verranno visualizzati solo i contatori per l'applicazione specifica. Ad esempio, per visualizzare quali contatori sono disponibili: 

![Contatori delle prestazioni in Application Insights - Analisi](./media/performance-counters/analytics-performance-counters.png)

"Istanza" qui fa riferimento all'istanza del contatore delle prestazioni e non all'istanza del ruolo o del server. Il nome dell'istanza del contatore delle prestazioni segmenta in genere i contatori, come quello per il tempo del processore, in base al nome del processo o dell'applicazione.

Per ottenere un grafico della memoria disponibile nel periodo recente: 

![Timechart delle metriche in Application Insights - Analisi](./media/performance-counters/analytics-available-memory.png)

Come altri dati di telemetria, **performanceCounters** contiene anche una colonna `cloud_RoleInstance` che indica l'identità dell'istanza del server host in cui viene eseguita l'app. Ad esempio, per confrontare le prestazioni dell'applicazione su computer diversi: 

![Prestazioni segmentate per istanze del ruolo in Application Insights - Analisi](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Conteggi di ASP.NET e Application Insights

*Qual è la differenza tra il tasso di eccezione e le metriche delle eccezioni?*

* *Tasso di eccezione* è un contatore delle prestazioni del sistema. Il CLR consente di contare tutte le eccezioni gestite e non gestite generate e divide il totale in un intervallo di campionamento per la lunghezza dell'intervallo. SDK di Application Insights raccoglie questo risultato e lo invia al portale.

* *Eccezioni* è un conteggio dei report TrackException ricevuti dal portale nell'intervallo di campionamento del grafico. Include solo le eccezioni gestite in cui sono state scritte chiamate TrackException nel codice e non include [le eccezioni non gestite](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Contatori delle prestazioni per le applicazioni in esecuzione in App Web di AzurePerformance counters for applications running in Azure Web Apps

Sia ASP.NET che ASP.NET applicazioni Core distribuite in App Web di Azure vengono eseguite in un ambiente sandbox speciale. Questo ambiente non consente l'accesso diretto ai contatori delle prestazioni del sistema. Tuttavia, un sottoinsieme limitato di contatori viene esposto come variabili di ambiente come descritto [di seguito.](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables) Application Insights SDK per ASP.NET e ASP.NET Core raccoglie i contatori delle prestazioni da App Web di Azure da queste variabili di ambiente speciali. In questo ambiente è disponibile solo un sottoinsieme di contatori e l'elenco completo è disponibile [qui.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Contatori delle prestazioni nelle applicazioni ASP.NET CorePerformance counters in ASP.NET Core applications

Il supporto per i contatori delle prestazioni in ASP.NET Core è limitato:Support for performance counters in ASP.NET Core is limited:

* [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versioni 2.4.1 e successive raccolgono i contatori delle prestazioni se l'applicazione è in esecuzione in App Web di Azure (Windows).
* SDK versioni 2.7.1 e successive raccolgono i contatori `NETSTANDARD2.0` delle prestazioni se l'applicazione è in esecuzione in Windows e di destinazione o versioni successive.
* Per le applicazioni destinate a .NET Framework, tutte le versioni di SDK supportano i contatori delle prestazioni.
* SDK versioni 2.8.0 e successive supportano il contatore cpu/memoria in Linux. Nessun altro contatore è supportato in Linux. Il modo consigliato per ottenere i contatori di sistema in Linux (e in altri ambienti non Windows) consiste [nell'utilizzare EventCounters](eventcounters.md)

## <a name="alerts"></a>Avvisi
Come per altre metriche, è possibile [impostare un avviso](../../azure-monitor/app/alerts.md) per ricevere una notifica se un contatore delle prestazioni supera un limite specificato. Aprire il riquadro Avvisi e fare clic su Aggiungi avviso.

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi

* [Rilevamento delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md)
* [Rilevamento delle eccezioni](../../azure-monitor/app/asp-net-exceptions.md)

