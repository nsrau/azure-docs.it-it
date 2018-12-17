---
title: Analizzare i dati di Log Analytics in Monitoraggio di Azure | Microsoft Docs
description: Una ricerca log è necessaria per recuperare qualsiasi tipo di dati da Log Analytics.  Questo articolo descrive come vengono usate le nuove ricerche log in Log Analytics e illustra i concetti con cui occorre avere familiarità prima di crearne una.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.openlocfilehash: bc37b3b60a5ad7f4e2b4794e4fcb74c1a5004b75
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336884"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Analizzare i dati di Log Analytics in Monitoraggio di Azure

I dati del log raccolti da Monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics, che è basata su [Esplora dati di Azure](/azure/data-explorer). Vengono raccolti i dati di telemetria di diverse origini e viene usato il [linguaggio di query di Esplora dati](/azure/kusto/query) per recuperare e analizzare i dati.

> [!NOTE]
> Log Analytics in precedenza era considerato come un servizio a parte in Azure. Ora è considerato parte di Monitoraggio di Azure ed è incentrato sull'archiviazione e l'analisi dei dati dei log tramite un linguaggio di query proprio. Le funzionalità considerate parte di Log Analytics, ad esempio agenti di Windows e Linux per la raccolta dei dati, viste per la visualizzazione dei dati esistenti e avvisi per la notifica proattiva dei problemi, non sono state modificate, ma sono ora considerate parte di Monitoraggio di Azure.



## <a name="log-queries"></a>Query di log

È necessaria una query dei log per recuperare dati da Log Analytics.  Indipendentemente dal fatto che l'esigenza sia quella di [analizzare i dati nel portale](../../azure-monitor/log-query/portals.md), [configurare una regola di avviso](../../azure-monitor/platform/alerts-metric.md) per ricevere una notifica di una determinata condizione o di recuperare dati tramite l'[API di Log Analytics](https://dev.loganalytics.io/), per specificare i dati necessari si userà una query.  Questo articolo descrive come vengono usate le query di log in Log Analytics e illustra i concetti con cui occorre avere familiarità prima di crearne una.



## <a name="where-log-queries-are-used"></a>Dove si utilizzano le query di log

Di seguito sono indicati alcuni dei vari modi in cui verranno usate le query in Log Analytics:

- **Portali.** È possibile eseguire un'analisi interattiva dei dati di log nel [portale di Azure](../../azure-monitor/log-query/portals.md).  In questo modo è possibile modificare le query e analizzare i risultati in una vasta gamma di formati e visualizzazioni.  
- **Regole di avviso.** Le [regole di avviso](../../azure-monitor/platform/alerts-overview.md) consentono di identificare in modo proattivo i problemi dai dati nell'area di lavoro.  Ogni regola di avviso è basata su una ricerca log che viene eseguita automaticamente a intervalli regolari.  I risultati vengono esaminati per determinare se è necessario creare un avviso.
- **Dashboard.** È possibile aggiungere i risultati di qualsiasi query in un [dashboard Azure](../../azure-monitor/platform/dashboards.md) che consente di visualizzare i dati di metriche e log insieme ed eventualmente condividerli con altri utenti di Azure. 
- **Visualizzazioni.**  È possibile creare visualizzazioni dei dati da includere nei dashboard degli utenti con [Progettazione viste](../../azure-monitor/platform/view-designer.md).  Le query di log forniscono i dati usati da [riquadri](../../azure-monitor/platform/view-designer-tiles.md) e [parti della visualizzazione](../../azure-monitor/platform/view-designer-parts.md) in ogni vista.  
- **Esportazione.**  Quando si importano dati dall'area di lavoro di Log Analytics in Excel o [Power BI](../../azure-monitor/platform/powerbi.md), si crea una query di log per definire i dati da esportare.
- **PowerShell.** È possibile eseguire uno script di PowerShell da una riga di comando o un runbook di Automazione di Azure che usa [Get AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) per recuperare dati da Log Analytics.  Questo cmdlet richiede una query per determinare i dati da recuperare.
- **API di Log Analytics.**  L'[API di ricerca log di Log Analytics](../../azure-monitor/platform/alerts-overview.md) consente a qualsiasi client API REST di recuperare dati di log da un'area di lavoro.  La richiesta dell'API include una query eseguita su Log Analytics per determinare i dati da recuperare.

![Ricerche log](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>Scrivere una query
Log Analytics usa [una versione del linguaggio di query di Esplora dati](../../azure-monitor/log-query/get-started-queries.md) per recuperare e analizzare i dati del log in diversi modi.  Si inizia in genere con query semplici per poi usare funzioni più avanzate, man mano che i requisiti diventano più complessi.

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

## <a name="how-log-analytics-data-is-organized"></a>Organizzazione dei dati di Log Analytics
Quando si compila una query, è necessario innanzitutto determinare quali tabelle contengono i dati da cercare. I diversi tipi di dati sono suddivisi in tabelle dedicate in ciascuna [area di lavoro di Log Analytics](../../azure-monitor/learn/quick-create-workspace.md).  La documentazione per le diverse origini dati include il nome del tipo di dati che crea e una descrizione di ciascuna delle relative proprietà.  Molte query richiedono solo dati da una singola tabella, ma altre possono usare numerose opzioni per includere dati da più tabelle.

Sebbene [Application Insights](../../application-insights/app-insights-overview.md) archivi i dati dell'applicazione, ad esempio richieste, eccezioni, tracce e utilizzo, in Log Analytics, questi dati vengono archiviati in una partizione diversa da quella degli altri dati di log. Per accedere a questi dati si utilizza lo stesso linguaggio di query, ma è necessario usare la [console di Application Insights](../../application-insights/app-insights-analytics.md) o l'[API REST di Application Insights](https://dev.applicationinsights.io/). È possibile usare [le query tra risorse](../../azure-monitor/log-query/cross-workspace-query.md) per combinare i dati di Application Insights con altri dati in Log Analytics.


![Tabelle](media/log-query-overview/queries-tables.png)







## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni sui [portali usati per creare e modifiche le ricerche log](../../azure-monitor/log-query/portals.md).
- Vedere un'[esercitazione sulla scrittura di query](../../azure-monitor/log-query/get-started-queries.md) con il nuovo linguaggio di query.
