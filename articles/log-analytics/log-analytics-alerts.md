---
title: Informazioni sugli avvisi in Log Analytics di Azure | Microsoft Docs
description: Gli avvisi in Log Analytics identificano le informazioni importanti nel repository OMS e possono notificare i problemi all'utente in modo proattivo o richiamare le azioni per tentare di correggerle.  In questo articolo vengono descritti i diversi tipi di regole di avviso e come vengono definite.
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/05/2018
ms.author: bwren
ms.openlocfilehash: 07e8312d5e113eeb9016dcc832b1cf66f8001c5f
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="understanding-alerts-in-log-analytics"></a>Informazioni sugli avvisi in Log Analytics

Gli avvisi in Log Analytics identificano informazioni importanti nel repository di Log Analytics.  Questo articolo illustra alcune decisioni di progettazione da prendere in base alla frequenza di raccolta dei dati sottoposti a query, ai ritardi casuali di inserimento dati dovuti alla latenza di rete o alla capacità di elaborazione e al commit dei dati nel repository di Log Analytics.  Vengono fornite anche informazioni dettagliate sul funzionamento delle regole di avviso in Log Analytics e vengono descritte le differenze tra i diversi tipi di regole di avviso.

Per il processo di creazione delle regole di avviso, vedere gli articoli seguenti:

- Creare regole di avviso tramite il [portale di Azure](log-analytics-alerts-creating.md)
- Creare regole di avviso tramite il [modello di Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Creare regole di avviso tramite l'[API REST](log-analytics-api-alerts.md)

## <a name="considerations"></a>Considerazioni

I dettagli sulla frequenza di raccolta dati per diversi tipi di dati e soluzioni sono disponibili nella sezione [Informazioni dettagliate sulla raccolta di dati](log-analytics-add-solutions.md#data-collection-details) dell'articolo di panoramica delle soluzioni. Come indicato in questo articolo, la frequenza di raccolta può essere anche solo di una volta ogni sette giorni oppure *su notifica*. È importante comprendere e valutare la frequenza di raccolta dati prima di configurare un avviso. 

- La frequenza di raccolta determina la frequenza con cui l'agente di OMS nei computer invia dati a Log Analytics. Se, ad esempio, la frequenza di raccolta è di 10 minuti e non ci sono altri ritardi nel sistema, i timestamp dei dati trasmessi possono risalire da zero a 10 minuti prima dell'aggiunta al repository e di quando diventano disponibili per la ricerca in Log Analytics.

- Prima che un avviso possa essere attivato, i dati devono venire scritti nel repository, in modo da essere disponibili per le query. A causa della latenza descritta in precedenza, la frequenza di raccolta non corrisponde al momento in cui i dati sono disponibili per le query. I dati, ad esempio, possono essere raccolti precisamente ogni 10 minuti, ma saranno disponibili nel repository a intervalli irregolari. Ipoteticamente, i dati raccolti a intervalli di zero, 10 e 20 minuti potrebbero essere disponibili per la ricerca, rispettivamente a 25, 28 e 35 minuti o in base ad altri intervalli irregolari dipendenti dalla latenza di inserimento. Il caso peggiore per questi ritardi è documentato nel [contratto di servizio per Log Analytics](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1), che non include il ritardo introdotto dalla frequenza di raccolta o dalla latenza di rete tra il computer e il servizio Log Analytics.


## <a name="alert-rules"></a>Regole di avviso

Gli avvisi vengono creati da regole di avviso che eseguono automaticamente ricerche log a intervalli regolari.  Se i risultati della ricerca log corrispondono a criteri specifici viene creato un record di avviso.  La regola può quindi eseguire automaticamente una o più azioni per notificare l'avviso all'utente in modo proattivo o richiamare un altro processo.  I diversi tipi di regole di avviso usano una logica diversa per eseguire l'analisi.

![Avvisi Log Analytics](media/log-analytics-alerts/overview.png)

Poiché è prevista una latenza per l'inserimento dei dati di log, il tempo assoluto tra l'indicizzazione dei dati e il momento in cui risultano disponibili per la ricerca può essere imprevedibile.  La disponibilità quasi in tempo reale dei dati raccolti deve essere presa in considerazione quando si definiscono le regole di avviso.    

C'è un compromesso tra l'affidabilità degli avvisi e la loro velocità di risposta. È possibile scegliere di configurare i parametri di avviso per ridurre al minimo i falsi allarmi e gli avvisi persi oppure è possibile scegliere parametri di avviso per rispondere rapidamente alle condizioni monitorate, ma che in alcuni casi possono generare falsi avvisi o comportare la perdita di avvisi.

Le regole di avviso vengono definite dai dettagli seguenti:

- **Ricerca log**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query vengono usati per determinare se viene creato un avviso.
- **Intervallo di tempo**.  Specifica l'intervallo di tempo per la query.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  Può essere un valore qualsiasi compreso tra cinque minuti e 24 ore. L'intervallo deve essere sufficientemente ampio per tenere in considerazione ritardi ragionevoli nell'inserimento. L'intervallo di tempo deve essere il doppio della lunghezza del ritardo più lungo che si vuole poter gestire.<br> Se, ad esempio, si vuole che gli avvisi rimangano affidabili con ritardi di 30 minuti, l'intervallo deve essere di un'ora.  

    Se l'intervallo di tempo è troppo piccolo, possono verificarsi due situazioni.

    - **Avvisi persi**. Si supponga che il ritardo di inserimento talvolta sia di 60 minuti, ma il più delle volte di 15 minuti.  Se l'intervallo di tempo è impostato su 30 minuti, quando il ritardo è di 60 minuti un avviso andrà perso, perché i dati non saranno disponibili per la ricerca quando la query di avviso viene eseguita. 
   
        >[!NOTE]
        >Diagnosticare il motivo della perdita dell'avviso è impossibile. Nel caso precedente, ad esempio, i dati vengono scritti nel repository 60 minuti dopo che la query di avviso è stata eseguita. Se il giorno successivo si nota che un avviso è andato perso e la query viene eseguita il giorno successivo nell'intervallo di tempo corretto, il risultato corrisponde ai criteri di ricerca log. Risulterebbe quindi che l'avviso avrebbe dovuto essere stato attivato. In realtà, l'avviso non è stato attivato perché i dati non erano ancora disponibili quando è stata eseguita la query di avviso. 
        >
 
    - **Falsi avvisi**. A volta le query di avviso sono progettate per identificare l'assenza di eventi. Ad esempio, per rilevare quando una macchina virtuale è offline cercando heartbeat mancanti. Come nel caso precedente, se l'heartbeat non è disponibile per la ricerca nell'intervallo di tempo di avviso, viene generato un avviso in quanto i dati dell'heartbeat non sono ancora disponibili per la ricerca e quindi sono assenti. Il risultato sarebbe lo stesso quando la macchina virtuale è legittimamente offline e non genera dati di heartbeat. Eseguendo la query il giorno successivo nell'intervallo di tempo corretto, risulterà che gli heartbeat erano presenti e si è verificato un errore dell'avviso. Gli heartbeat, di fatto, non erano ancora disponibili per la ricerca perché l'intervallo di tempo di avviso è stato impostato su un valore troppo piccolo.  

- **Frequenza**.  Specifica la frequenza con cui la query deve essere eseguita e consente di creare avvisi con velocità di risposta maggiore. Il valore deve essere compreso tra cinque minuti e 24 ore e deve essere uguale o minore all'intervallo di tempo di avviso.  Se il valore è maggiore dell'intervallo di tempo, si rischia di omettere il record.<br>Se l'obiettivo è l'affidabilità con ritardi fino a 30 minuti e il ritardo normale è di 10 minuti, l'intervallo di tempo deve essere di un'ora e il valore della frequenza deve essere di 10 minuti. In questo modo verrà attivato un avviso con i dati con un ritardo di inserimento di 10 minuti tra 10 e 20 minuti da quando i dati dell'avviso sono stati generati.<br>Per evitare di creare più avvisi per gli stessi dati a causa di un intervallo di tempo troppo grande, è possibile usare l'opzione [Elimina avvisi](log-analytics-tutorial-response.md#create-alerts) per eliminare gli avvisi per un tempo corrispondente almeno all'intervallo di tempo.
  
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
- **Intervallo**.  Definisce l'intervallo di tempo in cui i dati vengono aggregati.  Ad esempio, se sono stati specificati **5 minuti**, viene creato un record per ogni istanza del campo Gruppo aggregato a intervalli di 5 minuti nell'intervallo di tempo specificato per l'avviso.

#### <a name="threshold"></a>Soglia
La soglia per le regole di avviso Unità di misurazione della metrica è definita da un valore di aggregazione e da un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

#### <a name="example"></a>Esempio
Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  Si deve creare una regola di avviso con i dettagli seguenti.  

**Query:** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Intervallo di tempo:** 30 minuti<br>
**Frequenza di avviso:** 5 minuti<br>
**Valore di aggregazione:** maggiore di 90<br>
**Attiva l'avviso in base a:** totale violazioni maggiore di 5<br>

La query crea un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti.  Di seguito sono illustrati dati di esempio per tre computer.

![Risultati della query di esempio](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

In questo esempio, verranno creati avvisi separati per srv02 e srv03 poiché hanno superato la soglia del 90% 3 volte nell'intervallo di tempo.  Se **Attiva l'avviso in base:** venisse modificato in **Consecutivo** verrebbe creato un avviso solo per srv03 poiché ha violato la soglia di 3 volte consecutive.

## <a name="alert-records"></a>Record di avvisi
I record degli avvisi creati dalle regole di avviso in Log Analytics hanno un **Tipo** impostato su **Avviso** e **SourceSystem** impostato su **OMS**.  Includono le proprietà elencate nella tabella seguente.

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| type |*Avviso* |
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
