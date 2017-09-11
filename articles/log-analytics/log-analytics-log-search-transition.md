---
title: Informazioni di riepilogo per il linguaggio di query di Azure Log Analytics | Microsoft Docs
description: "Questo articolo offre informazioni utili per la transizione al nuovo linguaggio di query per Log Analytics se si ha già familiarità con il linguaggio legacy."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 10b7f3ad23d9c5451bc7ff82b8927c260230f6da
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---

# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Transizione al nuovo linguaggio di query di Azure Log Analytics

> [!NOTE]
> In [Aggiornare l'area di lavoro di Azure Log Analytics alla nuova ricerca log](log-analytics-log-search-upgrade.md) sono disponibili altre informazioni sul nuovo linguaggio di query di Log Analytics e istruzioni per l'aggiornamento dell'area di lavoro.

Questo articolo offre informazioni utili per la transizione al nuovo linguaggio di query per Log Analytics se si ha già familiarità con il linguaggio legacy.

## <a name="language-converter"></a>Convertitore di linguaggio

Se si ha familiarità con il linguaggio di query di Log Analytics legacy, il modo più semplice per creare la stessa query nel nuovo linguaggio consiste nell'usare il convertitore di linguaggio che viene installato nel portale per la ricerca log al momento della conversione dell'area di lavoro.  L'uso del convertitore è molto semplice. È sufficiente digitare una query legacy nella casella di testo superiore e quindi fare clic su **Converti**.  È possibile fare clic sul pulsante di ricerca per eseguire la query o copiare e incollare la query per usarla altrove.

![Convertitore di linguaggio](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="cheat-sheet"></a>Tabella di riepilogo

La tabella seguente mette a confronto i comandi equivalenti di svariate query comuni per il linguaggio di query legacy e nuovo di Azure Log Analytics.

| Descrizione | Legacy | Nuovo |
|:--|:--|:--|
| Ricerca in tutte le tabelle      | error | ricerca di "error" (senza distinzione tra maiuscole/minuscole) |
| Selezione di dati da una tabella | Type=Event |  Event |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| Confronto di stringhe      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (senza distinzione tra maiuscole/minuscole)<br>Event &#124; where Computer contains_cs "Contoso" (con distinzione tra maiuscole/minuscole) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| Confronto di date        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &#124; where TimeGenerated between (datetime(2017-05-01) .. datetime(2017-05-31)) |
| Confronto booleano     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat | where IsGatewayInstalled == false |
| Ordinamento                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Distinzione               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| Estensione di colonne         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| Aggregazione            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| Aggregazione con limite | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| Unione                  | Type=Event or Type=Syslog | union Event, Syslog |
| Join                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Passaggi successivi
- Vedere un'[esercitazione sulla scrittura di query](https://go.microsoft.com/fwlink/?linkid=856078) con il nuovo linguaggio di query.
- Vedere le [informazioni di riferimento sul linguaggio di query](https://go.microsoft.com/fwlink/?linkid=856079) per informazioni dettagliate su tutti i comandi, gli operatori e le funzioni per il nuovo linguaggio di query.  

