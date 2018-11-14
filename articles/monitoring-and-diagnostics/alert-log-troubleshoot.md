---
title: Risoluzione dei problemi relativi agli avvisi del log nel Monitoraggio di Azure
description: Problemi comuni, errori e risoluzione per le regole di avviso del log in Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5572c80879584e7f6df650263ae455a134ee4088
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283598"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Risoluzione dei problemi relativi agli avvisi del log nel Monitoraggio di Azure  

## <a name="overview"></a>Panoramica
Questo articolo mostra come gestire gli errori comuni riscontrati durante la configurazione degli avvisi del log in Monitoraggio di Azure e offre soluzioni ad alcune domande frequenti relative alla funzionalità o alla configurazione degli avvisi del log. Il termine **Avvisi del log** viene usato per descrivere gli avvisi in cui il segnale è basato su una query personalizzata in [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) o [Application Insights](../application-insights/app-insights-analytics.md). Altre informazioni su funzionalità, terminologia e tipi su [Avvisi del log - Panoramica](monitor-alerts-unified-log.md).

> [!NOTE]
> Questo articolo non prende in considerazione i casi in cui la regola di avviso viene visualizzata come attivata nel portale di Azure e nella notifica tramite gruppi di azioni associati. Per questi casi, fare riferimento ai dettagli nell'articolo sui [gruppi di azioni](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Avviso del log non attivato

Di seguito sono riportati in dettaglio alcuni motivi per cui una [regola di avviso del log in Monitoraggio di Azure](alert-log.md) configurata non viene attivata se visualizzata negli [avvisi di Azure](monitoring-alerts-managing-alert-states.md), contrariamente a quanto dovrebbe invece accadere. 

### <a name="data-ingestion-time-for-logs"></a>Tempo di inserimento dati per i log
Il funzionamento degli avvisi del log si basa sull'esecuzione periodica di query specificate dai clienti in base a [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) oppure [Application Insights](../application-insights/app-insights-analytics.md). Entrambi si basano sulla potenza di Analytics, che elabora quantità elevate di dati di log e ne garantisce la funzionalità. Dal momento che il servizio Log Analytics comporta l'elaborazione di diversi terabyte di dati di migliaia di clienti e origini di vario tipo in tutto il mondo, il servizio è soggetto a ritardi. Per altre informazioni, vedere [Tempo di inserimento dei dati in Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Per ovviare al ritardo di inserimento dei dati che può verificarsi nei log di Log Analytics o Application Insights, l'avviso rimane in attesa ed esegue nuovi tentativi quando realizza che i dati non sono ancora stati inseriti per il periodo specificato. Gli avvisi del log hanno un tempo di attesa impostato che aumenta in modo esponenziale, in modo da essere certi di attendere il tempo necessario per l'inserimento dei dati da parte di Log Analytics. Se pertanto i log sottoposti a query dalla regola di avviso subiscono ritardi di inserimento, l'avviso del log verrà attivato solo dopo che i dati risulteranno disponibili in Log Analytics in seguito all'inserimento e dopo l'intervallo esponenziale, poiché il servizio di avviso del log esegue nel frattempo nuovi tentativi.

### <a name="incorrect-time-period-configured"></a>Periodo di tempo errato configurato
Come descritto nell'articolo sulla [terminologia specifica per gli avvisi del log](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), il periodo di tempo indicato nella configurazione specifica l'intervallo di tempo per la query. La query restituisce solo i record creati in questo intervallo temporale. Il periodo di tempo limita i dati recuperati per la query di log in modo da impedirne l'uso improprio e ignora qualsiasi comando di tempo (come ago) usato nella query di log. 
*Ad esempio, se il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15, vengono restituiti solo i record creati tra le 12.15 e le 13.15 per l'esecuzione della query di log. Se la query di log usa un comando di tempo come ago (1d), la query viene eseguita solo per i dati creati tra le 12.15 e le 13.15, ovvero solo per i dati che esistono dagli ultimi 60 minuti e non per i sette giorni di dati specificati nella query di log.*

In base alla logica di query, controllare se è stato specificato un periodo di tempo appropriato nella configurazione. Per l'esempio indicato in precedenza, se la query di log usa il comando ago (1d) come illustrato con l'indicatore verde, il periodo di tempo deve essere impostato su 24 ore o 1440 minuti (come indicato in rosso), per garantire che la query specificata venga eseguita correttamente come previsto.
    ![Periodo di tempo](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Opzione Elimina avvisi impostata
Come descritto nel passaggio 8 dell'articolo sulla [creazione di una regola di avviso del log nel portale di Azure](alert-log.md#managing-log-alerts-from-the-azure-portal), l'avviso del log offre un'opzione per configurare l'eliminazione automatica della regola di avviso ed evitare la notifica o l'attivazione per il periodo di tempo specificato. Con l'opzione Elimina avvisi, l'avviso del log verrà eseguito senza attivare il gruppo di azioni per il tempo specificato nell'opzione **Elimina avvisi** e quindi l'utente potrebbe credere che l'avviso non sia stato attivato, mentre in realtà è stato eliminato come da configurazione.
    ![Elimina avvisi](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Regola di avviso di unità di misura della metrica errata
Il tipo di unità di misura della metrica della regola di avviso del log è un sottotipo degli avvisi del log, che presenta funzionalità speciali, ma impiega a sua volta una limitazione sulla sintassi della query di avviso. La regola di avviso del log dell'unità di misura della metrica richiede che l'output della query di avviso specifichi una serie temporale metrica: una tabella con periodi di tempo distinti ed equivalenti e contenente i valori corrispondenti dell'elemento AggregatedValue calcolato. Gli utenti possono anche scegliere di includere nella tabella variabili aggiuntive insieme ad AggregatedValue, ad esempio Computer, Node e così via. L'uso di questi dati nella tabella può essere ordinato.

Si supponga, ad esempio, che la regola di avviso del log dell'unita di misura della metrica sia stata configurata come segue:
- Query: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Periodo di tempo di 6 ore
- Soglia di 50
- Logica dell'avviso di tre violazioni consecutive
- Aggregate Upon impostato su $table

Ora, poiché per il comando è stato usato summarize... by e sono state indicate due variabili (timestamp e $table), il servizio di avviso sceglie $table per "Aggregate Upon", ordinando la tabella dei risultati per il campo $table, come illustrato di seguito, e quindi cerca le varie entità AggregatedValue per ogni tipo di tabella (come availabilityResults) per verificare la presenza di almeno 3 violazioni consecutive.

   ![Esecuzione della query di unità di misura della metrica con più valori](./media/monitor-alerts-unified/LogMMQuery.png)

Quando "Aggregate Upon" è $table, i dati vengono ordinati nella colonna $table (come indicato in rosso), quindi si esegue il raggruppamento e la ricerca per tipi di campo "Aggregate upon" con valore $table. I valori per availabilityResults verranno ad esempio considerati come un unico tracciato/entità (come evidenziato in arancione). In questo valore tracciato/entità, il servizio di avviso verifica la presenza di tre violazioni consecutive (come visualizzato in verde) per cui l'avviso verrà attivato per il valore di tabella 'availabilityResults'. In modo analogo, se per qualsiasi altro valore di $table sono presenti tre violazioni consecutive, verrà attivata un'altra notifica di avviso e il servizio ordinerà automaticamente i valori in un tracciato/entità (in arancione) in base all'ora.

Si supponga ora che la regola di avviso del log dell'unità di misura della metrica sia stata modificata, che la query sia `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` e che il resto della configurazione rimanga inalterato prima di includere la logica dell'avviso per tre violazioni consecutive. L'opzione "Aggregate Upon" sarà in questo caso timestamp per impostazione predefinita. Poiché viene fornito un solo valore nella query per summarize... by (timestamp), in modo analogo all'esempio precedente, al termine dell'esecuzione l'output sarà simile a quanto illustrato di seguito. 

   ![Esecuzione della query di unità di misura della metrica con un valore](./media/monitor-alerts-unified/LogMMtimestamp.png)

Quando "Aggregate Upon" è timestamp, i dati vengono ordinati nella colonna timestamp (come indicato in rosso), quindi di esegue il raggruppamento per timestamp. I valori per `2018-10-17T06:00:00Z` verranno ad esempio considerati come un unico tracciato/entità (come evidenziato in arancione). In questo valore tracciato/entità, il servizio di avviso non individuerà alcuna violazione consecutiva (poiché ogni valore di timestamp ha solo una voce) e di conseguenza non verrà attivato l'avviso. In questo caso, l'utente dovrà eseguire una di queste due operazioni:
- Aggiungere una variabile fittizia o una variabile esistente (ad esempio $table) per eseguire correttamente l'ordinamento con il campo "Aggregate Upon" configurato
- Riconfigurare la regola di avviso per l'uso della logica dell'avviso in base alle *violazioni totali* in modo appropriato
 
## <a name="log-alert-fired-unnecessarily"></a>Avviso del log inutilmente attivato
Di seguito sono riportati in dettaglio alcuni motivi per cui una [regola di avviso del log in Monitoraggio di Azure](alert-log.md) configurata potrebbe essere attivata se visualizzata negli [avvisi di Azure](monitoring-alerts-managing-alert-states.md), contrariamente a quanto invece dovrebbe accadere.

### <a name="alert-triggered-by-partial-data"></a>Avviso attivato da dati parziali
Analytics che potenzia Log Analytics e Application Insights è soggetto a ritardi di inserimento ed l'elaborazione. Per questo motivo, nel momento in cui viene eseguita la query di avviso del log specificata, potrebbero verificarsi scenari in cui non sono disponibili dati o ne sono disponibili solo alcuni. Per altre informazioni, vedere [Tempo di inserimento dei dati in Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

A seconda del modo in cui è configurata la regola di avviso, potrebbero verificarsi attivazioni errate se al momento dell'esecuzione dell'avviso non sono presenti dati nei log o i dati sono solo parziali. In questi casi è consigliabile modificare la query dell'avviso o la configurazione. *Se ad esempio la regola di avviso del log è configurata per l'attivazione quando il numero di risultati della query Analytics è minore di 5, la regola verrà attivata in assenza di dati (zero record) o in presenza di risultati parziali (un record). Dopo il ritardo di inserimento, invece, quando la stessa query viene eseguita in Analytics, la query con dati completi potrebbe generare un risultato di 10 record.*

### <a name="alert-query-output-misunderstood"></a>Output della query di avviso frainteso
Per gli avvisi del log, la logica di generazione degli avvisi viene specificata dall'utente tramite una query Analytics. Questa query può usare vari Big Data e funzioni matematiche per creare specifici costrutti. Il servizio di generazione di avvisi eseguirà la query creata dal cliente a determinati intervalli con i dati per il periodo di tempo specificato. Il servizio apporta lievi modifiche alla query in base al tipo di avviso scelto. Questa condizione può essere riscontrata anche nella sezione "Query da eseguire" della schermata Configura logica dei segnali, come illustrato di seguito: ![Query da eseguire](./media/monitor-alerts-unified/LogAlertPreview.png)
 
La sezione **Query da eseguire** mostra ciò che verrà eseguito dal servizio di avviso del log. L'utente può eseguire la query specificata e l'intervallo di tempo tramite il [portale di Analytics](../log-analytics/log-analytics-log-search-portals.md) o l'[API Analytics](https://docs.microsoft.com/rest/api/loganalytics/) se intende conoscere l'output della query di avviso prima che venga creato.
 
## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli [avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md)
* Altre informazioni su [Application Insights](../application-insights/app-insights-analytics.md)
* Altre informazioni su [Log Analytics](../log-analytics/log-analytics-queries.md). 