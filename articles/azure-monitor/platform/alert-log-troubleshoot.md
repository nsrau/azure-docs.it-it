---
title: Risolvere gli avvisi del log in monitoraggio di Azure | Microsoft Docs
description: Problemi comuni, errori e soluzioni per le regole di avviso del log in Azure.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 794f4ad5bba46af53280d35b55b762b9eef8e1a1
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675243"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Risolvere gli avvisi del log in monitoraggio di Azure  

Questo articolo illustra come risolvere i problemi comuni che possono verificarsi quando si configurano gli avvisi del log in monitoraggio di Azure. Fornisce anche soluzioni ai problemi comuni relativi alla funzionalità o alla configurazione degli avvisi del log. 

Il termine *avvisi del log* descrive le regole che vengono attivate in base a una query di log in un' [area di lavoro di Azure Log Analytics](../learn/tutorial-viewdata.md) o in [applicazione Azure Insights](../../azure-monitor/app/analytics.md). Altre informazioni su funzionalità, terminologia e tipi negli [avvisi di log in monitoraggio di Azure](../platform/alerts-unified-log.md).

> [!NOTE]
> Questo articolo non considera i casi in cui il portale di Azure Mostra una regola di avviso attivata e una notifica non viene eseguita da un gruppo di azione associato. Per questi casi, vedere i dettagli in [creare e gestire gruppi di azioni nella portale di Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Avviso del log non attivato

Ecco alcuni motivi comuni per cui lo stato di una regola di avviso del log configurata [in monitoraggio di Azure](../platform/alerts-log.md) non viene visualizzato [come *generato* quando previsto](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tempo di inserimento dati per i log

Un avviso del log esegue periodicamente la query in base a [log Analytics](../learn/tutorial-viewdata.md) o [Application Insights](../../azure-monitor/app/analytics.md). Poiché monitoraggio di Azure elabora molti terabyte di dati provenienti da migliaia di clienti provenienti da diverse origini in tutto il mondo, il servizio è soggetto a diversi ritardi temporali. Per altre informazioni, vedere [tempo di inserimento dati nei log di monitoraggio di Azure](../platform/data-ingestion-time.md).

Per attenuare i ritardi, il sistema attende e tenta più volte la query di avviso se rileva che i dati necessari non sono stati ancora inseriti. Il sistema ha un tempo di attesa impostato che aumenta in modo esponenziale. L'avviso del log viene attivato solo dopo che i dati sono disponibili, quindi il ritardo potrebbe essere dovuto all'inserimento lento dei dati di log. 

### <a name="incorrect-time-period-configured"></a>Periodo di tempo errato configurato

Come descritto nell'articolo relativo alla [terminologia per gli avvisi del log](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), il periodo di tempo indicato nella configurazione specifica l'intervallo di tempo per la query. La query restituisce solo i record creati in questo intervallo. 

Il periodo di tempo limita i dati recuperati per una query di log, in modo da evitare abusi e aggirare qualsiasi comando del tempo (ad esempio, **fa**) usato in una query di log. Se ad esempio il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15, solo i record creati tra le 12.15 e le 13.15 vengono usati per la query di log. Se la query di log usa un comando time come **fa (1D)** , la query usa comunque solo i dati compresi tra il 12:15 e il 1:15 PM perché il periodo di tempo è impostato su tale intervallo.

Verificare che il periodo di tempo nella configurazione corrisponda alla query. Per l'esempio illustrato in precedenza, se la query di log USA **ago (1D)** con il marcatore verde, il periodo di tempo deve essere impostato su 24 ore o 1.440 minuti (indicato in rosso). Questa impostazione garantisce che la query venga eseguita come previsto.

![Periodo di tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Opzione Elimina avvisi impostata

Come descritto nel passaggio 8 dell'articolo sulla [creazione di una regola di avviso del log nel portale di Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), gli avvisi del log forniscono un'opzione di **eliminazione degli avvisi** per eliminare le azioni di attivazione e notifica per un periodo di tempo configurato. Di conseguenza, si potrebbe pensare che un avviso non è stato attivato. Infatti, è stato attivato ma è stato eliminato.  

![Elimina avvisi](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Regola di avviso di unità di misura della metrica errata

Gli *avvisi del log di misurazione delle metriche* sono un sottotipo di avvisi di log con funzionalità speciali e una sintassi di query di avviso limitata. Una regola per un avviso del log di misurazione delle metriche richiede che l'output della query sia una serie temporale metrica. Ovvero, l'output è una tabella con periodi di tempo distinti e di dimensioni uguali insieme ai valori aggregati corrispondenti. 

È possibile scegliere di disporre di variabili aggiuntive nella tabella insieme a **AggregatedValue**. Queste variabili possono essere utilizzate per ordinare la tabella. 

Si supponga ad esempio che una regola per un avviso del log di misurazione delle metriche sia stata configurata come segue:

- Query di `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Periodo di tempo di 6 ore
- Soglia di 50
- Logica di avviso di tre violazioni consecutive
- **Aggregazione su** scelta come **$Table**

Poiché il comando include il **Riepilogo... in** e sono disponibili due variabili (**timestamp** e **$Table**), il sistema sceglie **$Table** per l' **aggregazione in base**a. Il sistema ordina la tabella dei risultati in base al campo **$Table** , come illustrato nello screenshot seguente. Esamina quindi più istanze di **AggregatedValue** per ogni tipo di tabella (ad esempio **availabilityResults**) per verificare se sono presenti tre o più violazioni consecutive.

![Esecuzione di query di misurazione delle metriche con più valori](media/alert-log-troubleshoot/LogMMQuery.png)

Poiché **Aggregate** su viene definito in **$Table**, i dati vengono ordinati in base a una colonna **$Table** (indicata in rosso). Quindi si raggruppano e si cercano i tipi di **aggregazione sul** campo. 

Per **$Table**, ad esempio, i valori per **availabilityResults** verranno considerati come un singolo tracciato o entità (indicato in arancione). In questo valore Plot/Entity il servizio Alert controlla la presenza di tre violazioni consecutive (indicate in verde). Le violazioni attivano un avviso per il valore della tabella **availabilityResults**. 

Analogamente, se si verificano tre violazioni consecutive per qualsiasi altro valore di **$Table**, viene attivata un'altra notifica di avviso per la stessa operazione. Il servizio Alert Ordina automaticamente i valori in un tracciato/entità (indicati in arancione) in base all'ora.

Si supponga ora che la regola per l'avviso del log di misurazione delle metriche sia stata modificata e che la query sia stata `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Il resto della configurazione è rimasto invariato, inclusa la logica di avviso per tre violazioni consecutive. Per impostazione predefinita, l'opzione **aggrega su** in questo caso è **timestamp** . Nella query per il riepilogo è disponibile un solo valore **... da** (ovvero **timestamp**). Come nell'esempio precedente, l'output alla fine dell'esecuzione è come illustrato di seguito.

   ![Esecuzione di query di misurazione della metrica con valore singolare](media/alert-log-troubleshoot/LogMMtimestamp.png)

Poiché **Aggregate** su è definito in **timestamp**, i dati vengono ordinati in base alla colonna **timestamp** (indicata in rosso). Quindi, viene raggruppato in base al **timestamp**. Ad esempio, i valori per `2018-10-17T06:00:00Z` verranno considerati come un singolo tracciato/entità (indicato in arancione). In questo valore Plot/Entity il servizio Alert non troverà alcuna violazione consecutiva (poiché ogni valore di **timestamp** ha una sola voce). Quindi, l'avviso non viene mai attivato. In tal caso, l'utente deve:

- Aggiungere una variabile fittizia o una variabile esistente (ad esempio **$Table**) per eseguire correttamente l'ordinamento usando il campo **aggregato su** .
- Riconfigurare la regola di avviso in modo da usare la logica di avviso in base alla **violazione totale** .

## <a name="log-alert-fired-unnecessarily"></a>Avviso del log inutilmente attivato

Una [regola di avviso del log configurata in monitoraggio di Azure](../platform/alerts-log.md) può essere attivata in modo imprevisto quando la si visualizza negli [avvisi di Azure](../platform/alerts-managing-alert-states.md). Le sezioni seguenti descrivono alcuni motivi comuni.

### <a name="alert-triggered-by-partial-data"></a>Avviso attivato da dati parziali

Log Analytics e Application Insights sono soggetti a ritardi di inserimento ed elaborazione. Quando si esegue una query di avviso del log, è possibile che non siano disponibili dati o che siano disponibili solo alcuni dati. Per altre informazioni, vedere [tempo di inserimento dei dati di log in monitoraggio di Azure](../platform/data-ingestion-time.md).

A seconda di come è stata configurata la regola di avviso, è possibile che si verifichi una mancata attivazione se non sono presenti dati o dati parziali nei log al momento dell'esecuzione dell'avviso. In questi casi, è consigliabile modificare la query o la configurazione dell'avviso. 

Ad esempio, se si configura la regola di avviso del log in modo che venga attivata quando il numero di risultati di una query di Analytics è inferiore a 5, l'avviso viene attivato quando non sono presenti dati (zero record) o risultati parziali (un record). Tuttavia, dopo il ritardo di inserimento dei dati, è possibile che la stessa query con dati completi fornisca un risultato di 10 record.

### <a name="alert-query-output-is-misunderstood"></a>L'output della query di avviso non è compreso

La logica per gli avvisi di log viene specificata in una query di Analytics La query di analisi può usare varie funzioni matematiche e Big Data. Il servizio Alert esegue la query a intervalli specificati con i dati per un periodo di tempo specificato. Il servizio avvisi apporta modifiche minime alla query in base al tipo di avviso. È possibile visualizzare questa modifica nella sezione **query da** eseguire nella schermata **Configura logica segnale** :

![Query da eseguire](media/alert-log-troubleshoot/LogAlertPreview.png)

La **query da eseguire** box è il servizio di avviso del log eseguito. Per comprendere l'output della query di avviso prima di creare l'avviso, è possibile eseguire la query dichiarata e l'intervallo di tempo tramite il [portale di analisi](../log-query/portals.md) o l' [API di analisi](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Avviso del log disabilitato

Le sezioni seguenti elencano alcuni motivi per cui monitoraggio di Azure potrebbe disabilitare la [regola di avviso del log](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>La risorsa in cui è stato creato l'avviso non esiste più

Le regole di avviso del log create in monitoraggio di Azure sono destinate a una risorsa specifica, ad esempio un'area di lavoro di Azure Log Analytics, un'app applicazione Azure Insights e una risorsa di Azure. Il servizio di avviso del log eseguirà quindi una query di analisi fornita nella regola per la destinazione specificata. Tuttavia, dopo la creazione della regola, gli utenti passano spesso ad eliminare da Azure o a spostarsi all'interno di Azure, ovvero la destinazione della regola di avviso del log. Poiché la destinazione della regola di avviso non è più valida, l'esecuzione della regola ha esito negativo.

In questi casi, monitoraggio di Azure Disabilita l'avviso del log e garantisce che non venga addebitato inutilmente quando la regola non può essere eseguita continuamente per un periodo di tempo considerevole (ad esempio una settimana). È possibile individuare l'ora esatta in cui monitoraggio di Azure ha disabilitato l'avviso di log tramite il [log attività di Azure](../../azure-resource-manager/resource-group-audit.md). Nel log attività di Azure viene aggiunto un evento quando monitoraggio di Azure Disabilita la regola di avviso del log.

L'evento di esempio seguente nel log attività di Azure è per una regola di avviso che è stata disabilitata a causa di un errore continuo.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>La query utilizzata in un avviso del log non è valida

Ogni regola di avviso del log creata in monitoraggio di Azure come parte della relativa configurazione deve specificare una query di analisi che il servizio avvisi sarà eseguito periodicamente. La query di analisi potrebbe avere la sintassi corretta al momento della creazione o dell'aggiornamento della regola. In alcuni casi, tuttavia, in un periodo di tempo, la query fornita nella regola di avviso del log può sviluppare problemi di sintassi e causare un errore di esecuzione della regola. Ecco alcuni motivi comuni per cui una query di analisi fornita in una regola di avviso del log può sviluppare errori:

- La query viene scritta per l' [esecuzione in più risorse](../log-query/cross-workspace-query.md). E una o più delle risorse specificate non esistono più.
- L' [avviso di log del tipo di misurazione della metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) configurato ha una query di avviso non conforme alle normative della sintassi
- Non è stato disponibile alcun flusso di dati alla piattaforma di analisi. L' [esecuzione della query restituisce un errore](https://dev.loganalytics.io/documentation/Using-the-API/Errors) perché non sono disponibili dati per la query specificata.
- Le modifiche nel [linguaggio di query](https://docs.microsoft.com/azure/kusto/query/) includono un formato modificato per i comandi e le funzioni. Quindi, la query fornita in precedenza in una regola di avviso non è più valida.

[Azure Advisor](../../advisor/advisor-overview.md) segnala questo comportamento. Viene aggiunta una raccomandazione per la regola di avviso del log specifica in Azure Advisor, sotto la categoria di disponibilità elevata con effetto medio e una descrizione della regola di avviso "Ripristina la regola di avviso del log per garantire il monitoraggio". Se una query di avviso nella regola di avviso del log non viene rettificata dopo che Azure Advisor ha fornito una raccomandazione per sette giorni, monitoraggio di Azure Disabilita l'avviso del log e verifica che non venga addebitato inutilmente quando la regola non può essere eseguita continuamente per un periodo di tempo considerevole ( come una settimana).

È possibile trovare l'ora esatta in cui monitoraggio di Azure ha disattivato la regola di avviso del log cercando un evento nel [log attività di Azure](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, fare riferimento agli [avvisi di log in Azure](../platform/alerts-unified-log.md).
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md).
- Altre informazioni sulle [query di log](../log-query/log-query-overview.md).
