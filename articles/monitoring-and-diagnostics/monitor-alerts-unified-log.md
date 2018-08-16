---
title: Avvisi dei log in Monitoraggio di Azure
description: Attivare messaggi di posta elettronica e notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni di query di analisi specificate per Avvisi di Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: fd278ad6865c871ed0a5ed9272c9fadfca0f38db
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440430"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Avvisi dei log in Monitoraggio di Azure - Avvisi 
Questo articolo contiene informazioni dettagliate sugli avvisi dei log, uno dei tipi di avvisi supportati nel nuovo servizio [Avvisi di Azure](monitoring-overview-unified-alerts.md) che consente agli utenti di usare la piattaforma di analisi di Azure come base per la generazione di avvisi.


Un avviso di log è costituito da regole di ricerca log create per [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) o [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events). I dettagli sui prezzi per gli avvisi dei log sono disponibili nella pagina [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/en-us/pricing/details/monitor/). Nelle fatture di Azure gli avvisi dei log sono rappresentati come tipo `microsoft.insights/scheduledqueryrules` con:
- Avvisi dei log in Application Insights visualizzati con il nome esatto insieme al gruppo di risorse e alle proprietà dell'avviso
- Avvisi dei log in Log Analytics visualizzati con il nome `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` insieme al gruppo di risorse e alle proprietà dell'avviso

    > [!NOTE]
    > Il nome per tutte le ricerche salvate, per le pianificazioni e per le azioni create con l'API Log Analytics deve essere in minuscolo. Se vengono usati caratteri non validi, ad esempio `<, >, %, &, \, ?, /`, nella fattura saranno sostituiti con `_`.

## <a name="log-search-alert-rule---definition-and-types"></a>Regole di avviso di ricerca log - Definizioni e tipi

Le regole di ricerca log vengono create da Avvisi di Azure in modo da eseguire automaticamente le query di log specificate a intervalli regolari.  Se i risultati della query del log corrispondono a criteri specifici viene creato un record di avviso. La regola può quindi eseguire automaticamente una o più azioni tramite [gruppi di azioni](monitoring-action-groups.md). 

Le regole di ricerca log sono definite dai dettagli seguenti:
- **Query di log**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query vengono usati per determinare se viene creato un avviso. Una query di *Azure Application Insights* può anche includere [chiamate tra applicazioni](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), purché l'utente abbia diritti di accesso alle applicazioni esterne. 

    > [!IMPORTANT]
    > Il supporto delle [query tra applicazioni per Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) è disponibile in anteprima. La funzionalità limitata all'uso con 2 o più app e l'esperienza utente sono soggette a modifiche. L'utilizzo di [query tra aree di lavoro](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) e [query tra risorse per Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) attualmente **non è supportato** in Avvisi di Azure.

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

#### <a name="example"></a>Esempio
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
    > Per specificare l'intervallo, è necessario usare la funzione Bin nella query. Poiché bin() può generare risultati con intervalli di tempo diversi, il servizio convertirà automaticamente il comando bin in comando bin_at con l'ora appropriata al runtime per garantire risultati con punto fisso
    
- **Soglia**: la soglia per le regole di avviso Unità di misurazione della metrica è definita da un valore di aggregazione e da un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

#### <a name="example"></a>Esempio
Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  Si deve creare una regola di avviso con i dettagli seguenti:  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
- **Periodo di tempo:** 30 minuti<br>
- **Frequenza avviso:** cinque minuti<br>
- **Valore di aggregazione:** maggiore di 90<br>
- **Attiva l'avviso in base a:** totale violazioni maggiore di 2<br>

La query crea un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti.  Di seguito sono illustrati dati di esempio per tre computer.

![Risultati della query di esempio](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

In questo esempio vengono creati avvisi separati per srv02 e srv03, in quanto hanno superato la soglia del 90% tre volte nel periodo di tempo.  Se l'impostazione di **Attiva l'avviso in base a:** venisse modificata in **Consecutivo** verrebbe creato un avviso solo per srv03 poiché ha violato la soglia di tre volte consecutive.


## <a name="log-search-alert-rule---creation-and-modification"></a>Regole di avviso di ricerca log - Creazione e modifica

Un avviso di log, insieme alla regola di avviso di ricerca log che lo costituisce, può essere visualizzato, creato o modificato da:
- Portale di Azure
- API REST (anche tramite PowerShell)
- Modelli di Azure Resource Manager

### <a name="azure-portal"></a>Portale di Azure
Da quando è stato introdotto il [nuovo servizio Avvisi di Azure](monitoring-overview-unified-alerts.md), gli utenti possono gestire tutti i tipi di avvisi nel portale di Azure da un'unica posizione e con una procedura d'uso simile. Per altre informazioni, vedere [Uso del nuovo servizio Avvisi di Azure](monitor-alerts-unified-usage.md).

Gli utenti possono anche perfezionare le query nella piattaforma Analytics preferita in Azure e quindi *importarle per l'uso in Avvisi salvandole*. Procedura da seguire:
- *Per Application Insights*: passare al portale di Analytics e convalidare la query e i suoi risultati. Salvare quindi con nome univoco in *Query condivise*.
- *Per Log Analytics*: passare a Ricerca Log e convalidare la query e i suoi risultati. Salvare quindi con nome univoco in qualsiasi categoria.

Quindi, durante la [creazione di un avviso del log in Avvisi](monitor-alerts-unified-usage.md), viene visualizzata la query salvata ed elencata come tipo di segnale **Log (query salvata)**, come illustrato nell'esempio seguente: ![Query salvata e importata in Avvisi](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> L'uso di **Log (query salvata)** ha come risultato l'importazione in Avvisi. Di conseguenza, tutte le modifiche apportate in seguito in Analytics non influiranno sulle regole di avviso di ricerca log e viceversa.

### <a name="rest-apis"></a>API REST
Le API fornite per gli avvisi di log sono RESTful ed è possibile accedervi tramite l'API REST di Azure Resource Manager. Di conseguenza, è possibile accedervi anche tramite PowerShell, insieme ad altre opzioni per sfruttare le API.

Per informazioni dettagliate e alcuni esempi di uso dell'API REST, vedere le risorse seguenti:
- [API REST per gli avvisi in Log Analytics](../log-analytics/log-analytics-api-alerts.md) per informazioni su come creare e gestire le regole di avviso di ricerca log per Azure Log Analytics
- [API REST per le regole di query pianificate in Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) per informazioni su come creare e gestire regole di avviso di ricerca log per Azure Application Insights

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Gli utenti possono anche sfruttare la flessibilità offerta da [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) per la creazione e l'aggiornamento di risorse per creare o aggiornare gli avvisi di log.

Per informazioni dettagliate e alcuni esempi sull'uso di modelli di Resource Manager, vedere le risorse seguenti:
- [Gestione di ricerche salvate e avvisi](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) per informazioni sugli avvisi di log basati su Azure Log Analytics
- [Regola di query pianificata](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) per informazioni sugli avvisi di log basati su Azure Application Insights
 

## <a name="next-steps"></a>Passaggi successivi
* Acquisire familiarità con i [webhook negli avvisi dei log in Azure](monitor-alerts-unified-log-webhook.md).
* Leggere altre informazioni sul nuovo servizio [Avvisi di Azure ](monitoring-overview-unified-alerts.md).
* Altre informazioni su [Application Insights](../application-insights/app-insights-analytics.md).
* Altre informazioni su [Log Analytics](../log-analytics/log-analytics-overview.md).    
