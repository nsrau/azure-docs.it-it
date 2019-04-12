---
title: Analizzare i dati di log in Monitoraggio di Azure | Microsoft Docs
description: È necessaria una query di log per recuperare i dati di log da Monitoraggio di Azure.  Questo articolo descrive come vengono usate le nuove query di log in Monitoraggio di Azure e illustra i concetti con cui avere familiarità prima di crearne una.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: dcac701f3c1b6d64a7017c31679c019b91103ba2
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904730"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Analizzare i dati di log in Monitoraggio di Azure

I dati del log raccolti da Monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics, che è basata su [Esplora dati di Azure](/azure/data-explorer). Vengono raccolti i dati di telemetria di diverse origini e viene usato il [linguaggio di query Kusto](/azure/kusto/query) di Esplora dati per recuperare e analizzare i dati.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="log-queries"></a>Query di log

È necessaria una query di log per recuperare i dati di log da Monitoraggio di Azure.  Indipendentemente dal fatto che l'esigenza sia quella di [analizzare i dati nel portale](portals.md), [configurare una regola di avviso](../platform/alerts-metric.md) per ricevere una notifica di una determinata condizione o recuperare dati tramite l'[API di log di Monitoraggio di Azure](https://dev.loganalytics.io/), verrà usata una query per specificare i dati necessari.  Questo articolo descrive come vengono usate le query di log in Monitoraggio di Azure e illustra i concetti con cui avere familiarità prima di crearne una.

## <a name="where-log-queries-are-used"></a>Dove si utilizzano le query di log

I diversi modi, che si userà le query in Monitoraggio di Azure includono quanto segue:

- **Portale.** È possibile eseguire un'analisi interattiva dei dati di log nel [portale di Azure](portals.md).  In questo modo è possibile modificare le query e analizzare i risultati in una vasta gamma di formati e visualizzazioni.  
- **Regole di avviso.** Le [regole di avviso](../platform/alerts-overview.md) consentono di identificare in modo proattivo i problemi dai dati nell'area di lavoro.  Ogni regola di avviso è basata su una ricerca log che viene eseguita automaticamente a intervalli regolari.  I risultati vengono esaminati per determinare se è necessario creare un avviso.
- **Dashboard.** È possibile aggiungere i risultati di qualsiasi query in un [dashboard Azure](../learn/tutorial-logs-dashboards.md) che consente di visualizzare i dati di metriche e log insieme ed eventualmente condividerli con altri utenti di Azure. 
- **Visualizzazioni.**  È possibile creare visualizzazioni dei dati da includere nei dashboard degli utenti con [Progettazione viste](../platform/view-designer.md).  Le query di log forniscono i dati usati da [riquadri](../platform/view-designer-tiles.md) e [parti della visualizzazione](../platform/view-designer-parts.md) in ogni vista.  

- **Esportazione.**  Quando si importano dati di log da Monitoraggio di Azure in Excel o [Power BI](../platform/powerbi.md), si crea una query di log per definire i dati da esportare.
- **PowerShell.** È possibile eseguire uno script di PowerShell da una riga di comando o un runbook di automazione di Azure che usa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresults) per recuperare i dati di log da monitoraggio di Azure.  Questo cmdlet richiede una query per determinare i dati da recuperare.
- **API di log di monitoraggio di Azure.**  L'[API di log di Monitoraggio di Azure](../platform/alerts-overview.md) consente a qualsiasi client API REST di recuperare dati di log dall'area di lavoro.  La richiesta dell'API include una query eseguita su Monitoraggio di Azure per determinare i dati da recuperare.

![Ricerche log](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Scrivere una query
Monitoraggio di Azure usa [una versione del linguaggio di query Kusto](get-started-queries.md) per recuperare e analizzare i dati di log in diversi modi.  Si inizia in genere con query semplici per poi usare funzioni più avanzate, man mano che i requisiti diventano più complessi.

La struttura di base di una query è costituita da una tabella di origine seguita da una serie di operatori separati dal carattere barra verticale `|`.  È possibile concatenare più operatori per eseguire operazioni di recupero dei dati e funzioni più elaborate e avanzate.

Ad esempio, si supponga di voler trovare i primi dieci computer nei quali si è verificata la maggior parte degli eventi di errore il giorno precedente.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Oppure, potrebbe essere necessario individuare i computer senza heartbeat nelle ultime 24 ore.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

Potrebbe anche essere utile un grafico a linee con l'utilizzo del processore per ogni computer nell'ultima settimana.

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

Da questi semplici esempi è possibile notare che la struttura della query è simile, indipendentemente dal tipo di dati in uso.  È possibile suddividerla in passaggi distinti, in cui i dati risultanti da un comando vengono inviati tramite la pipeline al comando successivo.

È anche possibile eseguire query sui dati nelle aree di lavoro di Log Analytics nella sottoscrizione.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-azure-monitor-log-data-is-organized"></a>Organizzazione dei dati di log di Monitoraggio di Azure
Quando si compila una query, è necessario innanzitutto determinare quali tabelle contengono i dati da cercare. I diversi tipi di dati sono suddivisi in tabelle dedicate in ciascuna [area di lavoro Log Analytics](../learn/quick-create-workspace.md).  La documentazione per le diverse origini dati include il nome del tipo di dati che crea e una descrizione di ciascuna delle relative proprietà.  Molte query richiedono solo dati da una singola tabella, ma altre possono usare numerose opzioni per includere dati da più tabelle.

Sebbene [Application Insights](../app/app-insights-overview.md) archivi i dati dell'applicazione quali richieste, eccezioni, tracce e utilizzo in log di Monitoraggio di Azure, questi dati vengono archiviati in una partizione diversa da quella degli altri dati di log. Per accedere a questi dati si utilizza lo stesso linguaggio di query, ma è necessario usare la [console di Application Insights](../app/analytics.md) o l'[API REST di Application Insights](https://dev.applicationinsights.io/). È possibile usare [query tra risorse](../log-query/cross-workspace-query.md) per combinare i dati di Application Insights con altri dati di log in Monitoraggio di Azure.


![Tabelle](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come usare [ricerche nei log di Log Analitica per creare e modificare](../log-query/portals.md).
- Vedere un'[esercitazione sulla scrittura di query](../log-query/get-started-queries.md) con il nuovo linguaggio di query.
