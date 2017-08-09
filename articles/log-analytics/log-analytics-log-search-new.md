---
title: Ricerche log in OMS Log Analytics| Microsoft Docs
description: "Una ricerca log è necessaria per recuperare qualsiasi tipo di dati da Log Analytics.  Questo articolo descrive come vengono usate le nuove ricerche log in Log Analytics e illustra i concetti con cui occorre avere familiarità prima di crearne una."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 07f8189d1380f5956db8614e82b8b704b152fb26
ms.contentlocale: it-it
ms.lasthandoff: 07/28/2017

---
# <a name="understanding-log-searches-in-log-analytics"></a>Informazioni sulle ricerche log in Log Analytics

> [!NOTE]
> Questo articolo descrive le ricerche log in Azure Log Analytics tramite il nuovo linguaggio di query.  In [Aggiornare l'area di lavoro di Azure Log Analytics alla nuova ricerca log](log-analytics-log-search-upgrade.md) sono disponibili altre informazioni sul nuovo linguaggio e istruzioni per l'aggiornamento dell'area di lavoro.  
>
> Se l'area di lavoro non è stata aggiornata al nuovo linguaggio di query, è consigliabile consultare [Trovare dati tramite ricerche nei log in Log Analytics](log-analytics-log-searches.md).

Una ricerca log è necessaria per recuperare qualsiasi tipo di dati da Log Analytics.  Indipendentemente dal fatto che l'esigenza sia quella di analizzare i dati nel portale, configurare una regola di avviso per ricevere una notifica di una determinata condizione o di recuperare dati tramite l'API di Log Analytics, per specificare i dati necessari si userà una ricerca log.  Questo articolo descrive come vengono usate le ricerche log in Log Analytics e illustra i concetti con cui occorre avere familiarità prima di crearne una. Vedere la sezione [Passaggi successivi](#next-steps) per informazioni dettagliate sulla creazione e la modifica delle ricerche log e per informazioni di riferimento sul linguaggio di query.

## <a name="where-log-searches-are-used"></a>Possibili usi delle ricerche log

Di seguito sono indicati alcuni dei vari modi in cui verranno usate le ricerche log in Log Analytics:

- **Portali.** È possibile eseguire un'analisi interattiva dei dati nel repository con il [portale per la ricerca log](log-analytics-log-search-log-search-portal.md) o il [portale Advanced Analytics](https://docs.loganalytics.io/learn/tutorial_getting_started_with_analytics_portal.html).  In questo modo è possibile modificare le query e analizzare i risultati in una vasta gamma di formati e visualizzazioni.  Per la maggior parte delle query create si inizia in uno dei portali, per poi copiarle dopo aver verificato che il funzionamento corrisponde alle esigenze.
- **Regole di avviso.** Le [regole di avviso](log-analytics-alerts.md) consentono di identificare in modo proattivo i problemi dai dati nell'area di lavoro.  Ogni regola di avviso è basata su una ricerca log che viene eseguita automaticamente a intervalli regolari.  I risultati vengono esaminati per determinare se è necessario creare un avviso.
- **Visualizzazioni.**  È possibile creare visualizzazioni dei dati da includere nei dashboard degli utenti con [Progettazione viste](log-analytics-view-designer.md).  Le ricerche log forniscono i dati usati dai [riquadri](log-analytics-view-designer-tiles.md) e dalle [parti della visualizzazione](log-analytics-view-designer-parts.md) in ogni visualizzazione.  È possibile eseguire il drill-down dalle parti della visualizzazione nel portale per la ricerca log per eseguire ulteriori analisi dei dati.
- **Esportazione.**  Quando si esportano dati dall'area di lavoro di Log Analytics in Excel o [Power BI](log-analytics-powerbi.md), si crea una ricerca log per definire i dati da esportare.
- **PowerShell.** È possibile eseguire uno script di PowerShell da una riga di comando o un runbook di Automazione di Azure che usa [Get AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) per recuperare dati da Log Analytics.  Questo cmdlet richiede una query per determinare i dati da recuperare.
- **API di Log Analytics.**  L'[API di ricerca log di Log Analytics](log-analytics-log-search-api.md) consente a qualsiasi client API REST di recuperare dati da un'area di lavoro.  La richiesta dell'API include una query eseguita su Log Analytics per determinare i dati da recuperare.

![Ricerche log](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Organizzazione dei dati di Log Analytics
Quando si compila una query, è necessario innanzitutto determinare quali tabelle contengono i dati da cercare. Ogni [origine dati](log-analytics-data-sources.md) e [soluzione](../operations-management-suite/operations-management-suite-solutions.md) archivia i dati in tabelle dedicate nell'area di lavoro di Log Analytics.  La documentazione per ogni origine dati e soluzione include il nome del tipo di dati che crea e una descrizione di ciascuna delle relative proprietà.     Molte query richiedono solo dati da singole tabelle, ma altre possono usare un'ampia gamma di opzioni per includere dati da più tabelle.

![Tabelle](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>Scrittura di una query
Alla base delle ricerche log in Log Analytics vi è [un linguaggio di query completo](https://docs.loganalytics.io/) che consente di recuperare e analizzare i dati dal repository in diversi modi.  Si tratta dello stesso linguaggio di query usato per [Application Insights](../application-insights/app-insights-analytics.md).  Imparare a scrivere una query è fondamentale per la creazione di ricerche log in Log Analytics.  Si inizia in genere con query semplici per poi usare funzioni più avanzate, man mano che i requisiti diventano più complessi.

La struttura di base di una query è costituita da una tabella di origine seguita da una serie di operatori separati dal carattere barra verticale `|`.  È possibile concatenare più operatori per eseguire operazioni di recupero dei dati e funzioni più elaborate e avanzate.

Ad esempio, si supponga di voler trovare i primi dieci computer nei quali si è verificata la maggior parte degli eventi di errore il giorno precedente.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Oppure, potrebbe essere necessario individuare i computer senza heartbeat nelle ultime 24 ore.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Potrebbe anche essere utile un grafico a linee con l'utilizzo del processore per ogni computer nell'ultima settimana.

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

Da questi semplici esempi è possibile notare che la struttura della query è simile, indipendentemente dal tipo di dati in uso.  È possibile suddividerla in passaggi distinti, in cui i dati risultanti da un comando vengono inviati tramite la pipeline al comando successivo.

Per una documentazione completa sul linguaggio di query di Azure Log Analytics, incluse esercitazioni e informazioni di riferimento sul linguaggio, vedere la [documentazione del linguaggio di query di Azure Log Analytics](https://docs.loganalytics.io/).

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni sui [portali usati per creare e modifiche le ricerche log](log-analytics-log-search-portals.md).
- Vedere un'[esercitazione sulla scrittura di query](https://docs.loganalytics.io/learn/tutorial_getting_started_with_queries.html) con il nuovo linguaggio di query.

