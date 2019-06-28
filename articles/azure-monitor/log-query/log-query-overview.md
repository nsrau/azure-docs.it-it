---
title: Panoramica del log di query in Monitoraggio di Azure | Microsoft Docs
description: Risposte alle domande più comuni correlate alla log esegue una query e consente di iniziare a inviare.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310346"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Panoramica delle query di log in Monitoraggio di Azure
Query di log consentono di sfruttare appieno il valore dei dati raccolti [log di monitoraggio di Azure](../platform/data-platform-logs.md). Un linguaggio di query avanzato consente di unire i dati da più tabelle, aggregare grandi set di dati ed eseguire operazioni complesse con quantità minima di codice. È possibile rispondere praticamente a qualsiasi domanda e l'analisi eseguita fino a quando sono stati raccolti i dati di supporti ed importante comprendere come creare query a destra.

Alcune funzionalità di monitoraggio di Azure, ad esempio [insights](../insights/insights-overview.md) e [soluzioni](../insights/solutions-inventory.md) elaborare i dati di log senza esporre è per la query sottostante. Per avvalersi pienamente altre funzionalità di monitoraggio di Azure, è necessario comprendere come le query vengono costruite e come è possibile usarli per l'analisi interattiva dei dati nei log di monitoraggio di Azure.

Usare questo articolo come punto di partenza per apprendere le query di log in Monitoraggio di Azure. Risponde a domande comuni e vengono forniti collegamenti ad altra documentazione che fornisce ulteriori dettagli e le lezioni.

## <a name="how-can-i-learn-how-to-write-queries"></a>Come ottenere informazioni sulla scrittura di query?
Se si vuole passare direttamente le cose, è possibile iniziare con le esercitazioni seguenti:

- [Introduzione a Log Analitica in Monitoraggio di Azure](get-started-portal.md).
- [Introduzione alle query di log in Monitoraggio di Azure](get-started-queries.md).

Dopo aver creato le nozioni di base verso il basso, illustrata più lezioni usando i propri dati o dati dal nostro ambiente dimostrativo inizia con: 

- [Lavorare con le stringhe di query di log di monitoraggio di Azure](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Quale lingua si effettua l'accesso usare le query?
Log di monitoraggio di Azure si basa sul [Esplora dati di Azure](/azure/data-explorer), e le query di log vengono scritti usando lo stesso linguaggio di query Kusto (KQL). Si tratta di un linguaggio avanzato progettato per essere facile da leggere e autore e si deve essere in grado di iniziare a usarlo con il minimo aiuto.

Visualizzare [documentazione di Azure Data Explorer KQL](/azure/kusto/query) per una documentazione completa su KQL e riferimento nelle diverse funzioni disponibili.<br>
Visualizzare [Introduzione alle query di log in Monitoraggio di Azure](get-started-queries.md) per una procedura dettagliata del linguaggio usando i dati di log di monitoraggio di Azure.
Visualizzare [differenze del linguaggio di query log monitoraggio di Azure](data-explorer-difference.md) per piccole differenze nella versione di KQL utilizzate da monitoraggio di Azure.

## <a name="what-data-is-available-to-log-queries"></a>Quali dati sono disponibili per le query di log?
Tutti i dati raccolti nei log di monitoraggio di Azure è disponibile per recuperare e analizzare in query di log. I dati verranno scritti in tabelle diverse origini dati diverse, ma è possibile includere più tabelle in una singola query per analizzare i dati in più origini. Quando si compila una query, è necessario innanzitutto determinare quali tabelle presentano i dati che si sta cercando, quindi si deve avere almeno una conoscenza di base di strutturazione dei dati nei log di monitoraggio di Azure.

Visualizzare [origini dei log di monitoraggio di Azure](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), per un elenco di dati diverse origini che popola i log di monitoraggio di Azure.<br>
Visualizzare [struttura dei log di monitoraggio di Azure](logs-structure.md) per una spiegazione del modo in cui i dati sono strutturati.

## <a name="what-does-a-log-query-look-like"></a>Una query di log aspetto?
Una query potrebbe essere semplice come nome di una tabella per il recupero di tutti i record della tabella:

```Kusto
Syslog
```

Oppure è stato possibile filtrare i record specifico, li riepilogare e visualizzare i risultati in un grafico:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Per un'analisi più complessa, è possibile recuperare dati da più tabelle tramite un join per analizzare i risultati tra loro.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Anche se non si ha familiarità con KQL, sarà possibile capire almeno la logica di base utilizzata da queste query. Si inizia con il nome di una tabella e quindi aggiungere più comandi per filtrare ed elaborare tali dati. Una query può usare qualsiasi numero di comandi ed è possibile scrivere query più complesse come acquisire familiarità con i diversi comandi KQL disponibili.

Visualizzare [Introduzione alle query di log in Monitoraggio di Azure](get-started-queries.md) per un'esercitazione introduttiva il linguaggio e funzioni comuni, sulle query di log.<br>


## <a name="what-is-log-analytics"></a>Cos'è Log Analytics?
Log Analitica è lo strumento principale nel portale di Azure per la scrittura di query di log e analizzare in modo interattivo i relativi risultati. Anche se una query di log viene usata altrove in Monitoraggio di Azure, si sarà in genere scrivere e testare la query prima di tutto tramite Log Analitica.

È possibile avviare Analitica di Log da diverse posizioni nel portale di Azure. L'ambito dei dati disponibili per Log Analitica dipende dal modo in cui iniziare. Visualizzare [ambito Query](scope.md) per altri dettagli.

- Selezionare **registri** dal **monitoraggio di Azure** dal menu o **aree di lavoro di Log Analitica** menu.
- Selezionare **Analitica** dalle **Panoramica** page di un'applicazione di Application Insights.
- Selezionare **registri** dal menu di scelta di una risorsa di Azure.

![Log Analytics](media/log-query-overview/log-analytics.png)

Visualizzare [Introduzione a Log Analitica in Monitoraggio di Azure](get-started-portal.md) per una procedura dettagliata di Analitica di Log che vengono presentate alcune delle sue caratteristiche.

## <a name="where-else-are-log-queries-used"></a>In caso contrario vengono usate le query di log?
Oltre a lavorare in modo interattivo con query di log e i relativi risultati nel Log Analitica, le aree in Monitoraggio di Azure in cui si useranno le query seguenti:

- **Regole di avviso.** Le [regole di avviso](../platform/alerts-overview.md) consentono di identificare in modo proattivo i problemi dai dati nell'area di lavoro.  Ogni regola di avviso è basata su una ricerca log che viene eseguita automaticamente a intervalli regolari.  I risultati vengono esaminati per determinare se è necessario creare un avviso.
- **Dashboard.** È possibile aggiungere i risultati di qualsiasi query in un [dashboard Azure](../learn/tutorial-logs-dashboards.md) che consente di visualizzare i dati di metriche e log insieme ed eventualmente condividerli con altri utenti di Azure.
- **Visualizzazioni.**  È possibile creare visualizzazioni dei dati da includere nei dashboard degli utenti con [Progettazione viste](../platform/view-designer.md).  Le query di log forniscono i dati usati da [riquadri](../platform/view-designer-tiles.md) e [parti della visualizzazione](../platform/view-designer-parts.md) in ogni vista.  
- **Esportazione.**  Quando si importano dati di log da Monitoraggio di Azure in Excel o [Power BI](../platform/powerbi.md), si crea una query di log per definire i dati da esportare.
- **PowerShell.** È possibile eseguire uno script di PowerShell da una riga di comando o un runbook di automazione di Azure che usa [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) per recuperare i dati di log da monitoraggio di Azure.  Questo cmdlet richiede una query per determinare i dati da recuperare.
- **API di log di Monitoraggio di Azure.**  L'[API di log di Monitoraggio di Azure](../platform/alerts-overview.md) consente a qualsiasi client API REST di recuperare dati di log dall'area di lavoro.  La richiesta dell'API include una query eseguita su Monitoraggio di Azure per determinare i dati da recuperare.


## <a name="next-steps"></a>Passaggi successivi
- Esaminare una [esercitazione sull'uso di Log Analitica nel portale di Azure](get-started-portal.md).
- Esaminare una [esercitazione sulla scrittura di query](get-started-queries.md).
