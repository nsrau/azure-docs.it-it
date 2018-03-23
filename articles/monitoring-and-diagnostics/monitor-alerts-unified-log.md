---
title: Avvisi del log in Monitoraggio di Azure - Avvisi (anteprima) | Microsoft Docs
description: Attivare messaggi di posta elettronica o notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni specificate di query complesse per Avvisi di Azure (anteprima).
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: 0cee8bf77e0facc12159b823152b8859ce5cedd8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Avvisi del log in Monitoraggio di Azure - Avvisi (anteprima)
Questo articolo contiene informazioni dettagliate sul funzionamento delle regole di avviso nelle query di Analytics in Avvisi di Azure (anteprima) e la descrizione delle differenze tra diversi tipi di regole di avviso del log. Per informazioni sugli avvisi delle metriche basati su log, vedere [Avvisi delle metriche near real time](monitoring-near-real-time-metric-alerts.md).

Al momento, Avvisi di Azure (anteprima), supporta gli avvisi del log per le query di [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) e [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).

> [!WARNING]

> L'avviso del log in Avvisi di Azure (anteprima) attualmente non supporta le query tra aree di lavoro o tra app.

Gli utenti possono anche perfezionare le query nella piattaforma Analytics preferita in Azure e quindi *importarle per l'uso in Avvisi (anteprima) salvandole*. Procedura da seguire:
- Per Application Insights: passare al portale di Analytics, convalidare la query e i relativi risultati. Salvare quindi con nome univoco in *Query condivise*.
- Per Log Analytics: passare a Ricerca Log, convalidare la query e i relativi risultati. Salvare quindi con nome univoco in qualsiasi categoria.

Quindi durante la [creazione di un avviso del log in Avvisi (anteprima)](monitor-alerts-unified-usage.md) viene visualizzata la query salvata elencata come tipo di segnale **Log (query salvata)**, come illustrato nell'esempio seguente: ![Query salvata importata in Avvisi](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> L'uso di **Log (query salvata)** ha come risultato l'importazione in Avvisi. Di conseguenza, tutte le modifiche apportate in seguito in Analytics non si rifletteranno nelle regole di avviso salvate e viceversa.

## <a name="log-alert-rules"></a>Regole di avviso del log

Gli avvisi vengono creati tramite Avvisi di Azure (anteprima) per eseguire automaticamente query del log a intervalli regolari.  Se i risultati della query del log corrispondono a criteri specifici viene creato un record di avviso. La regola può quindi eseguire automaticamente una o più azioni per notificare l'avviso all'utente in modo proattivo o richiamare un altro processo come l'invio di dati a un'applicazione esterna tramite [webhook basato su JSON](monitor-alerts-unified-log-webhook.md), usando [Gruppi di azioni](monitoring-action-groups.md). I diversi tipi di regole di avviso usano una logica diversa per eseguire l'analisi.

Le regole di avviso vengono definite dai dettagli seguenti:

- **Query di log**.  La query eseguita ogni volta che viene attivata la regola di avviso.  I record restituiti da questa query vengono usati per determinare se viene creato un avviso.
- **Intervallo di tempo**.  Specifica l'intervallo di tempo per la query.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  L'intervallo di tempo può essere un valore qualsiasi compreso tra 5 minuti e 1440 minuti o 24 ore. Ad esempio, se l'intervallo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15 , vengono restituiti solo i record creati tra le 12.15 e le 13.15.
- **Frequenza**.  Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore dell'intervallo di tempo.  Se il valore è maggiore dell'intervallo di tempo, si rischia di omettere il record.<br>Si considerino ad esempio un intervallo di tempo di 30 minuti e una frequenza pari a 60 minuti.  Una query eseguita alle 13:00 restituirà i record compresi tra le 12:30 e le 13:00.  La volta successiva, la query verrà eseguita alle 14:00 e restituirà i record compresi tra le 13:30 e le 14:00.  Qualsiasi record creato tra le 13:00 e 13:30 non verrà mai valutato.
- **Soglia**.  Per determinare se è necessario creare un avviso, vengono valutati i risultati della ricerca log.  La soglia è diversa per i diversi tipi di regole di avviso.

Ogni regola di avviso di Log Analytics è di uno fra due tipi.  Ognuno di questi tipi viene descritto in dettaglio nelle sezioni seguenti.

- **[Numero di risultati](#number-of-results-alert-rules)**. Singolo avviso creato quando i record di numeri restituiti dalla ricerca log superano un numero specificato.
- **[Unità di misura della metrica](#metric-measurement-alert-rules)**.  Avviso creato per ogni oggetto nei risultati della ricerca log quando i valori superano la soglia specificata.

Di seguito sono riportate le differenze tra i tipi di regola di avviso.

- **La regola di avviso Numero di risultati crea sempre un avviso singolo, mentre le regola di avviso **Unità di misura della metrica** crea un avviso per ogni oggetto che supera la soglia.
- Le regole di avviso **Numero di risultati** creano un avviso quando la soglia viene superata una sola volta. Le regole di avviso **Unità di misura della metrica** possono creare un avviso quando viene superata la soglia più di una volta in un intervallo di tempo specifico.

## <a name="number-of-results-alert-rules"></a>Regole di avviso Numero di risultati
Le regole di avviso **Numero di risultati** creano un singolo avviso quando il numero di record restituiti dalla query di ricerca supera la soglia specificata. Questo tipo di regola di avviso è adatto per l'uso con eventi come quelli dei log eventi di Windows, Syslog, WebApp Response e log personalizzati.  È consigliabile creare un avviso quando viene creato un evento di errore specifico o vengono creati più eventi di errore entro un intervallo di tempo specifico.

**Soglia**: la soglia per una regola di avviso **Numero di risultati è maggiore o minore di un valore specifico.  Se il numero di record restituiti dalla ricerca log corrisponde a questi criteri, viene creato un avviso.

Per generare un avviso per un evento singolo, impostare il numero di risultati su un valore maggiore di 0 e controllare l'occorrenza di un singolo evento creato dopo l'ultima esecuzione della query. Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che in un intervallo di tempo specifico non vengano creati più eventi.  

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non viene registrato uno di questi eventi in un intervallo di tempo specifico, dovrà essere creato un avviso.  In questo caso è necessario impostare la soglia su **minore di 1**.

### <a name="example"></a>Esempio
Si consideri uno scenario in cui si vuole sapere quando l'app basata sul Web fornisce una risposta agli utenti con codice 500, Errore interno del server. Si deve creare una regola di avviso con i dettagli seguenti:  
**Query:** requests | where resultCode == "500"<br>
**Intervallo di tempo:** 30 minuti<br>
**Frequenza avviso:** cinque minuti<br>
**Valore di soglia:** maggiore di 0<br>

L'avviso esegue la query ogni 5 minuti, con 30 minuti di dati, per cercare tutti i record in cui il codice di risultato è 500. Se viene trovato anche un solo record di questo tipo, viene generato l'avviso e viene attivata l'azione configurata.

## <a name="metric-measurement-alert-rules"></a>Regole di avviso Unità di misura della metrica

Le regole di avviso **Unità di misura della metrica** creano un avviso per ogni oggetto in una query con un valore che supera una soglia specificata.  Presentano le differenze seguenti dalle regole di avviso **Numero di risultati**.

**Funzione di aggregazione**: determina il calcolo che viene eseguito e possibilmente un campo numerico da aggregare.  Ad esempio, **count()** restituisce il numero di record nella query, **avg(CounterValue)** restituisce la media del campo CounterValue nell'intervallo.

> [!NOTE]

> La funzione di aggregazione nella query deve essere denominata AggregatedValue e deve fornire un valore numerico. 


**Campo del gruppo**: viene creato un record con un valore aggregato per ogni istanza di questo campo e può essere generato un avviso per ognuna.  Ad esempio, se si desidera generare un avviso per ogni computer, si userà **dal Computer**   

> [!NOTE]

> Per le regole di avviso delle unità di misura metriche che si basano su Application Insights, è possibile specificare il campo per il raggruppamento dei dati. Per eseguire questa operazione, usare l'opzione **Aggrega in base a** nella definizione della regola.   

**Intervallo**: definisce l'intervallo di tempo in cui i dati vengono aggregati.  Ad esempio, se è stato specificato **cinque minuti**, viene creato un record per ogni istanza del campo Gruppo aggregato a intervalli di 5 minuti nella finestra temporale specificata per l'avviso.
> [!NOTE]
> La funzione Bin deve essere usata nella query. Poiché bin() può generare risultati con intervalli di tempo diversi, Avvisi usa la funzione bin_at con un orario appropriato in fase di runtime per garantire risultati con un punto fisso.

**Soglia**: la soglia per le regole di avviso Unità di misurazione della metrica è definita da un valore di aggregazione e da un numero di violazioni della sicurezza.  Se qualsiasi punto dati in una ricerca di log supera questo valore, ciò viene considerato una violazione.  Se il numero di violazioni per un oggetto nei risultati supera il valore specificato, viene creato un avviso per l'oggetto.

#### <a name="example"></a>Esempio
Si consideri uno scenario in cui si desidera creare un avviso se l'uso del processo di un computer supera il 90% tre volte in 30 minuti.  Si deve creare una regola di avviso con i dettagli seguenti:  

**Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
**Intervallo di tempo:** 30 minuti<br>
**Frequenza avviso:** cinque minuti<br>
**Valore di aggregazione:** maggiore di 90<br>
**Attiva l'avviso in base a:** totale violazioni maggiore di 5<br>

La query crea un valore medio per ogni computer a intervalli di 5 minuti.  Questa query verrebbe eseguita ogni 5 minuti per i dati raccolti nei 30 minuti precedenti.  Di seguito sono illustrati dati di esempio per tre computer.

![Risultati della query di esempio](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

In questo esempio, verranno creati avvisi separati per srv02 e srv03 poiché hanno superato la soglia del 90% 3 volte nell'intervallo di tempo.  Se l'impostazione di **Attiva l'avviso in base a:** venisse modificata in **Consecutivo** verrebbe creato un avviso solo per srv03 poiché ha violato la soglia di tre volte consecutive.


## <a name="next-steps"></a>Passaggi successivi
* Comprendere le [azioni webhook per gli avvisi del log](monitor-alerts-unified-log-webhook.md)
* [Panoramica di Avvisi di Azure (anteprima)](monitoring-overview-unified-alerts.md)
* Informazioni sull'[Uso di Avvisi di Azure (anteprima)](monitor-alerts-unified-usage.md)
* Altre informazioni su [Application Insights](../application-insights/app-insights-analytics.md)
* Altre informazioni su [Log Analytics](../log-analytics/log-analytics-overview.md).    
