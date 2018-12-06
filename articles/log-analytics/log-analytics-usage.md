---
title: Analizzare l'utilizzo dei dati in Log Analytics | Microsoft Docs
description: Usare il dashboard Utilizzo e costi stimati di Log Analytics per valutare la quantità di dati inviata a Log Analytics e individuare le cause di aumenti imprevisti.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: e702e1f5eb1816b007317765e4c9a9f88bb99bfd
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635425"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analizzare l'utilizzo dei dati in Log Analytics

> [!NOTE]
> Questo articolo descrive come analizzare l'utilizzo dei dati in Log Analytics.  Per le informazioni correlate, vedere gli articoli seguenti.
> - [Gestire i costi controllando i volumi dei dati e la conservazione in Log Analytics](log-analytics-manage-cost-storage.md) descrive come controllare i costi modificando il periodo di conservazione dei dati.
> - [Monitorare l'utilizzo e costi stimati](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) descrive come visualizzare l'utilizzo e costi stimati tra più funzionalità di monitoraggio di Azure per diversi modelli di determinazione prezzi. Illustra inoltre come modificare il modello di prezzi.

## <a name="understand-usage"></a>Informazioni sull'utilizzo

Usare la pagina **Utilizzo e costi stimati** di Log Analytics per verificare e analizzare l'utilizzo dei dati. Questa pagina mostra quanti dati vengono raccolti da ogni soluzione, quanti dati vengono conservati e una stima dei costi in base alla quantità di dati inseriti e a eventuali altri dati conservati oltre la quantità inclusa.

![Utilizzo e costi stimati](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

Per esplorare i dati in maggiore dettaglio, fare clic sull'icona nell'angolo superiore destro di uno dei grafici nella pagina **Utilizzo e costi stimati**. Ora è possibile usare questa query per esplorare i dettagli dell'utilizzo.  

![Visualizzazione dei log](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Risoluzione dei problemi che determinano un utilizzo superiore al previsto
Un utilizzo più elevato è dovuto a una o entrambe le cause seguenti:
- Vengono inviati più dati del previsto a Log Analytics
- Più nodi del previsto inviano dati a Log Analytics oppure alcuni nodi inviano più dati del solito

Esaminiamo più nel dettaglio entrambe le cause. 

> [!NOTE]
> Benché siano ancora inclusi nello schema, alcuni campi del tipo di dati Utilizzo sono stati deprecati e i rispettivi valori non vengono più popolati. Si tratta del campo **Computer** e dei campi correlati all'inserimento, ossia **TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="data-volume"></a>Volume dati 
Nella pagina **Utilizzo e costi stimati** il grafico *Inserimento dati per soluzione* mostra il volume totale dei dati inviati e la quantità inviata da ogni soluzione. In questo modo è possibile determinare tendenze specifiche, ad esempio se l'utilizzo dei dati complessivo (o da parte di una particolare soluzione) sta aumentando, è stabile o sta diminuendo. La query usata per generare questi dati è

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Si noti che la clausola "where IsBillable = true" esclude i tipi di dati da determinate soluzioni per le quali non è addebitato alcun inserimento. 

È possibile approfondire ulteriormente l'analisi per visualizzare le tendenze relative a tipi di dati specifici, ad esempio per studiare i dati risultanti dai log di IIS:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Nodi che inviano dati

Per conoscere il numero di nodi che hanno segnalato dati nell'ultimo mese, usare

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Per visualizzare il numero di eventi inseriti per computer, usare

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Usare questa query con parsimonia in quanto la sua esecuzione è dispendiosa. Per visualizzare il numero di eventi fatturabili inseriti per computer, usare 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Per sapere quali tipi di dati fatturabili inviano dati a uno specifico computer, usare:

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

Ecco alcune query di esempio utili per analizzare in maggiore profondità l'origine dei dati di un particolare tipo di dati:

+ Soluzione **Sicurezza**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Soluzione **Gestione log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo di dati **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo di dati **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo di dati **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo di dati **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Suggerimenti per ridurre il volume di dati

Ecco alcuni suggerimenti utili per ridurre il volume dei log raccolti:

| Origine del volume di dati elevato | Come ridurre il volume di dati |
| -------------------------- | ------------------------- |
| Eventi di sicurezza            | Selezionare gli [eventi di sicurezza comuni o minimi](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) <br> Modificare i criteri di controllo di sicurezza in modo che vengano raccolti solo gli eventi necessari. In particolare, esaminare la necessità di raccogliere eventi per: <br> - [controllo piattaforma filtro](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [controllo Registro di sistema](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [controllo file system](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [controllo oggetto kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [controllo manipolazione handle](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - controllo archivi rimovibili |
| Contatori delle prestazioni       | Modificare la [configurazione del contatore delle prestazioni](log-analytics-data-sources-performance-counters.md) per: <br> - Ridurre la frequenza di raccolta <br> - Ridurre il numero di contatori delle prestazioni |
| Log eventi                 | Modificare la [configurazione del log eventi](log-analytics-data-sources-windows-events.md) per: <br> - Ridurre il numero di log eventi raccolti <br> - Raccogliere solo i livelli di eventi richiesti, ad esempio non raccogliendo gli eventi di livello *informazioni* |
| syslog                     | Modificare la [configurazione di Syslog](log-analytics-data-sources-syslog.md) per: <br> - Ridurre il numero di strutture raccolte <br> - Raccogliere solo i livelli di eventi richiesti, ad esempio non raccogliendo gli eventi di livello *informazioni* e *debug* |
| AzureDiagnostics           | Modificare la raccolta dei log delle risorse per: <br> - Ridurre il numero di risorse che inviano log a Log Analytics <br> - Raccogliere solo i log necessari |
| Dati della soluzione da computer che non richiedono la soluzione | Usare il [targeting della soluzione](../azure-monitor/insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari |

### <a name="getting-node-counts"></a>Ottenere il conteggio dei nodi 

Se si usa un piano tariffario "Per nodo (OMS)", l'addebito viene effettuato in base al numero di nodi e soluzioni usati e il numero di nodi di Informazioni dettagliate e analisi fatturati sarà visualizzato in una tabella nella pagina **Utilizzo e costi stimati**.  

Per visualizzare il numero di nodi di sicurezza distinti è possibile usare la query:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Per visualizzare il numero di nodi di Automazione distinti usare la query:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Creare un avviso quando la raccolta dati supera le dimensioni previste
Questa sezione descrive come creare un avviso nei casi seguenti:
- Il volume di dati supera una quantità specificata.
- Si prevede che il volume di dati superi una quantità specificata.

Avvisi di Azure supporta [avvisi relativi ai log](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) che usano query di ricerca. 

La query seguente restituisce un risultato quando vengono raccolti più di 100 GB di dati nelle ultime 24 ore:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

La query seguente usa una semplice formula per prevedere quando verranno inviati più di 100 GB di dati in un giorno: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Per generare un avviso su un volume di dati diverso, sostituire il numero 100 nelle query con il numero di GB da segnalare.

Per ricevere una notifica quando la raccolta dati supera le dimensioni previste, seguire la procedura descritta in [Creare un nuovo avviso del log](../monitoring-and-diagnostics/alert-metric.md).

Quando si crea l'avviso per la prima query e la quantità di dati supera i 100 GB in 24 ore, impostare:  

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro di Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - Per **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *1440* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data volume greater than 100 GB in 24 hours* (Volume di dati maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si crea l'avviso per la seconda query e si prevedono più di 100 GB di dati in 24 ore, impostare:

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro di Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - Per **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *180* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data volume expected to be greater than 100 GB in 24 hours* (Volume di dati previsto maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](../monitoring-and-diagnostics/monitoring-action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si riceve un avviso, seguire la procedura descritta nella sezione seguente per risolvere i problemi che determinano un utilizzo superiore al previsto.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come usare il linguaggio di ricerca, vedere [Ricerche nei log in Log Analytics](log-analytics-queries.md). È possibile usare le query di ricerca per eseguire ulteriori analisi sui dati di utilizzo.
* Per ricevere una notifica quando vengono soddisfatti determinati criteri di ricerca, seguire la procedura descritta in [Creare un nuovo avviso del log](../monitoring-and-diagnostics/alert-metric.md).
* Usare il [targeting della soluzione](../azure-monitor/insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari
* Per configurare un criterio efficace per la raccolta degli eventi di sicurezza, vedere [Criteri per i filtri del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md).
* Modificare la [configurazione del contatore delle prestazioni](log-analytics-data-sources-performance-counters.md).
* Per modificare le impostazioni di raccolta degli eventi, vedere la [configurazione del registro eventi](log-analytics-data-sources-windows-events.md).
* Per modificare le impostazioni di raccolta di SysLog, vedere la [configurazione di SysLog](log-analytics-data-sources-syslog.md).
