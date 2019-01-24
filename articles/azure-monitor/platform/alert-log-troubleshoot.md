---
title: Risoluzione dei problemi relativi agli avvisi del log nel Monitoraggio di Azure
description: Problemi comuni, errori e risoluzione per le regole di avviso del log in Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 56d76cd43b63a389569ae39c1e987a5fccbb9793
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429447"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Risoluzione dei problemi relativi agli avvisi del log nel Monitoraggio di Azure  

## <a name="overview"></a>Panoramica

Questo articolo mostra come risolvere gli errori comuni riscontrati durante la configurazione degli avvisi del log in Monitoraggio di Azure. Fornisce inoltre le soluzioni ad alcune domande frequenti relative alla funzionalità o alla configurazione degli avvisi del log. 

Il termine **avvisi del log** fa riferimento agli avvisi che vengono attivati in base a una query personalizzata in [Log Analytics](../learn/tutorial-viewdata.md) o [Application Insights](../../azure-monitor/app/analytics.md). Altre informazioni su funzionalità, terminologia e tipi sono disponibili in [Avvisi del log - Panoramica](../platform/alerts-unified-log.md).

> [!NOTE]
> Questo articolo non prende in considerazione i casi in cui il portale di Azure visualizza una regola di avviso come attivata e una notifica come eseguita tramite gruppi di azioni associati. Per questi casi, fare riferimento ai dettagli nell'articolo sui [gruppi di azioni](../platform/action-groups.md).


## <a name="log-alert-didnt-fire"></a>Avviso del log non attivato

Di seguito sono elencati alcuni motivi per cui una [regola di avviso del log configurata in Monitoraggio di Azure](../platform/alerts-log.md) non viene visualizzata [come *attivata* quando previsto](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tempo di inserimento dati per i log

Un avviso del log esegue periodicamente una query basata su [Log Analytics](../learn/tutorial-viewdata.md) o [Application Insights](../../azure-monitor/app/analytics.md). Poiché Log Analytics elabora molti terabyte di dati di migliaia di clienti da origini di vario tipo in tutto il mondo, il servizio è soggetto a ritardi variabili. Per altre informazioni, vedere [Tempo di inserimento dei dati in Log Analytics](../platform/data-ingestion-time.md).

Per ridurre il ritardo di inserimento dei dati, il sistema rimane in attesa e ritenta più volte di eseguire la query di avviso se rileva che i dati necessari non sono ancora stati inseriti. Il sistema ha un tempo di attesa impostato che aumenta in modo esponenziale. L'avviso del log viene attivato solo dopo che i dati risultano disponibili, pertanto il ritardo potrebbe essere dovuto alla lentezza dell'inserimento dei dati di log. 

### <a name="incorrect-time-period-configured"></a>Periodo di tempo errato configurato

Come descritto nell'articolo sulla [terminologia specifica per gli avvisi del log](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), il periodo di tempo indicato nella configurazione specifica l'intervallo di tempo per la query. La query restituisce solo i record creati in questo intervallo temporale. Il periodo di tempo limita i dati recuperati per la query di log in modo da impedirne l'uso improprio e ignora qualsiasi comando di tempo (come *ago*) usato nella query di log. Se ad esempio il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15, solo i record creati tra le 12.15 e le 13.15 vengono usati per la query di log. Se la query di log usa un comando di tempo come *ago (1d)*, la query usa ancora solo i dati tra le 12.15 e le 13.15 perché il periodo di tempo è impostato su tale intervallo.*

Controllare pertanto che il periodo di tempo specificato nella configurazione corrisponda alla query. Per l'esempio indicato in precedenza, se la query di log usa il comando *ago (1d)* come illustrato con l'indicatore verde, il periodo di tempo deve essere impostato su 24 ore o 1440 minuti (come indicato in rosso), per garantire che la query venga eseguita come previsto.

![Periodo di tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Opzione Elimina avvisi impostata

Come descritto nel passaggio 8 dell'articolo sulla [creazione di una regola di avviso del log nel portale di Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), gli avvisi del log offrono un'opzione **Elimina avvisi** per eliminare le azioni di attivazione e notifica per un periodo di tempo configurato. Di conseguenza, l'utente potrebbe credere che un avviso non sia stato attivato, mentre in realtà lo è stato ma è stato eliminato.  

![Elimina avvisi](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Regola di avviso di unità di misura della metrica errata

Gli **avvisi del log dell'unità di misura della metrica** sono un sottotipo di avvisi del log, che presentano funzionalità speciali e una sintassi della query di avviso limitata. Una regola di avviso del log dell'unità di misura della metrica richiede che l'output della query sia una serie temporale metrica; ovvero, una tabella con periodi di tempo distinti ed equivalenti e contenente i valori aggregati corrispondenti. Gli utenti possono anche scegliere di includere variabili aggiuntive nella tabella insieme ad AggregatedValue. Queste variabili possono essere usate per ordinare la tabella. 

Si supponga, ad esempio, che una regola di avviso del log dell'unita di misura della metrica sia stata configurata come segue:

- Query: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Periodo di tempo di 6 ore
- Soglia di 50
- Logica dell'avviso di tre violazioni consecutive
- Aggregate Upon impostato su $table

Poiché il comando include *summarize ... by* e sono state indicate due variabili (timestamp e $table), il sistema sceglie $table per *Aggregate Upon*. Ordina inoltre la tabella dei risultati per il campo *$table*, come illustrato di seguito, e quindi cerca le varie entità AggregatedValue per ogni tipo di tabella (come availabilityResults) per verificare la presenza di almeno tre violazioni consecutive.

![Esecuzione della query di unità di misura della metrica con più valori](media/alert-log-troubleshoot/LogMMQuery.png)

Quando *Aggregate Upon* è definito in *$table*, i dati vengono ordinati nella colonna $table (come indicato in rosso), quindi si esegue il raggruppamento e la ricerca per tipi di campo *Aggregate upon* con valore $table. I valori per availabilityResults verranno ad esempio considerati come un unico tracciato/entità (come evidenziato in arancione). In questo valore tracciato/entità, il servizio di avviso verifica la presenza di tre violazioni consecutive (come visualizzato in verde) per cui l'avviso verrà attivato per il valore di tabella "availabilityResults". In modo analogo, se per qualsiasi altro valore di $table sono presenti tre violazioni consecutive, verrà attivata un'altra notifica di avviso e il servizio ordinerà automaticamente i valori in un tracciato/entità (in arancione) in base all'ora.

Si supponga ora che la regola di avviso del log dell'unità di misura della metrica sia stata modificata, che la query sia `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` e che il resto della configurazione rimanga inalterato prima di includere la logica dell'avviso per tre violazioni consecutive. L'opzione "Aggregate Upon" sarà in questo caso timestamp per impostazione predefinita. Poiché viene fornito un solo valore nella query per *summarize... by* (*timestamp*), in modo analogo all'esempio precedente, al termine dell'esecuzione l'output sarà simile a quanto illustrato di seguito.

   ![Esecuzione della query di unità di misura della metrica con un valore](media/alert-log-troubleshoot/LogMMtimestamp.png)

Quando *Aggregate Upon* è definito in timestamp, i dati vengono ordinati nella colonna *timestamp* (come indicato in rosso), quindi si esegue il raggruppamento per timestamp. I valori per `2018-10-17T06:00:00Z` verranno ad esempio considerati come un unico tracciato/entità (come evidenziato in arancione). In questo valore tracciato/entità, il servizio di avviso non troverà alcuna violazione consecutiva (perché ogni valore di timestamp ha solo una voce) e di conseguenza non verrà attivato l'avviso. In questo caso, l'utente dovrà eseguire una di queste due operazioni:

- Aggiungere una variabile fittizia o una variabile esistente (ad esempio $table) per eseguire correttamente l'ordinamento con il campo "Aggregate Upon" configurato
- Riconfigurare la regola di avviso per l'uso della logica dell'avviso in base alle *violazioni totali* in modo appropriato

## <a name="log-alert-fired-unnecessarily"></a>Avviso del log inutilmente attivato

Di seguito sono riportati in dettaglio alcuni motivi per cui una [regola di avviso del log in Monitoraggio di Azure](../platform/alerts-log.md) configurata potrebbe essere attivata se visualizzata negli [avvisi di Azure](../platform/alerts-managing-alert-states.md), contrariamente a quanto invece dovrebbe accadere.

### <a name="alert-triggered-by-partial-data"></a>Avviso attivato da dati parziali

Analytics che potenzia Log Analytics e Application Insights è soggetto a ritardi di inserimento ed l'elaborazione. Per questo motivo, nel momento in cui viene eseguita la query di avviso del log specificata, potrebbero verificarsi scenari in cui non sono disponibili dati o ne sono disponibili solo alcuni. Per altre informazioni, vedere [Tempo di inserimento dei dati in Log Analytics](../platform/data-ingestion-time.md).

A seconda del modo in cui è configurata la regola di avviso, potrebbero verificarsi attivazioni errate se al momento dell'esecuzione dell'avviso non sono presenti dati nei log o i dati sono solo parziali. In questi casi, è consigliabile modificare la configurazione o la query di avviso. 

Se ad esempio la regola di avviso del log è configurata per l'attivazione quando il numero di risultati di una query Analytics è minore di 5, l'avviso verrà attivato in assenza di dati (zero record) o in presenza di risultati parziali (un record). Tuttavia, dopo il ritardo nell'inserimento dei dati, la stessa query con i dati completi potrebbe fornire un risultato di 10 record.

### <a name="alert-query-output-misunderstood"></a>Output della query di avviso frainteso

La logica per gli avvisi di log viene specificata in una query di Analytics che può usare diverse funzioni matematiche e di Big Data.  Il servizio di generazione degli avvisi esegue la query a determinati intervalli con i dati per il periodo di tempo specificato. Il servizio apporta lievi modifiche alla query in base al tipo di avviso scelto. Questa condizione può essere riscontrata nella sezione "Query da eseguire" nella schermata *Configura logica dei segnali*, come illustrato di seguito: ![Query da eseguire](media/alert-log-troubleshoot/LogAlertPreview.png)

Nella casella **Query da eseguire** viene visualizzata la query che verrà eseguita dal servizio di generazione degli avvisi di log. È possibile eseguire la query specificata con i dati dell'intervallo di tempo indicato tramite il [portale di Analytics](../log-query/portals.md) o l'[API Analytics](https://docs.microsoft.com/rest/api/loganalytics/) se si vuole conoscere l'output della query di avviso prima che l'avviso venga effettivamente creato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli [avvisi del log in Avvisi di Azure](../platform/alerts-unified-log.md)
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md)
- Altre informazioni su [Log Analytics](../../log-analytics/log-analytics-overview.md)
