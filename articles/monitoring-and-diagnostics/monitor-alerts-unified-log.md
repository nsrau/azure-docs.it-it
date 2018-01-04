---
title: Log degli avvisi in Monitoraggio di Azure - avvisi (anteprima) | Documenti Microsoft
description: Messaggi di posta elettronica trigger, le notifiche, gli URL di siti Web di chiamata (webhook) o automazione quando vengono soddisfatte le condizioni di query complesse specificate per gli avvisi di Azure (anteprima).
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: 99d222102ab0245c7c4dc8603eaedcfc88ae7a66
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Log degli avvisi in Monitoraggio di Azure - avvisi (anteprima)
In questo articolo vengono fornite informazioni dettagliate, come regole di avviso in un query Analitica negli avvisi di Azure (anteprima) e vengono descritte le differenze tra i diversi tipi di regole di avviso di log.
Attualmente avvisi di Azure (anteprima), supporta solo registra gli avvisi per le query da [Azure Log Analitica](../log-analytics/log-analytics-tutorial-viewdata.md) scritto in [Analitica Log nuovo linguaggio di query](../log-analytics/log-analytics-log-search-upgrade.md)

> [!WARNING]
> Gli avvisi di Azure (anteprima) - gli avvisi del registro, attualmente non supporta le query tra area di lavoro o tra app. 

## <a name="log-alert-rules"></a>Regole di avviso di log

Gli avvisi vengono creati tramite gli avvisi di Azure (anteprima) l'esecuzione automatica query log a intervalli regolari.  Se i risultati della query log corrispondono a determinati criteri, viene creato un record di avviso. La regola può quindi eseguire automaticamente una o più azioni per ricevere una notifica dell'avviso in modo proattivo o richiamare un altro processo simile all'esecuzione di runbook, utilizzando [gruppi di azioni](monitoring-action-groups.md).  I diversi tipi di regole di avviso usano una logica diversa per eseguire l'analisi.

Le regole di avviso vengono definite dai dettagli seguenti:

- **Log Query**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti dalla query vengono utilizzati per determinare se viene creato un avviso.
- **Intervallo di tempo**.  Specifica l'intervallo di tempo per la query.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  Intervallo di tempo può essere qualsiasi valore compreso tra 5 minuti e 1440 minuti o 24 ore. Ad esempio, se l'intervallo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15 , vengono restituiti solo i record creati tra le 12.15 e le 13.15.
- **Frequenza**.  Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore dell'intervallo di tempo.  Se il valore è maggiore dell'intervallo di tempo, si rischia di omettere il record.<br>Si considerino ad esempio un intervallo di tempo di 30 minuti e una frequenza pari a 60 minuti.  Una query eseguita alle 13:00 restituirà i record compresi tra le 12:30 e le 13:00.  La volta successiva, la query verrà eseguita alle 14:00 e restituirà i record compresi tra le 13:30 e le 14:00.  Qualsiasi record creato tra le 13:00 e 13:30 non verrà mai valutato.
- **Soglia**.  Per determinare se è necessario creare un avviso, vengono valutati i risultati della ricerca log.  La soglia è diversa per i diversi tipi di regole di avviso.

Ogni regola di avviso di Log Analytics è di uno fra due tipi.  Ognuno di questi tipi viene descritto in dettaglio nelle sezioni seguenti.

- **[Numero di risultati](#number-of-results-alert-rules)**. Singolo avviso creato quando i record di numeri restituiti dalla ricerca log superano un numero specificato.
- **[Unità di misura della metrica](#metric-measurement-alert-rules)**.  Avviso creato per ogni oggetto nei risultati della ricerca log quando i valori superano la soglia specificata.

Di seguito sono riportate le differenze tra i tipi di regola di avviso.

- **Numero di risultati** regola di avviso crea sempre un po' di tempo di avviso singolo **misura metrico** regola di avviso crea un avviso per ogni oggetto che supera la soglia.
- Le regole di avviso **Numero di risultati** creano un avviso quando la soglia viene superata una sola volta. Le regole di avviso **Unità di misura della metrica** possono creare un avviso quando viene superata la soglia più di una volta in un intervallo di tempo specifico.

## <a name="number-of-results-alert-rules"></a>Regole di avviso Numero di risultati
Le regole di avviso **Numero di risultati** creano un singolo avviso quando il numero di record restituiti dalla query di ricerca supera la soglia specificata.

**Soglia**: la soglia per un **numero di risultati** regola di avviso è maggiore o minore di un particolare valore.  Se il numero di record restituiti dalla ricerca log corrisponde a questi criteri, viene creato un avviso.

### <a name="scenarios"></a>Scenari

#### <a name="events"></a>Eventi
Questo tipo di regola di avviso è adatto per l'uso con eventi come quelli dei log eventi di Windows, Syslog e log personalizzati.  È consigliabile creare un avviso quando viene creato un evento di errore specifico o vengono creati più eventi di errore entro un intervallo di tempo specifico.

Per generare un avviso su un singolo evento, impostare il numero di risultati con maggiore di 0 e la frequenza e intervallo di tempo per cinque minuti.  La query che esegue ogni cinque minuti e il controllo per l'occorrenza di un singolo evento che è stato creato dall'ultima che esecuzione della query.  Una frequenza maggiore può ritardare il tempo tra l'evento raccolto e la creazione dell'avviso.

Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che in un intervallo di tempo specifico non vengano creati più eventi.  

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non viene registrato uno di questi eventi in un intervallo di tempo specifico, dovrà essere creato un avviso.  In questo caso, impostare la soglia **inferiore a 1**.

## <a name="metric-measurement-alert-rules"></a>Regole di avviso Unità di misura della metrica

Le regole di avviso **Unità di misura della metrica** creano un avviso per ogni oggetto in una query con un valore che supera una soglia specificata.  Presentano le differenze seguenti dalle regole di avviso **Numero di risultati**.

**Funzione di aggregazione**: determina il calcolo che viene eseguito e potenzialmente numerico campo da aggregare.  Ad esempio, **Count ()** restituisce il numero di record nella query, **avg(CounterValue)** restituisce la media del campo CounterValue sull'intervallo.

**Campo gruppo**: per ogni istanza di questo campo viene creato un record con un valore aggregato, e può essere generato un avviso per ogni.  Ad esempio, se si desidera generare un avviso per ogni computer, utilizzerebbe **dal Computer**   

**Intervallo**: definisce l'intervallo di tempo in cui i dati vengono aggregati.  Ad esempio, se è stato specificato **cinque minuti**, verrebbe creato un record per ogni istanza del campo gruppo aggregato a intervalli di 5 minuti in base l'intervallo di tempo specificato per l'avviso.

**Soglia**: la soglia per le regole di avviso di metriche di misurazione è definita da un valore di aggregazione e un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

#### <a name="example"></a>Esempio
Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  È necessario creare una regola di avviso con i dettagli seguenti:  

**Query:** prestazioni | dove ObjectName = = "Processore" e CounterName = = "% Processor Time" | riepilogare AggregatedValue = avg(CounterValue) da bin (TimeGenerated, 5m), i Computer<br>
**Intervallo di tempo:** 30 minuti<br>
**Avviso frequenza:** cinque minuti<br>
**Valore di aggregazione:** maggiore di 90<br>
**Avviso di trigger in base a:** totale upera maggiore di 5<br>

La query creerebbe un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti.  Di seguito sono illustrati dati di esempio per tre computer.

![Risultati della query di esempio](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

In questo esempio, verranno creati avvisi separati per srv02 e srv03 poiché hanno superato la soglia del 90% 3 volte nell'intervallo di tempo.  Se il **avviso Trigger in base a:** sono state modificate in **pieghevole** quindi verrebbe creato un avviso solo per srv03 poiché è verificata una violazione di soglia per tre campioni consecutivi.


## <a name="next-steps"></a>Passaggi successivi
* [Ottenere una panoramica degli avvisi di Azure (anteprima)](monitoring-overview-unified-alerts.md) 
* Informazioni su [utilizzando gli avvisi di Azure (anteprima)](monitor-alerts-unified-usage.md)
* Altre informazioni su [Log Analytics](../log-analytics/log-analytics-overview.md).    
