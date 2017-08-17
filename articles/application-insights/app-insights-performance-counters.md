---
title: Contatori delle prestazioni in Application Insights | Documentazione Microsoft
description: Sistema di monitoraggio e contatori delle prestazioni .NET personalizzati in Application Insights.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 2bcdff44f6c3169779c3dd018706eea8275825ca
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017

---
# <a name="system-performance-counters-in-application-insights"></a>Contatori delle prestazioni di sistema in Application Insights
Windows offre un'ampia gamma di [contatori delle prestazioni](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters), ad esempio su occupazione della CPU, memoria, disco e utilizzo di rete. È anche possibile definire contatori personalizzati. [Application Insights](app-insights-overview.md) può mostrare questi contatori delle prestazioni se l'applicazione viene eseguita in IIS in un host locale o in una macchina virtuale a cui si ha accesso come amministratore. I grafici indicano le risorse disponibili per l'applicazione live e possono aiutare a identificare un eventuale carico sbilanciato tra istanze del server.

I contatori delle prestazioni sono visualizzati nel pannello Server, che include una tabella segmentata in base all'istanza del server.

![Contatori delle prestazioni segnalati in Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

I contatori delle prestazioni non sono disponibili per App Web di Azure. Tuttavia, è possibile [inviare i dati del servizio Diagnostica di Azure ad Application Insights](app-insights-azure-diagnostics.md).

## <a name="view-counters"></a>Visualizzare i contatori
Il pannello Server mostra un set predefinito di contatori delle prestazioni. 

Per visualizzare altri contatori, modificare i grafici nel pannello Server oppure aprire un altro riquadro [Esplora metriche](app-insights-metrics-explorer.md) e aggiungere nuovi grafici. 

I contatori disponibili sono elencati come metriche quando si modifica un grafico.

![Contatori delle prestazioni segnalati in Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Per visualizzare tutti i grafici più utili in un'unica posizione, creare un [dashboard](app-insights-dashboards.md) e aggiungervi i grafici.

## <a name="add-counters"></a>Aggiungere contatori
Se il contatore delle prestazioni desiderato non appare nell'elenco delle metriche, significa che non viene raccolto da Application Insights SDK nel server Web. È possibile configurare l'SDK a questo scopo.

1. È possibile identificare i contatori disponibili nel server usando questo comando di PowerShell nel server:
   
    `Get-Counter -ListSet *`
   
    Vedere [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).
2. Aprire ApplicationInsights.config.
   
   * Se Application Insights è stato aggiunto all'app durante lo sviluppo, modificare ApplicationInsights.config nel progetto e quindi distribuirlo di nuovo nei server.
   * Se è stato usato Status Monitor per instrumentare un'app Web al runtime, trovare ApplicationInsights.config nella directory radice dell'app in IIS. Aggiornarlo qui in ogni istanza del server.
3. Modificare la direttiva dell'agente di raccolta delle prestazioni:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

È possibile acquisire contatori standard e quelli implementati manualmente. `\Objects\Processes` è un esempio di contatore standard, disponibile in tutti i sistemi Windows. `\Sales(photo)\# Items Sold` è un esempio di contatore personalizzato che può essere implementato in un servizio Web. 

Il formato è `\Category(instance)\Counter"` oppure, per categorie non associate a istanze, solo `\Category\Counter`.

`ReportAs` è necessario per i nomi dei contatori che non corrispondono a `[a-zA-Z()/-_ \.]+`, ovvero che contengono caratteri che non sono inclusi in questi set: lettere, parentesi tonde, barra, trattino, carattere di sottolineatura, spazio e punto.

Se si specifica un'istanza, questa verrà raccolta come dimensione "CounterInstanceName" della metrica indicata.

### <a name="collecting-performance-counters-in-code"></a>Raccogliere contatori delle prestazioni nel codice
Per raccogliere i contatori delle prestazioni di sistema e inviarli ad Application Insights, è possibile adattare il frammento di codice seguente:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

In alternativa, è possibile eseguire la stessa operazione con le metriche personalizzate create:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Contatori delle prestazioni in Analytics
È possibile cercare e visualizzare report dei contatori delle prestazioni in [Analytics](app-insights-analytics.md).

Lo schema **performanceCounters** espone `category`, il nome `counter` e il nome `instance` per ogni contatore delle prestazioni.  Nei dati di telemetria per ogni applicazione verranno visualizzati solo i contatori per l'applicazione specifica. Ad esempio, per visualizzare quali contatori sono disponibili: 

![Contatori delle prestazioni in Application Insights - Analisi](./media/app-insights-performance-counters/analytics-performance-counters.png)

"Istanza" qui fa riferimento all'istanza del contatore delle prestazioni e non all'istanza del ruolo o del server. Il nome dell'istanza del contatore delle prestazioni segmenta in genere i contatori, come quello per il tempo del processore, in base al nome del processo o dell'applicazione.

Per ottenere un grafico della memoria disponibile nel periodo recente: 

![Timechart delle metriche in Application Insights - Analisi](./media/app-insights-performance-counters/analytics-available-memory.png)

Come altri dati di telemetria, **performanceCounters** contiene anche una colonna `cloud_RoleInstance` che indica l'identità dell'istanza del server host in cui viene eseguita l'app. Ad esempio, per confrontare le prestazioni dell'applicazione su computer diversi: 

![Prestazioni segmentate per istanze del ruolo in Application Insights - Analisi](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Conteggi di ASP.NET e Application Insights
*Qual è la differenza tra il tasso di eccezione e le metriche delle eccezioni?*

* *Tasso di eccezione* è un contatore delle prestazioni del sistema. Il CLR consente di contare tutte le eccezioni gestite e non gestite generate e divide il totale in un intervallo di campionamento per la lunghezza dell'intervallo. SDK di Application Insights raccoglie questo risultato e lo invia al portale.
* *Eccezioni* è un conteggio dei report TrackException ricevuti dal portale nell'intervallo di campionamento del grafico. Include solo le eccezioni gestite in cui sono state scritte chiamate TrackException nel codice e non include [le eccezioni non gestite](app-insights-asp-net-exceptions.md). 

## <a name="alerts"></a>Avvisi
Come per altre metriche, è possibile [impostare un avviso](app-insights-alerts.md) per ricevere una notifica se un contatore delle prestazioni supera un limite specificato. Aprire il pannello Avvisi e fare clic su Aggiungi avviso.

## <a name="next"></a>Passaggi successivi
* [Rilevamento delle dipendenze](app-insights-asp-net-dependencies.md)
* [Rilevamento delle eccezioni](app-insights-asp-net-exceptions.md)


