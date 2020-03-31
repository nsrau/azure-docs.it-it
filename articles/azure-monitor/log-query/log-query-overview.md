---
title: Panoramica delle query di log in Monitoraggio di Azure Documenti Microsoft
description: Risponde alle domande comuni relative alle query di log e viene iniziato a utilizzarle.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670118"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Panoramica delle query di log in Monitoraggio di AzureOverview of log queries in Azure Monitor
Le query di log consentono di sfruttare appieno il valore dei dati raccolti nei log di monitoraggio di [Azure.](../platform/data-platform-logs.md) Un potente linguaggio di query consente di unire dati da più tabelle, aggregare set di dati di grandi dimensioni ed eseguire operazioni complesse con codice minimo. Praticamente qualsiasi domanda può essere risolta e l'analisi eseguita fino a quando i dati di supporto sono stati raccolti, e si capisce come costruire la query giusta.

Alcune funzionalità di Monitoraggio di Azure, ad esempio [informazioni dettagliate](../insights/insights-overview.md) e [soluzioni,](../insights/solutions-inventory.md) elaborano i dati del log senza esporre l'utente alle query sottostanti. To fully leverage other features of Azure Monitor, you should understand how queries are constructed and how you can use them to interactively analyze data in Azure Monitor Logs.

Usare questo articolo come punto di partenza per ottenere informazioni sulle query di log in Monitoraggio di Azure.Use this article as a starting point to learning about log queries in Azure Monitor. Risponde alle domande più comuni e fornisce collegamenti ad altra documentazione che fornisce ulteriori dettagli e lezioni.

## <a name="how-can-i-learn-how-to-write-queries"></a>Come posso imparare a scrivere query?
Se si desidera saltare a destra nelle cose, si può iniziare con i seguenti tutorial:

- [Introduzione a Log Analytics in Monitoraggio di Azure.](get-started-portal.md)
- [Introduzione alle query di log in Monitoraggio di Azure](get-started-queries.md).

Una volta che hai le nozioni di base verso il basso, camminare attraverso più lezioni utilizzando i propri dati o dati dal nostro ambiente demo a partire da: 

- [Usare le stringhe nelle query di log di Monitoraggio di Azure](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Quale lingua utilizzano le query di log?
I log di Monitoraggio di Azure sono basati su [Azure Data Explorer](/azure/data-explorer)e le query di log vengono scritte usando lo stesso linguaggio di query Kusto (KQL). Questo è un linguaggio ricco progettato per essere facile da leggere e autore, e si dovrebbe essere in grado di iniziare a usarlo con una guida minima.

Vedere la [documentazione KQL](/azure/kusto/query) di Azure Data Explorer per la documentazione completa su KQL e informazioni di riferimento sulle diverse funzioni disponibili.<br>
Vedere Introduzione alle query di log in Monitoraggio di Azure per una rapida procedura dettagliata del linguaggio usando i dati dai log di Monitoraggio di Azure.See [Get started with log queries in Azure Monitor](get-started-queries.md) for a quick walkthrough of the language using data from Azure Monitor Logs.
Vedere Differenze del [linguaggio](data-explorer-difference.md) di query del log di Monitoraggio di Azure per differenze minori nella versione di KQL usata da Monitoraggio di Azure.See Azure Monitor log query language differences for minor differences in the version of KQL used by Azure Monitor.

## <a name="what-data-is-available-to-log-queries"></a>Quali dati sono disponibili per registrare le query?
Tutti i dati raccolti nei log di Monitoraggio di Azure sono disponibili per il recupero e l'analisi nelle query di log. Origini dati diverse scriveranno i dati in tabelle diverse, ma è possibile includere più tabelle in una singola query per analizzare i dati tra più origini. Quando si compila una query, si inizia determinando quali tabelle conto dei dati che si stanno cercando, pertanto è necessario avere almeno una conoscenza di base della struttura dei dati nei log di monitoraggio di Azure.When you build a query, you start by determining which tables have the data that you're looking to, so you should have at least a basic understanding of how data in Azure Monitor Logs is structured.

Vedere Origini dei log di monitoraggio di Azure per un elenco di origini dati diverse che popolano i log di Monitoraggio di Azure.See [Sources of Azure Monitor Logs](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), for a list of different data sources that populate Azure Monitor Logs.<br>
Vedere [Struttura dei log di Monitoraggio](logs-structure.md) di Azure per una spiegazione della struttura dei dati.

## <a name="what-does-a-log-query-look-like"></a>Che aspetto ha una query di log?
Una query può essere semplice come un singolo nome di tabella per recuperare tutti i record da tale tabella:A query could be as simple as a single table name for retrieving all records from that table:

```Kusto
Syslog
```

Oppure potrebbe filtrare record specifici, riepilogarli e visualizzare i risultati in un grafico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Per un'analisi più complessa, è possibile recuperare i dati da più tabelle usando un join per analizzare i risultati insieme.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Anche se non hai familiarità con KQL, dovresti essere in grado di capire almeno la logica di base utilizzata da queste query. Iniziano con il nome di una tabella e quindi aggiungono più comandi per filtrare ed elaborare i dati. Una query può utilizzare un numero qualsiasi di comandi ed è possibile scrivere query più complesse man mano che si acquisisce familiarità con i diversi comandi KQL disponibili.

Per un'esercitazione sulle query di log che introduce il linguaggio e le funzioni comuni, vedere Introduzione alle query di [log in Monitoraggio di Azure.See Get started with log queries in Azure Monitor](get-started-queries.md) for a tutorial on log queries that introduces the language and common functions, .<br>


## <a name="what-is-log-analytics"></a>Cos'è Log Analytics?
Log Analytics è lo strumento principale nel portale di Azure per la scrittura di query di log e l'analisi interattiva dei risultati. Anche se una query di log viene utilizzata altrove in Monitoraggio di Azure, normalmente la query viene prima scritta e testata usando Log Analytics.

È possibile avviare Log Analytics da diverse posizioni nel portale di Azure.You can start Log Analytics from several places in the Azure portal. L'ambito dei dati disponibili per Log Analytics è determinato dalla modalità di avvio. Per ulteriori dettagli, vedere [Ambito della query.](scope.md)

- Selezionare Log dal menu **Monitoraggio di Azure** o dal menu Delle aree di lavoro di Log Analytics.Select **Logs** from the Azure Monitor menu or Log Analytics **workspaces** menu.
- Selezionare Analytics nella pagina **Panoramica** di un'applicazione Application Insights.Select **Analytics** from the Overview page of an Application Insights application.
- Selezionare Registri dal menu di una risorsa di Azure.Select **Logs** from the menu of an Azure resource.

![Log Analytics](media/log-query-overview/log-analytics.png)

Vedere Introduzione a [Log Analytics in Monitoraggio di Azure](get-started-portal.md) per una procedura dettagliata di Log Analytics che introduce molte delle relative funzionalità.

## <a name="where-else-are-log-queries-used"></a>In quale altro luogo vengono utilizzate le query di log?
Oltre a lavorare in modo interattivo con le query di log e i relativi risultati in Log Analytics, le aree di Monitoraggio di Azure in cui si useranno le query includono quanto segue:In addition to interactively working with log queries and their results in Log Analytics, areas in Azure Monitor where you will use queries include the following:

- **Regole di avviso.** Le [regole di avviso](../platform/alerts-overview.md) consentono di identificare in modo proattivo i problemi dai dati nell'area di lavoro.  Ogni regola di avviso è basata su una ricerca log che viene eseguita automaticamente a intervalli regolari.  I risultati vengono esaminati per determinare se è necessario creare un avviso.
- **Dashboard.** È possibile aggiungere i risultati di qualsiasi query in un [dashboard Azure](../learn/tutorial-logs-dashboards.md) che consente di visualizzare i dati di metriche e log insieme ed eventualmente condividerli con altri utenti di Azure.
- **Visualizzazioni.**  È possibile creare visualizzazioni dei dati da includere nei dashboard degli utenti con [Progettazione viste](../platform/view-designer.md).  Le query di log forniscono i dati usati da [riquadri](../platform/view-designer-tiles.md) e [parti della visualizzazione](../platform/view-designer-parts.md) in ogni vista.  
- **Esportazione.**  Quando si importano dati di log da Monitoraggio di Azure in Excel o [Power BI](../platform/powerbi.md), si crea una query di log per definire i dati da esportare.
- **Powershell.** È possibile eseguire uno script di PowerShell da una riga di comando o un runbook di Automazione di Azure che usa Get-AzOperationalInsightsSearchResults per recuperare i dati del log da Monitoraggio di Azure.You can run a PowerShell script from a command line or an Azure Automation runbook that uses [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) to retrieve log data from Azure Monitor.  Questo cmdlet richiede una query per determinare i dati da recuperare.
- **API di log di Monitoraggio di Azure.**  L'[API di log di Monitoraggio di Azure](https://dev.loganalytics.io) consente a qualsiasi client API REST di recuperare dati di log dall'area di lavoro.  La richiesta dell'API include una query eseguita su Monitoraggio di Azure per determinare i dati da recuperare.


## <a name="next-steps"></a>Passaggi successivi
- Eseguire [un'esercitazione sull'uso](get-started-portal.md)di Log Analytics nel portale di Azure.Walk through a tutorial on using Log Analytics in the Azure portal .
- Scorrere [un'esercitazione sulla scrittura di query](get-started-queries.md).
