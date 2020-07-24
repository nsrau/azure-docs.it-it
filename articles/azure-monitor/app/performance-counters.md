---
title: Contatori delle prestazioni in Application Insights | Documentazione Microsoft
description: Sistema di monitoraggio e contatori delle prestazioni .NET personalizzati in Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.openlocfilehash: e60663d9e767db020fc93eba1f4c1c6babb32294
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024419"
---
# <a name="system-performance-counters-in-application-insights"></a>Contatori delle prestazioni di sistema in Application Insights

Windows offre un'ampia gamma di [contatori delle prestazioni](/windows/desktop/perfctrs/about-performance-counters), ad esempio su occupazione della CPU, memoria, disco e utilizzo di rete. È anche possibile definire contatori delle prestazioni personalizzati. La raccolta dei contatori delle prestazioni è supportata se l'applicazione viene eseguita in IIS in un host locale o in una macchina virtuale a cui si ha accesso come amministratore. Sebbene le applicazioni in esecuzione come App Web di Azure non dispongano di accesso diretto ai contatori delle prestazioni, Application Insights raccoglie un subset di contatori disponibili.

## <a name="view-counters"></a>Visualizzare i contatori

Il riquadro Metrica mostra un set predefinito di contatori delle prestazioni.

![Contatori delle prestazioni segnalati in Application Insights](./media/performance-counters/performance-counters.png)

I contatori predefiniti attualmente configurati per essere raccolti per le applicazioni Web ASP.NET/ASP.NET Core sono:
- % tempo processo\\processore
- % tempo\\processore processo normalizzato
- Byte disponibili\\memoria
- Richieste ASP.NET/sec
- Eccezioni .NET CLR generate/sec
- Tempo di esecuzione ApplicationsRequest ASP.NET
- Byte elaborazione\\privati
- Byte elaborazione\\dati IO/sec
- Applicazioni ASP.NET\\Richieste in coda applicazione
- Processore(_Total)\\% tempo processore

## <a name="add-counters"></a>Aggiungere contatori

Se il contatore delle prestazioni desiderato non è incluso nell'elenco delle metriche, è possibile aggiungerlo.

1. È possibile identificare i contatori disponibili nel server usando questo comando di PowerShell nel server locale:

    `Get-Counter -ListSet *`

    Vedere [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter?view=powershell-5.1).
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
> Le applicazioni ASP.NET Core non dispongono di `ApplicationInsights.config`, perciò il metodo precedente non è valido per applicazioni ASP.NET Core.

È possibile acquisire contatori standard e quelli implementati manualmente. `\Objects\Processes` è un esempio di contatore standard, disponibile in tutti i sistemi Windows. `\Sales(photo)\# Items Sold` è un esempio di contatore personalizzato che può essere implementato in un servizio Web.

Il formato è `\Category(instance)\Counter"` oppure, per categorie non associate a istanze, solo `\Category\Counter`.

`ReportAs` è necessario per i nomi dei contatori che non corrispondono a `[a-zA-Z()/-_ \.]+`, ovvero che contengono caratteri che non sono inclusi in questi set: lettere, parentesi tonde, barra, trattino, carattere di sottolineatura, spazio e punto.

Se si specifica un'istanza, questa verrà raccolta come dimensione "CounterInstanceName" della metrica indicata.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Raccolta dei contatori delle prestazioni nel codice per applicazioni Web ASP.NET o applicazioni Console .NET/.NET Core
Per raccogliere i contatori delle prestazioni di sistema e inviarli ad Application Insights, è possibile adattare il frammento di codice seguente:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

In alternativa, è possibile eseguire la stessa operazione con le metriche personalizzate create:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Raccolta dei contatori delle prestazioni nel codice per applicazioni Web ASP.NET Core

Modificare il metodo `ConfigureServices` nella classe `Startup.cs` come indicato di seguito.

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
È possibile cercare e visualizzare report dei contatori delle prestazioni in [Analytics](../log-query/log-query-overview.md).

Lo schema **performanceCounters** espone `category`, il nome `counter` e il nome `instance` per ogni contatore delle prestazioni.  Nei dati di telemetria per ogni applicazione verranno visualizzati solo i contatori per l'applicazione specifica. Ad esempio, per visualizzare quali contatori sono disponibili: 

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

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Contatori delle prestazioni per le applicazioni in esecuzione in App Web di Azure

Le applicazioni ASP.NET e ASP.NET Core distribuite in App Web di Azure vengono eseguite in un ambiente sandbox speciale. Questo ambiente non consente l'accesso diretto ai contatori delle prestazioni di sistema. Un subset limitato di contatori, tuttavia, viene esposto come variabile di ambiente, come descritto [qui](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK per ASP.NET e ASP.NET Core raccoglie i contatori delle prestazioni da App Web di Azure da queste variabili di ambiente speciali. In questo ambiente è disponibile solo un subset di contatori ed è possibile trovare l'elenco completo [qui](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs).

## <a name="performance-counters-in-aspnet-core-applications"></a>Contatori delle prestazioni nelle applicazioni ASP.NET Core

Il supporto dei contatori delle prestazioni nelle applicazioni ASP.NET Core è limitato:

* Le versioni di [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1 e successive raccolgono i contatori delle prestazioni se l'applicazione è in esecuzione in App Web di Azure (Windows).
* Le versioni di SDK 2.7.1 e successive raccolgono contatori delle prestazioni se l'applicazione è in esecuzione in Windows e ha come destinazione `NETSTANDARD2.0` o versioni successive.
* Per le applicazioni destinate a .NET Framework, tutte le versioni di SDK supportano i contatori delle prestazioni.
* Le versioni di SDK 2.8.0 e successive supportano il contatore della cpu/memoria in Linux. Linux non supporta nessun altro contatore. Per ottenere i contatori di sistema in Linux (e in altri ambienti non Windows), usare [Contatore eventi](eventcounters.md)

## <a name="alerts"></a>Avvisi
Come per altre metriche, è possibile [impostare un avviso](../../azure-monitor/platform/alerts-log.md) per ricevere una notifica se un contatore delle prestazioni supera un limite specificato. Aprire il riquadro Avvisi e fare clic su Aggiungi avviso.

## <a name="next-steps"></a><a name="next"></a>Passaggi successivi

* [Rilevamento delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md)
* [Rilevamento delle eccezioni](../../azure-monitor/app/asp-net-exceptions.md)
