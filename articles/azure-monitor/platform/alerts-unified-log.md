---
title: Avvisi del log in Monitoraggio di Azure
description: Attivare messaggi di posta elettronica e notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni di query di analisi specificate per Avvisi di Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427424"
---
# <a name="log-alerts-in-azure-monitor"></a>Avvisi del log in Monitoraggio di Azure

Questo articolo contiene informazioni dettagliate sugli avvisi di log, uno dei tipi di avvisi supportati nel nuovo servizio [Avvisi di Azure](../../azure-monitor/platform/alerts-overview.md) che consente agli utenti di usare la piattaforma di analisi di Azure come base per la generazione di avvisi.

Un avviso di log è costituito da regole di ricerca log create per i [log di monitoraggio di Azure](../../azure-monitor/learn/tutorial-viewdata.md) o [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Per altre informazioni sull'utilizzo, vedere [Creating log alerts in Azure](../../azure-monitor/platform/alerts-log.md) (Creazione di avvisi dei log in Azure).

> [!NOTE]
> I dati di log più comuni dei [log di monitoraggio di Azure](../../azure-monitor/learn/tutorial-viewdata.md) sono ora disponibili anche nella piattaforma di metriche in Monitoraggio di Azure. Per i dettagli, vedere [Metric Alert for Logs](../../azure-monitor/platform/alerts-metric-logs.md) (Avvisi di metrica per i log).


## <a name="log-search-alert-rule---definition-and-types"></a>Regole di avviso di ricerca log - Definizioni e tipi

Le regole di ricerca log vengono create da Avvisi di Azure in modo da eseguire automaticamente le query di log specificate a intervalli regolari.  Se i risultati della query del log corrispondono a criteri specifici viene creato un record di avviso. La regola può quindi eseguire automaticamente una o più azioni tramite [gruppi di azioni](../../azure-monitor/platform/action-groups.md). Potrebbe essere necessario il ruolo di [Collaboratore di Monitoraggio di Azure](../../azure-monitor/platform/roles-permissions-security.md) per creare, modificare e aggiornare gli avvisi dei log, oltre ai diritti di accesso ed esecuzione di query per le destinazioni dell'analisi in una regola di avviso o una query di avviso. Nel caso in cui la creazione di utente non ha accesso a tutte le destinazioni di analitica nella regola di avviso o query di avviso: la creazione della regola potrebbe non riuscire o la regola di avviso di log verrà eseguita con i risultati parziali.

Le regole di ricerca log sono definite dai dettagli seguenti:

- **Query di log**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query vengono usati per determinare se deve essere attivato un avviso. La query di analisi può essere applicata a un'area di lavoro di Log Analytics o un'app di Application Insights specifica e addirittura a [più risorse di Log Analytics e Application Insights](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights), purché l'utente abbia i diritti di accesso e di query su tutte le risorse. 
    > [!IMPORTANT]
    > [query di cross-resource](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) degli avvisi del log per gli avvisi di Application Insights e log di supporto [Analitica di Log configurati utilizzando scheduledQueryRules API](../../azure-monitor/platform/alerts-log-api-switch.md) solo.

    Alcuni comandi e combinazioni di analisi non sono compatibili con l'uso negli avvisi dei log. Per informazioni più dettagliate, vedere [Query per avvisi dei log in Monitoraggio di Azure](../../azure-monitor/platform/alerts-log-query.md).

- **Periodo di tempo**.  Specifica l'intervallo di tempo per la query. La query restituisce solo i record creati in questo intervallo dell'ora corrente. Il periodo di tempo limita i dati recuperati per la query di log in modo da impedirne l'uso improprio e ignora qualsiasi comando di tempo (come ago) usato nella query di log. <br>*Ad esempio, se il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15, vengono restituiti solo i record creati tra le 12.15 e le 13.15 per l'esecuzione della query di log. Se la query di log usa un comando di tempo come ago (7d), la query viene eseguita solo per i dati creati tra le 12.15 e le 13.15, ovvero solo per i dati che esistono dagli ultimi 60 minuti e non per i sette giorni di dati specificati nella query di log.*

- **Frequenza**.  Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore del periodo di tempo.  Se il valore è maggiore del periodo di tempo, il record rischia di essere omesso.<br>*Ad esempio, considerando un periodo di tempo di 30 minuti e una frequenza di 60 minuti,  Una query eseguita alle 13:00 restituirà i record compresi tra le 12:30 e le 13:00.  La volta successiva, la query verrà eseguita alle 14:00 e restituirà i record compresi tra le 13:30 e le 14:00.  Qualsiasi record creato tra le 13.00 e le 13.30 non verrà mai valutato.*

- **Soglia**.  Per determinare se è necessario creare un avviso, vengono valutati i risultati della ricerca log.  La soglia è diversa per tipi diversi di regole di avviso di ricerca log.

Le regole di ricerca log, sia per i [log di monitoraggio di Azure](../../azure-monitor/learn/tutorial-viewdata.md) sia per [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), possono essere di due tipi diversi. Ognuno di questi tipi viene descritto in dettaglio nelle sezioni seguenti.

- **[Numero di risultati](#number-of-results-alert-rules)** . Singolo avviso creato quando i record di numeri restituiti dalla ricerca log superano un numero specificato.
- **[Unità di misura della metrica](#metric-measurement-alert-rules)** .  Avviso creato per ogni oggetto nei risultati della ricerca log quando i valori superano la soglia specificata.

Di seguito sono riportate le differenze tra i tipi di regola di avviso.

- Le regole di avviso *Numero di risultati* creano sempre un singolo avviso, mentre una regola di avviso *Unità di misura della metrica* crea un avviso per ogni oggetto che supera la soglia.
- Le regole di avviso *Numero di risultati* creano un avviso quando la soglia viene superata una sola volta. Le regole di avviso *Unità di misura della metrica* possono creare un avviso quando viene superata la soglia più di una volta in un intervallo di tempo specifico.

### <a name="number-of-results-alert-rules"></a>Regole di avviso Numero di risultati

Le regole di avviso **Numero di risultati** creano un singolo avviso quando il numero di record restituiti dalla query di ricerca supera la soglia specificata. Questo tipo di regola di avviso è adatto per l'uso con eventi come quelli dei log eventi di Windows, Syslog, WebApp Response e log personalizzati.  Si potrebbe voler creare un avviso quando viene creato un determinato evento di errore o quando vengono creati più eventi di errore entro un periodo di tempo specifico.

**Soglia**: La soglia per delle regole di avviso di un Numero di risultati è maggiore o minore di un particolare valore.  Se il numero di record restituiti dalla ricerca log corrisponde a questi criteri, viene creato un avviso.

Per generare un avviso per un evento singolo, impostare il numero di risultati su un valore maggiore di 0 e controllare l'occorrenza di un singolo evento creato dopo l'ultima esecuzione della query. Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che non vengano creati più eventi entro un periodo di tempo specifico.  

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non registra uno di questi eventi entro un periodo di tempo specifico, dovrà essere creato un avviso.  In questo caso è necessario impostare la soglia su **minore di 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Esempio di avviso di log di tipo Numero di record

Si consideri uno scenario in cui si vuole sapere quando l'app basata sul Web fornisce una risposta agli utenti con codice 500, Errore interno del server. Si deve creare una regola di avviso con i dettagli seguenti:  

- **Query:** requests | where resultCode == "500"<br>
- **Periodo di tempo:** 30 minuti<br>
- **Frequenza avviso:** cinque minuti<br>
- **Valore soglia:** Maggiore di 0<br>

L'avviso esegue la query ogni 5 minuti, con 30 minuti di dati, per cercare tutti i record in cui il codice di risultato è 500. Se viene trovato anche un solo record di questo tipo, viene generato l'avviso e viene attivata l'azione configurata.

### <a name="metric-measurement-alert-rules"></a>Regole di avviso Unità di misura della metrica

**Misurazione delle metriche** le regole di avviso creano un avviso per ogni oggetto in una query con un valore che supera una soglia specificata e trigger condizione specificata. A differenza **numero di risultati** regole di avviso **misura della metrica** le regole di avviso di lavoro quando il risultato di analitica fornisce una serie temporale. Presentano le differenze seguenti dalle regole di avviso **Numero di risultati**.

- **Funzione di aggregazione**: Determina il calcolo che viene eseguito e potenzialmente un campo numerico da aggregare.  Ad esempio, **count()** restituisce il numero di record nella query, **avg(CounterValue)** restituisce la media del campo CounterValue nell'intervallo. La funzione di aggregazione nella query deve essere denominata: AggregatedValue e fornire un valore numerico. 

- **Campo Gruppo**: Viene creato un record con un valore aggregato per ogni istanza di questo campo e può essere generato un avviso per ognuno di essi.  Ad esempio, se si desidera generare un avviso per ogni computer, si userà **dal Computer**. Se nella query di avviso sono specificati più campi gruppo, l'utente può specificare il campo da usare per ordinare i risultati tramite il parametro **Aggregate On** (metricColumn).

    > [!NOTE]
    > L'opzione *Aggregate On* (metricColumn) è disponibile solo per gli avvisi relativi ai log di tipo Unità di misura della metrica per Application Insights e per gli avvisi relativi ai log per [Log Analytics configurati usando l'API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md).

- **Intervallo**:  Definisce l'intervallo di tempo in cui i dati vengono aggregati.  Ad esempio, specificando **cinque minuti**, viene creato un record per ogni istanza del campo Gruppo, aggregato a intervalli di 5 minuti nel periodo di tempo specificato per l'avviso.

    > [!NOTE]
    > Per specificare l'intervallo, è necessario usare la funzione Bin nella query. Poiché la funzione bin() può generare risultati con intervalli di tempo diversi, il servizio convertirà automaticamente il comando bin in comando bin_at con l'ora appropriata al runtime per garantire risultati con punto fisso. Il tipo di avviso di log Unità di misura della metrica è progettato per funzionare con le query che contengono fino a tre istanze del comando bin().
    
- **Soglia**: La soglia per le regole di avviso Unità di misurazione della metrica è definita da un valore di aggregazione e da un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

L'errata configurazione dell'opzione *Aggregate On* o *metricColumn* può causare errori di attivazione delle regole di avviso. Per altre informazioni, vedere [Risoluzione dei problemi quando la regola di avviso Unità di misura della metrica non è corretta](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Esempio di avviso di log di tipo Unità di misurazione

Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  Si deve creare una regola di avviso con i dettagli seguenti:  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **Periodo di tempo:** 30 minuti<br>
- **Frequenza avviso:** cinque minuti<br>
- **Logica avvisi - Condizione e soglia:** Maggiore di 90<br>
- **Campo Gruppo (funzione Aggregate On):** Computer
- **Attiva l'avviso in base a:** Violazioni della protezione totali maggiori di 2<br>

La query crea un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti. Dato che il campo Gruppo (funzione Aggregate On) scelto fa parte della colonna "Computer" - AggregatedValue viene suddiviso in valori diversi di "Computer" e si determina l'utilizzo medio del processore per ogni computer a intervalli di 5 minuti.  Ad esempio, il risultato della query di esempio per tre computer, sarebbe il seguente.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Se il risultato della query dovesse essere tracciato, verrebbe visualizzato come segue.

![Risultati della query di esempio](media/alerts-unified-log/metrics-measurement-sample-graph.png)

In questo esempio vediamo l'utilizzo medio del processore calcolato per un arco di tempo di 5 minuti, suddiviso a intervalli di 5 minuti per ognuno dei tre computer. La soglia di 90 viene violata una sola volta da srv01 nell'intervallo 1:25. In confronto, srv02 supera la soglia di 90 negli intervalli 1:10, 1:15 e 1:25, mentre srv03 supera la soglia di 90 negli intervalli 1:10, 1:15, 1:20 e 1:30.
Dato che l'avviso è configurato per attivarsi nel caso in cui il totale delle violazioni sia superiore a due, vediamo che solo srv02 e srv03 soddisfano questi criteri. Di conseguenza vengono creati avvisi separati per srv02 e srv03, in quanto hanno superato la soglia del 90% per due volte in svariati intervalli di tempo.  Se invece l'utente avesse configurato il parametro *Attiva l'avviso in base a* per l'opzione *Violazioni continue*, si attiverebbe un avviso **solo** per srv03, poiché ha violato la soglia per tre intervalli di tempo consecutivi compresi tra 1:10 e 1:20. **Non** verrebbe attivato per srv02, dal momento che ha violato la soglia di due intervalli tempo consecutivi compresi tra 1:10 e 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Regola di avviso di ricerca log - attivazione e stato

La regola di avviso di ricerca log funziona sulla logica dichiarata dall'utente in base alla configurazione e alla query di analisi personalizzata usata. Poiché la logica di monitoraggio, tra cui la condizione esatta o motivo per cui deve essere attivata la regola di avviso è incapsulato in una query analitica - che può essere diverso in ogni regola di avviso di log. Gli avvisi di Azure dispone di informazioni insufficienti della causa radice sottostante specifica (o) lo scenario da valutare quando viene raggiunta o superata la condizione di soglia della regola di avviso ricerca log. In questo modo gli avvisi del log vengono definiti come senza stato. E le regole di avviso di log verranno continuano a generare, fino a quando viene soddisfatta la condizione di avviso in base al risultato della query analitica personalizzato fornito. Senza l'avviso ogni recupero risolto, come la logica della causa esatta dell'errore di monitoraggio viene nascosto all'interno della query analitica fornita dall'utente. Non vi siano attualmente alcun meccanismo per avvisi di monitoraggio di Azure per dedurre conseguenze lampanti causa principale da risolvere.

Consente di visualizzare la stessa operazione con un esempio pratico. Si supponga che una regola di avviso di log denominata *Contoso-Log-Alert*, come indicato di configurazione nel [riportato di seguito viene fornito per l'avviso di log di tipo numero di risultati](#example-of-number-of-records-type-log-alert) , in cui la query di avviso personalizzata è progettata per trovare 500 codice di risultato nei log.

- In 1:05 PM quando Contoso-Log-avviso è stato eseguito da avvisi di Azure, i risultati della ricerca log ha restituito zero record con il codice di risultato con 500. Poiché è di sotto della soglia zero e non viene generato l'avviso.
- Alla successiva iterazione: 10 Vediamoci quando Contoso-Log-avviso è stato eseguito da avvisi di Azure, risultati della ricerca log fornito cinque record con il codice di risultato come 500. Generato dopo cinque supera la soglia e viene generato l'avviso con azioni associate.
- Risultati della ricerca log fornito al 1 15 PM quando Contoso-Log-avviso è stato eseguito da avvisi di Azure, i due record con codice 500 risultati. Generato poiché due supera la soglia e viene generato l'avviso con azioni associate.
- A questo punto l'iterazione successiva: 20 Vediamoci cui Contoso-Log-avviso è stato eseguito dall'avviso di Azure, risultati della ricerca log fornito al nuovo zero record con codice 500 risultati. Poiché è di sotto della soglia zero e non viene generato l'avviso.

Ma nel caso sopra elencato: 15 Vediamoci - avvisi di Azure non è possibile determinare che i problemi sottostanti occorrenza 1:10 vengono mantenute e se è presente netti nuovi errori. Come query forniti dall'utente possono essere prendendo in considerazione i record precedenti - è possibile che gli avvisi di Azure. Poiché la logica per l'avviso è incapsulata in query di avviso, pertanto i due record con il codice di risultato 500 visualizzate Vediamoci: 15 potrebbe o non potrebbe essere già visto Vediamoci: 10. Di conseguenza per cautelarsi, quando Contoso--avviso del Log viene eseguito 15 Vediamoci, azione configurata viene generato nuovamente. A questo punto: 20 Vediamoci quando zero record sono visibili con il codice risultato 500 - avvisi di Azure possono essere certi che la causa del codice di risultato 500 occorrenza 1 10 PM e 1 15 PM è risolto e gli avvisi di monitoraggio di Azure possono dedurre in tutta sicurezza i problemi di 500-Errore non verranno eseguito per lo stesso motivo s nuovamente. Contoso-Log-avviso verrà di conseguenza non impostato su Resolved nel dashboard di avviso di Azure e/o notifiche inviate che indica che la risoluzione dell'avviso. Ma l'utente che conosce la condizione esatto o il motivo per la logica incorporata nella query analitica, si può [contrassegnare l'avviso come chiuso](alerts-managing-alert-states.md) in base alle esigenze.

## <a name="pricing-and-billing-of-log-alerts"></a>Prezzi e fatturazione degli avvisi dei log

I prezzi applicabili agli avvisi dei log sono disponibili nella pagina [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Nelle fatture di Azure gli avvisi dei log sono rappresentati come tipo `microsoft.insights/scheduledqueryrules` con:

- Avvisi dei log in Application Insights visualizzati con il nome esatto insieme al gruppo di risorse e alle proprietà dell'avviso
- Avvisi dei log in Log Analytics visualizzati con il nome esatto insieme al gruppo di risorse e alle proprietà dell'avviso, se creati con l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

L'[API legacy Log Analytics](../../azure-monitor/platform/api-alerts.md) ha azioni di avviso e pianificazioni che fanno parte della ricerca salvata di Log Analytics e non [risorse di Azure](../../azure-resource-manager/resource-group-overview.md) vere e proprie. Pertanto per abilitare la fatturazione per tali avvisi di log legacy creati per Log Analytics usando il portale di Azure **senza** [passare alla nuova API](../../azure-monitor/platform/alerts-log-api-switch.md) o usando l'[API legacy Log Analytics](../../azure-monitor/platform/api-alerts.md), vengono create pseudo-regole di avviso nascoste in `microsoft.insights/scheduledqueryrules` per la fatturazione in Azure. Le pseudo-regole di avviso nascoste create per la fatturazione in `microsoft.insights/scheduledqueryrules` vengono visualizzate come `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` insieme al gruppo di risorse e alle proprietà di avviso.

> [!NOTE]
> Se sono presenti caratteri non validi come `<, >, %, &, \, ?, /`, vengono sostituiti con `_` nella pseudo-regola di avviso nascosta e pertanto anche nella fattura di Azure.

Per rimuovere le risorse scheduleQueryRules nascoste create per le regole di avviso mediante l'[API legacy Log Analytics](api-alerts.md), l'utente può eseguire una delle operazioni seguenti:

- Può [cambiare la preferenza dell'API per le regole di avviso nell'area di lavoro di Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) e, senza perdere le regole di avviso o il monitoraggio, passare all'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) conforme ad Azure Resource Manager. In questo modo non è più necessario creare pseudo-regole di avviso nascoste per la fatturazione.
- Se l'utente non vuole cambiare la preferenza dell'API, dovrà **eliminare** la pianificazione e l'azione di avviso originali usando l'[API legacy Log Analytics](api-alerts.md) o eliminare nel [portale di Azure la regola di avviso dei log originale](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal).

Anche per le risorse scheduleQueryRules nascosti create per la fatturazione delle regole di avviso usando [legacy Log Analitica API](api-alerts.md), qualsiasi operazione di modifica, ad esempio PUT avrà esito negativo. Come le `microsoft.insights/scheduledqueryrules` tipo pseudo regole sono a scopo di fatturazione le regole di avviso create utilizzando [legacy Log Analitica API](api-alerts.md). Qualsiasi modifica regola di avviso deve essere eseguita usando [API Analitica Log legacy](api-alerts.md) (o) che potrà essere [passare delle preferenze di API per le regole di avviso](../../azure-monitor/platform/alerts-log-api-switch.md) usare [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) In alternativa.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [creazione di avvisi dei log in Azure](../../azure-monitor/platform/alerts-log.md).
* Acquisire familiarità con i [webhook negli avvisi dei log in Azure](alerts-log-webhook.md).
* Informazioni su [Avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
* Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md).
* Altre informazioni su [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).