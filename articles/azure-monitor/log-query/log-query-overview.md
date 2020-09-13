---
title: Panoramica delle query di log in monitoraggio di Azure | Microsoft Docs
description: Risposte alle domande comuni relative alle query di log e all'avvio dell'uso.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 5a47de51a90d926b47f6b465d6c10111407da9c6
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033167"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Panoramica delle query di log in monitoraggio di Azure
Le query di log consentono di sfruttare appieno il valore dei dati raccolti nei [log di monitoraggio di Azure](../platform/data-platform-logs.md). Un linguaggio di query avanzato consente di unire dati da più tabelle, aggregare set di dati di grandi dimensioni ed eseguire operazioni complesse con codice minimo. È possibile rispondere a qualsiasi domanda ed eseguire l'analisi fino a quando i dati di supporto sono stati raccolti e si comprende come costruire la query corretta.

Alcune funzionalità di monitoraggio di Azure, ad esempio [Insights](../insights/insights-overview.md) e [Solutions](../monitor-reference.md) , elaborano i dati di log senza esporre le query sottostanti. Per sfruttare appieno le altre funzionalità di monitoraggio di Azure, è necessario comprendere come vengono costruite le query e come usarle per analizzare in modo interattivo i dati nei log di monitoraggio di Azure.

Usare questo articolo come punto di partenza per acquisire familiarità con le query di log in monitoraggio di Azure. Risponde a domande comuni e fornisce collegamenti ad altra documentazione che fornisce ulteriori dettagli e lezioni.

## <a name="how-can-i-learn-how-to-write-queries"></a>Come è possibile imparare a scrivere query?
Se si vuole passare direttamente a qualcosa, è possibile iniziare con le esercitazioni seguenti:

- [Inizia a usare log Analytics in monitoraggio di Azure](get-started-portal.md).
- [Introduzione alle query di log in monitoraggio di Azure](get-started-queries.md).

Una volta apportate le nozioni di base, esaminare più lezioni usando i dati o i dati dell'ambiente demo a partire da: 

- [Usare le stringhe nelle query di log di Monitoraggio di Azure](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Che lingua usa le query di log?
I log di monitoraggio di Azure si basano su [Esplora dati di Azure](/azure/data-explorer)e le query di log vengono scritte usando lo stesso linguaggio di query kusto (KQL). Si tratta di un linguaggio avanzato progettato per essere facile da leggere e creare e dovrebbe essere possibile iniziare a usarlo con istruzioni minime.

Vedere la [documentazione di Azure Esplora dati KQL](/azure/kusto/query) per la documentazione completa su KQL e informazioni di riferimento sulle diverse funzioni disponibili.<br>
Per una rapida procedura dettagliata del linguaggio usando i dati dei log di monitoraggio di Azure, vedere [Introduzione alle query di log in monitoraggio di Azure](get-started-queries.md) .
Vedere le [differenze del linguaggio di query del log di monitoraggio di Azure](data-explorer-difference.md) per le differenze minime nella versione di KQL usata da monitoraggio di Azure.

## <a name="what-data-is-available-to-log-queries"></a>Quali dati sono disponibili per le query di log?
Tutti i dati raccolti nei log di monitoraggio di Azure sono disponibili per il recupero e l'analisi nelle query di log. Origini dati diverse scriveranno i dati in tabelle diverse, ma è possibile includere più tabelle in una singola query per analizzare i dati in più origini. Quando si compila una query, è necessario innanzitutto determinare quali tabelle contengono i dati da cercare. Per una spiegazione del modo in cui sono strutturati i dati, vedere [struttura dei log di monitoraggio di Azure](logs-structure.md) .

## <a name="what-does-a-log-query-look-like"></a>Che aspetto ha una query di log?
Una query può essere semplice come un nome di tabella singolo per recuperare tutti i record da tale tabella:

```Kusto
Syslog
```

In alternativa, è possibile filtrare per record specifici, riepilogarli e visualizzare i risultati in un grafico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Per un'analisi più complessa, è possibile recuperare i dati da più tabelle utilizzando un join per analizzare i risultati insieme.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Anche se non si ha familiarità con KQL, si dovrebbe essere in grado di determinare almeno la logica di base usata da queste query. Iniziano con il nome di una tabella e quindi aggiungono più comandi per filtrare ed elaborare i dati. Una query può usare un numero qualsiasi di comandi ed è possibile scrivere query più complesse Man mano che si acquisiscono familiarità con i diversi comandi KQL disponibili.

Per un'esercitazione sulle query di log che introduce il linguaggio e le funzioni comuni, vedere [Introduzione alle query di log in monitoraggio di Azure](get-started-queries.md) .<br>


## <a name="what-is-log-analytics"></a>Cos'è Log Analytics?
Log Analytics è lo strumento principale nel portale di Azure per scrivere query di log e analizzarne i risultati in modo interattivo. Anche se una query di log viene utilizzata altrove in Monitoraggio di Azure, normalmente la query viene prima scritta e testata usando Log Analytics.

È possibile avviare Log Analytics da diverse posizioni nell'portale di Azure. L'ambito dei dati disponibili per Log Analytics è determinato dalla modalità di avvio. Per altri dettagli, vedere [ambito della query](scope.md) .

- Selezionare **registri** dal menu **monitoraggio di Azure** o dal menu **aree di lavoro log Analytics** .
- Selezionare **log** dalla pagina **Panoramica** di un'applicazione Application Insights.
- Selezionare **registri** dal menu di una risorsa di Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Per una procedura dettagliata relativa all'Log Analytics in cui sono state introdotte diverse funzionalità, vedere [Introduzione a log Analytics in monitoraggio di Azure](get-started-portal.md) .

## <a name="where-else-are-log-queries-used"></a>In quali altri casi vengono usate le query di log?
Oltre a lavorare in modo interattivo con le query di log e i relativi risultati in Log Analytics, le aree di monitoraggio di Azure in cui si utilizzeranno le query includono quanto segue:

- **Regole di avviso.** Le [regole di avviso](../platform/alerts-overview.md) consentono di identificare in modo proattivo i problemi dai dati nell'area di lavoro.  Ogni regola di avviso è basata su una ricerca log che viene eseguita automaticamente a intervalli regolari.  I risultati vengono esaminati per determinare se è necessario creare un avviso.
- **Dashboard.** È possibile aggiungere i risultati di qualsiasi query in un [dashboard Azure](../learn/tutorial-logs-dashboards.md) che consente di visualizzare i dati di metriche e log insieme ed eventualmente condividerli con altri utenti di Azure.
- **Visualizzazioni.**  È possibile creare visualizzazioni dei dati da includere nei dashboard degli utenti con [Progettazione viste](../platform/view-designer.md).  Le query di log forniscono i dati usati da [riquadri](../platform/view-designer-tiles.md) e [parti della visualizzazione](../platform/view-designer-parts.md) in ogni vista.  
- **Esportazione.**  Quando si importano dati di log da Monitoraggio di Azure in Excel o [Power BI](../platform/powerbi.md), si crea una query di log per definire i dati da esportare.
- **PowerShell.** È possibile eseguire uno script di PowerShell da una riga di comando o da un Runbook di automazione di Azure che usa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) per recuperare i dati di log da monitoraggio di Azure.  Questo cmdlet richiede una query per determinare i dati da recuperare.
- **API di log di Monitoraggio di Azure.**  L'[API di log di Monitoraggio di Azure](https://dev.loganalytics.io) consente a qualsiasi client API REST di recuperare dati di log dall'area di lavoro.  La richiesta dell'API include una query eseguita su Monitoraggio di Azure per determinare i dati da recuperare.


## <a name="next-steps"></a>Passaggi successivi
- Seguire un'[esercitazione sull'uso di Log Analytics nel portale di Azure](get-started-portal.md).
- Seguire un'[esercitazione sulla scrittura di query](get-started-queries.md).
