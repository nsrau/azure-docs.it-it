---
title: Avvisi del log in Monitoraggio di Azure
description: Attivare messaggi di posta elettronica e notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni di query di analisi specificate per Avvisi di Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f6f94f12fc0a639743f310638af0f0b6ac0d2932
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958152"
---
# <a name="log-alerts-in-azure-monitor"></a>Avvisi del log in Monitoraggio di Azure
Questo articolo contiene informazioni dettagliate sugli avvisi di log, uno dei tipi di avvisi supportati nel nuovo servizio [Avvisi di Azure](monitoring-overview-unified-alerts.md) che consente agli utenti di usare la piattaforma di analisi di Azure come base per la generazione di avvisi.

Un avviso di log è costituito da regole di ricerca log create per [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) o [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events). Per altre informazioni sull'utilizzo, vedere [Creating log alerts in Azure](alert-log.md) (Creazione di avvisi dei log in Azure).

> [!NOTE]
> I dati di log più comuni di [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) sono ora disponibili anche nella piattaforma di metriche in Monitoraggio di Azure. Per i dettagli, vedere [Metric Alert for Logs](monitoring-metric-alerts-logs.md) (Avvisi di metrica per i log).


## <a name="log-search-alert-rule---definition-and-types"></a>Regole di avviso di ricerca log - Definizioni e tipi

Le regole di ricerca log vengono create da Avvisi di Azure in modo da eseguire automaticamente le query di log specificate a intervalli regolari.  Se i risultati della query del log corrispondono a criteri specifici viene creato un record di avviso. La regola può quindi eseguire automaticamente una o più azioni tramite [gruppi di azioni](monitoring-action-groups.md). 

Le regole di ricerca log sono definite dai dettagli seguenti:
- **Query di log**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query vengono usati per determinare se viene creato un avviso. Una query di Analytics può includere anche [chiamate tra applicazioni](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), [chiamate tra aree di lavoro e [chiamate tra risorse](../log-analytics/log-analytics-cross-workspace-search.md) purché l'utente disponga dei diritti di accesso alle applicazioni esterne. 

    > [!IMPORTANT]
    > L'utente deve possedere il ruolo di [Collaboratore di Monitoraggio di Azure](monitoring-roles-permissions-security.md) per creare, modificare e aggiornare gli avvisi dei log in Monitoraggio di Azure, nonché i diritti di accesso ed esecuzione di query per i database di destinazione per l'analisi in una regola di avviso o una query di avvisi. Se l'utente che crea non ha accesso a tutti i database di destinazione per l'analisi nella regola di avviso o nella query di avvisi, la creazione della regola può avere esito negativo e la regola di avviso dei log verrà eseguita con risultati parziali.

- **Periodo di tempo**.  Specifica l'intervallo di tempo per la query. La query restituisce solo i record creati in questo intervallo dell'ora corrente. Il periodo di tempo limita i dati recuperati per la query di log in modo da impedirne l'uso improprio e ignora qualsiasi comando di tempo (come ago) usato nella query di log. <br>*Ad esempio, se il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15, vengono restituiti solo i record creati tra le 12.15 e le 13.15 per l'esecuzione della query di log. Se la query di log usa un comando di tempo come ago (7d), la query viene eseguita solo per i dati creati tra le 12.15 e le 13.15, ovvero solo per i dati che esistono dagli ultimi 60 minuti e non per i sette giorni di dati specificati nella query di log.*
- **Frequenza**.  Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore del periodo di tempo.  Se il valore è maggiore del periodo di tempo, il record rischia di essere omesso.<br>*Ad esempio, considerando un periodo di tempo di 30 minuti e una frequenza di 60 minuti,  Una query eseguita alle 13:00 restituirà i record compresi tra le 12:30 e le 13:00.  La volta successiva, la query verrà eseguita alle 14:00 e restituirà i record compresi tra le 13:30 e le 14:00.  Qualsiasi record creato tra le 13.00 e le 13.30 non verrà mai valutato.*
- **Soglia**.  Per determinare se è necessario creare un avviso, vengono valutati i risultati della ricerca log.  La soglia è diversa per tipi diversi di regole di avviso di ricerca log.

Le regole di ricerca log, sia per [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) sia per [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), possono essere di due tipi diversi. Ognuno di questi tipi viene descritto in dettaglio nelle sezioni seguenti.

- **[Numero di risultati](#number-of-results-alert-rules)**. Singolo avviso creato quando i record di numeri restituiti dalla ricerca log superano un numero specificato.
- **[Unità di misura della metrica](#metric-measurement-alert-rules)**.  Avviso creato per ogni oggetto nei risultati della ricerca log quando i valori superano la soglia specificata.

Di seguito sono riportate le differenze tra i tipi di regola di avviso.

- Le regole di avviso *Numero di risultati* creano sempre un singolo avviso, mentre una regola di avviso *Unità di misura della metrica* crea un avviso per ogni oggetto che supera la soglia.
- Le regole di avviso *Numero di risultati* creano un avviso quando la soglia viene superata una sola volta. Le regole di avviso *Unità di misura della metrica* possono creare un avviso quando viene superata la soglia più di una volta in un intervallo di tempo specifico.

### <a name="number-of-results-alert-rules"></a>Regole di avviso Numero di risultati
Le regole di avviso **Numero di risultati** creano un singolo avviso quando il numero di record restituiti dalla query di ricerca supera la soglia specificata. Questo tipo di regola di avviso è adatto per l'uso con eventi come quelli dei log eventi di Windows, Syslog, WebApp Response e log personalizzati.  Si potrebbe voler creare un avviso quando viene creato un determinato evento di errore o quando vengono creati più eventi di errore entro un periodo di tempo specifico.

**Soglia**: la soglia per una regola di avviso Numero di risultati è maggiore o minore di un valore specifico.  Se il numero di record restituiti dalla ricerca log corrisponde a questi criteri, viene creato un avviso.

Per generare un avviso per un evento singolo, impostare il numero di risultati su un valore maggiore di 0 e controllare l'occorrenza di un singolo evento creato dopo l'ultima esecuzione della query. Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che non vengano creati più eventi entro un periodo di tempo specifico.  

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non registra uno di questi eventi entro un periodo di tempo specifico, dovrà essere creato un avviso.  In questo caso è necessario impostare la soglia su **minore di 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Esempio di avviso di log di tipo Numero di record
Si consideri uno scenario in cui si vuole sapere quando l'app basata sul Web fornisce una risposta agli utenti con codice 500, Errore interno del server. Si deve creare una regola di avviso con i dettagli seguenti:  
- **Query:** requests | where resultCode == "500"<br>
- **Periodo di tempo:** 30 minuti<br>
- **Frequenza avviso:** cinque minuti<br>
- **Valore di soglia:** maggiore di 0<br>

L'avviso esegue la query ogni 5 minuti, con 30 minuti di dati, per cercare tutti i record in cui il codice di risultato è 500. Se viene trovato anche un solo record di questo tipo, viene generato l'avviso e viene attivata l'azione configurata.

### <a name="metric-measurement-alert-rules"></a>Regole di avviso Unità di misura della metrica

- Le regole di avviso **Unità di misura della metrica** creano un avviso per ogni oggetto in una query con un valore che supera una soglia specificata.  Presentano le differenze seguenti dalle regole di avviso **Numero di risultati**.
- **Funzione di aggregazione**: determina il calcolo che viene eseguito e possibilmente un campo numerico da aggregare.  Ad esempio, **count()** restituisce il numero di record nella query, **avg(CounterValue)** restituisce la media del campo CounterValue nell'intervallo. La funzione di aggregazione nella query deve essere denominata AggregatedValue e deve fornire un valore numerico. 
- **Campo del gruppo**: viene creato un record con un valore aggregato per ogni istanza di questo campo e può essere generato un avviso per ognuna.  Ad esempio, se si desidera generare un avviso per ogni computer, si userà **dal Computer** 

    > [!NOTE]
    > Per le regole di avviso delle unità di misura metriche che si basano su Application Insights, è possibile specificare il campo per il raggruppamento dei dati. Per eseguire questa operazione, usare l'opzione **Aggrega in base a** nella definizione della regola.   
    
- **Intervallo**: definisce l'intervallo di tempo in cui i dati vengono aggregati.  Ad esempio, specificando **cinque minuti**, viene creato un record per ogni istanza del campo Gruppo, aggregato a intervalli di 5 minuti nel periodo di tempo specificato per l'avviso.

    > [!NOTE]
    > Per specificare l'intervallo, è necessario usare la funzione Bin nella query. Poiché la funzione bin() può generare risultati con intervalli di tempo diversi, il servizio convertirà automaticamente il comando bin in comando bin_at con l'ora appropriata al runtime per garantire risultati con punto fisso. Il tipo di misura della metrica dell'avviso di log è progettato per funzionare con le query che contengono comandi bin() singoli.
    
- **Soglia**: la soglia per le regole di avviso Unità di misurazione della metrica è definita da un valore di aggregazione e da un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

#### <a name="example-of-metric-measurement-type-log-alert"></a>Esempio di avviso di log di tipo Unità di misurazione
Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  Si deve creare una regola di avviso con i dettagli seguenti:  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **Periodo di tempo:** 30 minuti<br>
- **Frequenza avviso:** cinque minuti<br>
- **Valore di aggregazione:** maggiore di 90<br>
- **Attiva l'avviso in base a:** totale violazioni maggiore di 2<br>

La query crea un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti.  Di seguito sono illustrati dati di esempio per tre computer.

![Risultati della query di esempio](media/monitor-alerts-unified-log/metrics-measurement-sample-graph.png)

In questo esempio vengono creati avvisi separati per srv02 e srv03, in quanto hanno superato la soglia del 90% per tre volte nel periodo di tempo.  Se l'impostazione di **Attiva l'avviso in base a:** venisse modificata in **Consecutivo** verrebbe creato un avviso solo per srv03 poiché ha violato la soglia di tre volte consecutive.

## <a name="log-search-alert-rule---firing-and-state"></a>Regola di avviso di ricerca log - attivazione e stato
La regola di avviso di ricerca log funziona sulla logica dichiarata dall'utente in base alla configurazione e alla query di analisi personalizzata usata. Questo perché la logica della condizione esatta o il motivo per cui dovrebbe attivarsi la regola di avviso è incapsulato in una query di Analytics, che può differire in ogni regola di avviso di log. Il servizio Avvisi di Azure dispone di poche informazioni sulla specifica causa radice sottostante all'interno dei risultati del log quando viene soddisfatta o superata la condizione di soglia della regola di avviso di ricerca log. Gli avvisi di log vengono quindi definiti come senza stato e generati ogni volta che il risultato della ricerca log è sufficiente a superare la soglia specificata negli avvisi dei log del tipo di condizione *numero di risultati* o *unità di misurazione*. Le regole di avviso di log continueranno inoltre a essere generate, fintanto che la condizione di avviso viene soddisfatta dal risultato della query di analisi personalizzata specificata; senza l'avviso tutto viene risolto. Poiché la logica della causa radice esatta dell'errore di monitoraggio è mascherata nella query di analisi specificata dall'utente, non esiste modo con cui Avvisi di Azure deduca se il risultato della ricerca log che non soddisfa la soglia indica la risoluzione del problema.

Supponiamo ora di avere una regola di avviso di log denominata *Contoso-Log-Alert*, come da configurazione nell'[esempio presentato per l'avviso di log di tipo Numero di risultati](#example-of-number-of-records-type-log-alert). 
- Alle 13.05, dopo l'esecuzione di Contoso-Log-Alert in Avvisi di Azure, il risultato della ricerca log ha prodotto 0 record: sotto la soglia e quindi non viene generato alcun avviso. 
- Alla successiva iterazione che avviene alle 13.10, dopo l'esecuzione di Contoso-Log-Alert in Avvisi di Azure, il risultato della ricerca log ha prodotto 5 record: la soglia è superata e l'avviso viene generato. Subito dopo viene attivato il [gruppo di azioni](monitoring-action-groups.md) associato. 
- Alle 13.15, dopo l'esecuzione di Contoso-Log-Alert in Avvisi di Azure, il risultato della ricerca log ha prodotto 2 record: la soglia è superata e l'avviso viene generato. Subito dopo viene attivato il [gruppo di azioni](monitoring-action-groups.md) associato.
- Alla successiva iterazione delle 13:20, dopo l'esecuzione di Contoso-Log-Alert in Avvisi di Azure, il risultato della ricerca log ha prodotto di nuovo 0 record: sotto la soglia e quindi non viene generato alcun avviso.

Nel caso sopra elencato, tuttavia, alle 13.15 Avvisi di Azure non può determinare che i problemi sottostanti incontrati alle 13:10 persistono e se si sono verificati errori del tutto nuovi. Poiché la query specificata dall'utente potrebbe prendere in considerazione i record precedenti, Avvisi di Azure non può avere certezze. Per sicurezza, Contoso-Log-Alert viene quindi generato nuovamente alle 13.15 tramite il [gruppo di azioni](monitoring-action-groups.md) configurato. Alle 13.20, quando non vengono riscontrati record, Avvisi di Azure non può essere certo che la causa dei record sia stata risolta. Contoso-Log-Alert non verrà pertanto impostato su Risolto nel dashboard di Avvisi di Azure e/o non verranno inviate notifiche di risoluzione dell'avviso.


## <a name="pricing-and-billing-of-log-alerts"></a>Prezzi e fatturazione degli avvisi dei log
I prezzi applicabili agli avvisi dei log sono disponibili nella pagina [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). Nelle fatture di Azure gli avvisi dei log sono rappresentati come tipo `microsoft.insights/scheduledqueryrules` con:
- Avvisi dei log in Application Insights visualizzati con il nome esatto insieme al gruppo di risorse e alle proprietà dell'avviso
- Avvisi dei log in Log Analytics visualizzati con il nome `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` insieme al gruppo di risorse e alle proprietà dell'avviso

    > [!NOTE]
    > Il nome per tutte le ricerche salvate, per le pianificazioni e per le azioni create con l'API Log Analytics deve essere in minuscolo. Se vengono usati caratteri non validi, ad esempio `<, >, %, &, \, ?, /`, nella fattura saranno sostituiti con `_`.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [creazione di avvisi dei log in Azure](alert-log.md).
* Acquisire familiarità con i [webhook negli avvisi dei log in Azure](monitor-alerts-unified-log-webhook.md).
* Informazioni su [Avvisi di Azure](monitoring-overview-unified-alerts.md).
* Altre informazioni su [Application Insights](../application-insights/app-insights-analytics.md).
* Altre informazioni su [Log Analytics](../log-analytics/log-analytics-queries.md).    
