---
title: Risoluzione dei problemi degli avvisi di log in Monitoraggio di Azure | Microsoft Docs
description: Problemi comuni, errori e risoluzione per le regole di avviso di log in Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775995"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Risoluzione dei problemi relativi agli avvisi del log nel Monitoraggio di Azure  

## <a name="overview"></a>Panoramica

Questo articolo illustra come risolvere gli errori comuni riscontrati durante l'impostazione di avvisi del log in Monitoraggio di Azure. Fornisce inoltre le soluzioni ad alcune domande frequenti relative alla funzionalità o alla configurazione degli avvisi del log. 

Il termine **gli avvisi del Log** descrive gli avvisi che fire basato su una query di log in un [dell'area di lavoro di Log Analitica](../learn/tutorial-viewdata.md) oppure [Application Insights](../../azure-monitor/app/analytics.md). Altre informazioni su funzionalità, terminologia e tipi sono disponibili in [Avvisi del log - Panoramica](../platform/alerts-unified-log.md).

> [!NOTE]
> Questo articolo non prende in considerazione i casi in cui il portale di Azure visualizza una regola di avviso come attivata e una notifica come eseguita tramite gruppi di azioni associati. Per questi casi, fare riferimento ai dettagli nell'articolo sui [gruppi di azioni](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Avviso del log non attivato

Di seguito sono elencati alcuni motivi per cui una [regola di avviso del log configurata in Monitoraggio di Azure](../platform/alerts-log.md) non viene visualizzata [come *attivata* quando previsto](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tempo di inserimento dati per i log

Un avviso del log esegue periodicamente una query basata su [Log Analytics](../learn/tutorial-viewdata.md) o [Application Insights](../../azure-monitor/app/analytics.md). Poiché il monitoraggio di Azure elabora diversi terabyte di dati da migliaia di clienti provenienti da origini diverse in tutto il mondo, il servizio è soggetto a un ritardo di tempo diversi. Per altre informazioni, vedere [ora di inserimento dei dati nei log di monitoraggio di Azure](../platform/data-ingestion-time.md).

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

Analytics che potenzia Log Analytics e Application Insights è soggetto a ritardi di inserimento ed l'elaborazione. Per questo motivo, nel momento in cui viene eseguita la query di avviso del log specificata, potrebbero verificarsi scenari in cui non sono disponibili dati o ne sono disponibili solo alcuni. Per altre informazioni, vedere [ora di inserimento dei dati di Log in Monitoraggio di Azure](../platform/data-ingestion-time.md).

A seconda del modo in cui è configurata la regola di avviso, potrebbero verificarsi attivazioni errate se al momento dell'esecuzione dell'avviso non sono presenti dati nei log o i dati sono solo parziali. In questi casi, è consigliabile modificare la configurazione o la query di avviso. 

Se ad esempio la regola di avviso del log è configurata per l'attivazione quando il numero di risultati di una query Analytics è minore di 5, l'avviso verrà attivato in assenza di dati (zero record) o in presenza di risultati parziali (un record). Tuttavia, dopo il ritardo nell'inserimento dei dati, la stessa query con i dati completi potrebbe fornire un risultato di 10 record.

### <a name="alert-query-output-misunderstood"></a>Output della query di avviso frainteso

La logica per gli avvisi di log viene specificata in una query di Analytics che può usare diverse funzioni matematiche e di Big Data.  Il servizio di generazione degli avvisi esegue la query a determinati intervalli con i dati per il periodo di tempo specificato. Il servizio apporta lievi modifiche alla query in base al tipo di avviso scelto. Questa modifica può essere visualizzata nella sezione "Eseguire una Query per l'esecuzione" in *configura la logica del segnale* schermata, come illustrato di seguito: ![Query da eseguire](media/alert-log-troubleshoot/LogAlertPreview.png)

Nella casella **Query da eseguire** viene visualizzata la query che verrà eseguita dal servizio di generazione degli avvisi di log. È possibile eseguire la query specificata con i dati dell'intervallo di tempo indicato tramite il [portale di Analytics](../log-query/portals.md) o l'[API Analytics](https://docs.microsoft.com/rest/api/loganalytics/) se si vuole conoscere l'output della query di avviso prima che l'avviso venga effettivamente creato.

## <a name="log-alert-was-disabled"></a>Avviso del log è stata disabilitata

Di seguito sono riportati alcuni motivi a causa della quale [regola di avviso di log in Monitoraggio di Azure](../platform/alerts-log.md) potrebbe essere disabilitata in Monitoraggio di Azure.

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>Risorsa di avviso di cui è stato creato non è più presente

Le regole di avviso di log create in Monitoraggio di Azure come destinazione una risorsa specifica, ad esempio un'area di lavoro di Analitica di Log di Azure, app di Azure Application Insights e risorse di Azure. E il servizio di avvisi di log quindi eseguirà query analitica specificata nella regola per la destinazione specificata. Ma dopo la creazione della regola, spesso gli utenti passare alla Elimina da Azure o si sposta all'interno di Azure - la destinazione della regola di avviso. Poiché la destinazione della regola di avviso di log non è più valida, l'esecuzione della regola ha esito negativo.

In questi casi, monitoraggio di Azure verrà disattivata l'avviso del log e verificare che i clienti non vengono fatturati inutilmente quando la regola stessa non è in grado di eseguire continuamente per periodo ridimensionabile, ad esempio una settimana. Gli utenti possono scoprire l'ora esatta in corrispondenza del quale la regola di avviso di log è stata disabilitata da monitoraggio di Azure tramite [Log attività di Azure](../../azure-resource-manager/resource-group-audit.md). Nel Log attività di Azure quando viene disabilitata la regola di avviso di log da Azure, viene aggiunto un evento nel Log attività di Azure.

Un evento di esempio nel Log attività di Azure per la disabilitazione della regola di avviso perché causa dei continui; viene illustrato di seguito.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-log-alert-is-not-valid"></a>Query usata nell'avviso di Log non è valido

Ogni regola di avviso di log creato nel monitoraggio di Azure come parte della configurazione è necessario specificare una query analitica per essere eseguito periodicamente dal servizio di avvisi. Mentre la query analitica potrebbe causare la sintassi corretta al momento della creazione della regola o aggiornamento. Alcune volte in un periodo di tempo, fornire la query nel Registro di regola di avviso può sviluppare problemi di sintassi e causare l'esecuzione della regola a non riuscire. Alcuni dei motivi per cui query analitica fornita in una regola di avviso di log possono sviluppare gli errori sono:

- Query viene scritto [eseguito su più risorse](../log-query/cross-workspace-query.md) e uno o più delle risorse specificate, a questo punto non sono presenti.
- Non si è verificato alcun flusso di dati per la piattaforma analitica, a causa della quale il [esecuzione di query restituisce un errore](https://dev.loganalytics.io/documentation/Using-the-API/Errors) perché non sono presenti dati per la query specificata.
- Modifiche nel [linguaggio di Query](https://docs.microsoft.com/azure/kusto/query/) si sono verificati nelle quali comandi e funzioni hanno un formato rivisto. Di conseguenza la query specificata in precedenza nella regola di avviso non è più valida.

L'utente verrà visualizzato un avviso di questo comportamento prima di tutto tramite [Azure Advisor](../../advisor/advisor-overview.md). È necessario aggiungere una raccomandazione per la regola di avviso di log specifico su Azure Advisor, sotto la categoria della disponibilità elevata con impatto medio e la descrizione come "Ripristina la regola di avviso di log per verificare che il monitoraggio". Se dopo sette giorni della raccomandazione su Azure Advisor fornisce la query di avviso nella regola di avviso di log specificato non viene rettificata. Monitoraggio di Azure verrà quindi disabilitare l'avviso del log e verificare che i clienti non vengono fatturati inutilmente quando la regola stessa non è in grado di eseguire continuamente per periodo ridimensionabile, ad esempio una settimana.

Gli utenti possono scoprire l'ora esatta in corrispondenza del quale la regola di avviso di log è stata disabilitata da monitoraggio di Azure tramite [Log attività di Azure](../../azure-resource-manager/resource-group-audit.md). Nel Log attività di Azure, quando la regola di avviso di log è disabilitata per Azure - viene aggiunto un evento nel Log attività di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli [avvisi del log in Avvisi di Azure](../platform/alerts-unified-log.md)
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md)
- Altre informazioni su [le query di log](../log-query/log-query-overview.md)
