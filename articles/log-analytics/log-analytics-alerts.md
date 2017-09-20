---
title: Informazioni sugli avvisi in Log Analytics di Azure | Microsoft Docs
description: Gli avvisi in Log Analytics identificano le informazioni importanti nel repository OMS e possono notificare i problemi all'utente in modo proattivo o richiamare le azioni per tentare di correggerle.  In questo articolo vengono descritti i diversi tipi di regole di avviso e come vengono definite.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 951e76d3fb18d9e433b148e82d4d6cee9417ce6d
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="understanding-alerts-in-log-analytics"></a>Informazioni sugli avvisi in Log Analytics

Gli avvisi in Log Analytics identificano informazioni importanti nel repository di Log Analytics.  Questo articolo contiene informazioni dettagliate sul funzionamento delle regole di avviso in Log Analytics e la descrizione delle differenze tra diversi tipi di regole di avviso.

Per il processo di creazione delle regole di avviso, vedere gli articoli seguenti:

- Creare regole di avviso tramite il [portale di Azure](log-analytics-alerts-creating.md)
- Creare regole di avviso tramite il [modello di Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Creare regole di avviso tramite l'[API REST](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Regole di avviso

Gli avvisi vengono creati da regole di avviso che eseguono automaticamente ricerche log a intervalli regolari.  Se i risultati della ricerca log corrispondono a criteri specifici viene creato un record di avviso.  La regola può quindi eseguire automaticamente una o più azioni per notificare l'avviso all'utente in modo proattivo o richiamare un altro processo.  I diversi tipi di regole di avviso usano una logica diversa per eseguire l'analisi.

![Avvisi Log Analytics](media/log-analytics-alerts/overview.png)

Le regole di avviso vengono definite dai dettagli seguenti:

- **Ricerca log**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query vengono usati per determinare se viene creato un avviso.
- **Intervallo di tempo**.  Specifica l'intervallo di tempo per la query.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Ad esempio, se l'intervallo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15 , vengono restituiti solo i record creati tra le 12.15 e le 13.15.
- **Frequenza**.  Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore dell'intervallo di tempo.  Se il valore è maggiore dell'intervallo di tempo, si rischia di omettere il record.<br>Si considerino ad esempio un intervallo di tempo di 30 minuti e una frequenza pari a 60 minuti.  Una query eseguita alle 13:00 restituirà i record compresi tra le 12:30 e le 13:00.  La volta successiva, la query verrà eseguita alle 14:00 e restituirà i record compresi tra le 13:30 e le 14:00.  Qualsiasi record creato tra le 13:00 e 13:30 non verrà mai valutato.
- **Soglia**.  Per determinare se è necessario creare un avviso, vengono valutati i risultati della ricerca log.  La soglia è diversa per i diversi tipi di regole di avviso.

Ogni regola di avviso di Log Analytics è di uno fra due tipi.  Ognuno di questi tipi viene descritto in dettaglio nelle sezioni seguenti.

- **[Numero di risultati](#number-of-results-alert-rules)**. Singolo avviso creato quando i record di numeri restituiti dalla ricerca log superano un numero specificato.
- **[Unità di misura della metrica](#metric-measurement-alert-rules)**.  Avviso creato per ogni oggetto nei risultati della ricerca log quando i valori superano la soglia specificata.

Di seguito sono riportate le differenze tra i tipi di regola di avviso.

- La regola di avvivo **Numero di risultati** crea sempre un avviso singolo, mentre le regola di avviso **Unità di misura della metrica** crea un avviso per ogni oggetto che supera la soglia.
- Le regole di avviso **Numero di risultati** creano un avviso quando la soglia viene superata una sola volta. Le regole di avviso **Unità di misura della metrica** possono creare un avviso quando viene superata la soglia più di una volta in un intervallo di tempo specifico.

## <a name="number-of-results-alert-rules"></a>Regole di avviso Numero di risultati
Le regole di avviso **Numero di risultati** creano un singolo avviso quando il numero di record restituiti dalla query di ricerca supera la soglia specificata.

### <a name="threshold"></a>Soglia
La soglia per una regola di avviso **Numero di risultati** è semplicemente maggiore o minore di un particolare valore.  Se il numero di record restituiti dalla ricerca log corrisponde a questi criteri, viene creato un avviso.

### <a name="scenarios"></a>Scenari

#### <a name="events"></a>Eventi
Questo tipo di regola di avviso è adatto per l'uso con eventi come quelli dei log eventi di Windows, Syslog e log personalizzati.  È consigliabile creare un avviso quando viene creato un evento di errore specifico o vengono creati più eventi di errore entro un intervallo di tempo specifico.

Per generare un avviso su un singolo evento, impostare un numero di risultati maggiore di 0 e la frequenza e l'intervallo di tempo su 5 minuti.  Queste impostazioni attivano l'esecuzione della query ogni 5 minuti e controllano l'occorrenza di un singolo evento che è stato creato dopo l'ultima esecuzione della query.  Una frequenza maggiore può ritardare il tempo tra l'evento raccolto e la creazione dell'avviso.

Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che in un intervallo di tempo specifico non vengano creati più eventi.  

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non viene registrato uno di questi eventi in un intervallo di tempo specifico, dovrà essere creato un avviso.  In questo caso è necessario impostare la soglia su **minore di 1**.

#### <a name="performance-alerts"></a>Avvisi di prestazioni
[I dati sulle prestazioni](log-analytics-data-sources-performance-counters.md) vengono archiviato come record nel repository OMS in modo analogo agli eventi.  Se si vuole generare un avviso quando un contatore delle prestazioni supera una determinata soglia, tale soglia deve essere inclusa nella query.

Ad esempio, se si vuole generare un avviso quando il processore è in esecuzione oltre il 90%, usare una query come la seguente con la soglia per la regola di avviso **maggiore di 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Se si vuole generare un avviso quando la media del processore è oltre il 90% per un determinato intervallo di tempo, usare una query con il [comando measure](log-analytics-search-reference.md#commands) come la seguente con la soglia per la regola di avviso **maggiore di 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md), le query precedenti verranno sostituite da quelle seguenti: `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>Regole di avviso Unità di misura della metrica

>[!NOTE]
> Le regole di avviso Unità di misura della metrica sono attualmente disponibili in anteprima pubblica.

Le regole di avviso **Unità di misura della metrica** creano un avviso per ogni oggetto in una query con un valore che supera una soglia specificata.  Presentano le differenze seguenti dalle regole di avviso **Numero di risultati**.

#### <a name="log-search"></a>Ricerca log
Sebbene sia possibile usare qualsiasi query per una regola di avviso **Numero di risultati**, esistono requisiti specifici della query per una regola di avviso per l'unità di misura della metrica.  Deve includere un [comando di misurazione](log-analytics-search-reference.md#commands) per raggruppare i risultati in un campo specifico. Questo comando deve includere gli elementi seguenti.

- **Funzione di aggregazione**.  Determina il calcolo che viene eseguito e potenzialmente un campo numerico da aggregare.  Ad esempio, **count()** restituirà il numero di record nella query, **avg(CounterValue)** restituirà la media del campo CounterValue nell'intervallo.
- **Campo Gruppo**.  Viene creato un record con un valore aggregato per ogni istanza di questo campo e può essere generato un avviso per ognuno di essi.  Ad esempio, se si desidera generare un avviso per ogni computer, si userà **dal Computer**.   
- **Intervallo**.  Definisce l'intervallo di tempo in cui i dati vengono aggregati.  Ad esempio, se è stato specificato **5minutes**, viene creato un record per ogni istanza del campo Gruppo aggregato a intervalli di 5 minuti nella finestra temporale specificata per l'avviso.

#### <a name="threshold"></a>Soglia
La soglia per le regole di avviso Unità di misurazione della metrica è definita da un valore di aggregazione e da un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

#### <a name="example"></a>Esempio
Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  Si deve creare una regola di avviso con i dettagli seguenti.  

**Query:** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Intervallo di tempo:** 30 minuti<br>
**Frequenza di avviso:** 5 minuti<br>
**Valore di aggregazione:** maggiore di 90<br>
**Attiva l'avviso in base a:** violazioni totali maggiori di 5<br>

La query crea un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti.  Di seguito sono illustrati dati di esempio per tre computer.

![Risultati della query di esempio](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

In questo esempio, verranno creati avvisi separati per srv02 e srv03 poiché hanno superato la soglia del 90% 3 volte nell'intervallo di tempo.  Se **Attiva l'avviso in base:** venisse modificato in **Consecutivo** verrebbe creato un avviso solo per srv03 poiché ha violato la soglia di 3 volte consecutive.

## <a name="alert-records"></a>Record di avvisi
I record degli avvisi creati dalle regole di avviso in Log Analytics hanno un **Tipo** impostato su **Avviso** e **SourceSystem** impostato su **OMS**.  Includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*Avviso* |
| SourceSystem |*OMS* |
| *Object*  | [Gli avvisi di misurazione della metrica](#metric-measurement-alert-rules) avranno una proprietà per il campo di Gruppo.  Ad esempio, se la ricerca log si raggruppa sul computer, il record dell'avviso contiene un campo Computer con il nome del computer come valore.
| AlertName |Nome dell'avviso. |
| AlertSeverity |Livello di gravità dell'avviso. |
| LinkToSearchResults |Collegamento alla ricerca nei log di Log Analytics che restituisce i record della query che ha creato l'avviso. |
| Query |Testo della query eseguita. |
| QueryExecutionEndTime |Fine dell'intervallo di tempo per la query. |
| QueryExecutionStartTime |Inizio dell'intervallo di tempo per la query. |
| ThresholdOperator | Operatore usato dalla regola di avviso. |
| ThresholdValue | Valore usato dalla regola di avviso. |
| TimeGenerated |Data e ora in cui è stato creato l'avviso. |

Sono disponibili altri tipi di record di avviso creati dalla [soluzione Gestione avvisi](log-analytics-solution-alert-management.md) e dalle [esportazioni di Power BI](log-analytics-powerbi.md).  Questi record hanno un **Tipo** impostato su **Avviso** ma vengono distinti da relativo **SourceSystem**.


## <a name="next-steps"></a>Passaggi successivi
* Installare la [soluzione Gestione avvisi](log-analytics-solution-alert-management.md) per analizzare gli avvisi creati in Log Analytics insieme agli avvisi raccolti da System Center Operations Manager.
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) che possono generare avvisi.
* Completare una procedura dettagliata per la [configurazione di un webhook](log-analytics-alerts-webhooks.md) con una regola di avviso.  
* Informazioni su come scrivere [runbook in Automazione di Azure](https://azure.microsoft.com/documentation/services/automation) per la risoluzione dei problemi identificati dagli avvisi.

