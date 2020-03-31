---
title: Risolvere i problemi relativi agli avvisi di log in Monitoraggio di AzureTroubleshoot log alerts in Azure Monitor Documenti Microsoft
description: Problemi comuni, errori e soluzioni per le regole di avviso di log in Azure.Common issues, errors, and resolutions for log alert rules in Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249035"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Risolvere i problemi relativi agli avvisi di log in Monitoraggio di AzureTroubleshoot log alerts in Azure Monitor  

Questo articolo illustra come risolvere i problemi comuni che potrebbero verificarsi quando si configurano gli avvisi di log in Monitoraggio di Azure.This article shows you how to resolve common issues that might happen when you're setting up log alerts in Azure Monitor. Fornisce inoltre soluzioni ai problemi comuni relativi alla funzionalità o alla configurazione degli avvisi di log.

Gli *avvisi di log* di termine descrivono le regole che vengono attivate in base a una query di log in un'area di lavoro di Azure Log Analytics o in Azure Application Insights.The term log alerts describe rules that fire based on a log query in [an Azure Log Analytics workspace](../learn/tutorial-viewdata.md) or in [Azure Application Insights](../../azure-monitor/app/analytics.md). Per altre informazioni sulle funzionalità, la terminologia e i tipi, vedere [Registrare avvisi in Monitoraggio di Azure.Learn](../platform/alerts-unified-log.md)more about functionality, terminology, and types in Log alerts in Azure Monitor .

> [!NOTE]
> Questo articolo non considera i casi in cui il portale di Azure mostra una regola di avviso attivata e una notifica non viene eseguita da un gruppo di azioni associato. In questi casi, vedere i dettagli in Creare e gestire gruppi di azioni nel portale di [Azure.](../platform/action-groups.md)

## <a name="log-alert-didnt-fire"></a>Avviso del log non attivato

Ecco alcuni motivi comuni per cui lo stato di una regola di avviso log configurata [in Monitoraggio di Azure](../platform/alerts-log.md) non viene visualizzato come [ *generato* quando previsto.](../platform/alerts-managing-alert-states.md)

### <a name="data-ingestion-time-for-logs"></a>Tempo di inserimento dei dati per i logData ingestion time for logs

Un avviso di log esegue periodicamente la query in base a [Log Analytics](../learn/tutorial-viewdata.md) o [Application Insights.](../../azure-monitor/app/analytics.md) Poiché Monitor di Azure elabora molti terabyte di dati da migliaia di clienti provenienti da diverse fonti in tutto il mondo, il servizio è suscettibile a ritardi temporali variabili. Per altre informazioni, vedere Tempo di inserimento dati nei log di Monitoraggio di Azure.For more information, see [Data ingestion time in Azure Monitor logs.](../platform/data-ingestion-time.md)

Per ridurre i ritardi, il sistema attende e ritenta la query di avviso più volte se rileva che i dati necessari non sono ancora stati ingeriti. Il sistema ha un tempo di attesa impostato che aumenta in modo esponenziale. L'avviso di log viene attivato solo dopo che i dati sono disponibili, pertanto il ritardo potrebbe essere dovuto a un'inserimento lento dei dati del log.

### <a name="incorrect-time-period-configured"></a>Periodo di tempo errato configurato

Come descritto nell'articolo sulla [terminologia per gli avvisi di log](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), il periodo di tempo indicato nella configurazione specifica l'intervallo di tempo per la query. La query restituisce solo i record creati all'interno di questo intervallo.

Il periodo di tempo limita i dati recuperati per una query di log per evitare abusi e aggira qualsiasi comando di tempo (come **fa)** utilizzato in una query di log. Se ad esempio il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15, solo i record creati tra le 12.15 e le 13.15 vengono usati per la query di log. Se la query di log utilizza un comando time come **fa (1d),** la query utilizza ancora solo dati compresi tra le 12:15 e le 13:15 perché il periodo di tempo è impostato su tale intervallo.

Verificare che il periodo di tempo nella configurazione corrisponda alla query. Per l'esempio illustrato in precedenza, se la query di log utilizza **fa (1d)** con l'indicatore verde, il periodo di tempo deve essere impostato su 24 ore o 1.440 minuti (indicato in rosso). Questa impostazione garantisce che la query venga eseguita come previsto.

![Periodo di tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Opzione Elimina avvisi impostata

Come descritto nel passaggio 8 dell'articolo sulla creazione di una regola di avviso di log nel portale di [Azure,](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)gli avvisi di log forniscono un'opzione **Suppress Alerts** per eliminare le azioni di attivazione e notifica per un periodo di tempo configurato. Di conseguenza, si potrebbe pensare che non sia stato attivato un avviso. Infatti, ha sparato, ma è stato soppresso.  

![Elimina avvisi](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Regola di avviso di unità di misura della metrica errata

Gli avvisi del log di *misurazione delle metriche* sono un sottotipo di avvisi di log con funzionalità speciali e una sintassi di query degli avvisi con restrizioni. Una regola per un avviso del log di misurazione metrica richiede che l'output della query sia una serie temporale metrica. Ovvero, l'output è una tabella con periodi di tempo distinti e di dimensioni uguali insieme ai valori aggregati corrispondenti.

È possibile scegliere di avere variabili aggiuntive nella tabella insieme a **AggregatedValue**. Queste variabili possono essere utilizzate per ordinare la tabella.

Si supponga, ad esempio, che una regola per un avviso del log di misurazione delle metriche sia stata configurata come:For example, suppose a rule for a metric measurement log alert was configured as:

- Query di`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Periodo di tempo di 6 ore
- Soglia di 50
- Logica di avviso di tre violazioni consecutive
- **Aggrega su** scelto come **$table**

Poiché il comando include **riassumere ... e** fornisce due variabili (**timestamp** e **$table**), il sistema sceglie **$table** per **Aggregate Upon**. Il sistema ordina la tabella dei risultati in base al campo **$table,** come illustrato nella schermata seguente. Vengono quindi esaminate più istanze **di AggregatedValue** per ogni tipo di tabella (ad esempio **availabilityResults**) per verificare se sono state presenti tre o più violazioni consecutive.

![Esecuzione di query di misurazione metrica con più valoriMetric measurement query execution with multiple values](media/alert-log-troubleshoot/LogMMQuery.png)

Poiché **Aggregate Upon** è definito **in $table**, i dati vengono ordinati in base a una colonna **$table** (indicata in rosso). Quindi raggruppamo e cerchiamo i tipi di campo **Aggrega a.**

Ad esempio, per **$table**, i valori per **availabilityResults** verranno considerati come un'unica traccia/entità (indicata in arancione). In questo value plot/entity, il servizio di avviso verifica la presenza di tre violazioni consecutive (indicate in verde). Le violazioni attivano un avviso per il valore della tabella **availabilityResults**.

Analogamente, se si verificano tre violazioni consecutive per qualsiasi altro valore di **$table**, viene attivata un'altra notifica di avviso per la stessa cosa. Il servizio di avviso ordina automaticamente i valori in un grafico/entità (indicato in arancione) in base al tempo.

Si supponga ora che la regola per l'avviso `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`del log di misurazione delle metriche sia stata modificata e che la query sia stata modificata. Il resto della configurazione è rimasto lo stesso di prima, inclusa la logica di avviso per tre violazioni consecutive. L'opzione **Aggrega a** questo caso è **timestamp** per impostazione predefinita. Nella query viene fornito un solo valore per **riassumere ... da** (ovvero **timestamp**). Come nell'esempio precedente, l'output alla fine dell'esecuzione sarebbe illustrato di seguito.

   ![Esecuzione di query di misurazione metrica con valore singolareMetric measurement query execution with singular value](media/alert-log-troubleshoot/LogMMtimestamp.png)

Poiché **Aggregate Upon** è definito in **timestamp**, i dati vengono ordinati in base alla colonna **timestamp** (indicata in rosso). Quindi si raggruppa in base **al timestamp**. Ad esempio, `2018-10-17T06:00:00Z` i valori per verranno considerati come un'unica trama/entità (indicata in arancione). In questo value plot/entity, il servizio di avviso non troverà violazioni consecutive (perché ogni valore **timestamp** ha una sola voce). Quindi l'avviso non viene mai attivato. In tal caso, l'utente deve:

- Aggiungere una variabile fittizia o una variabile esistente (ad **esempio $table**) per eseguire correttamente l'ordinamento utilizzando il campo **Aggrega a** .
- Riconfigurare la regola di avviso per utilizzare invece la logica di avviso in base alla **violazione totale.**

## <a name="log-alert-fired-unnecessarily"></a>Avviso del log inutilmente attivato

Una [regola](../platform/alerts-log.md) di avviso del log configurata in Monitoraggio di Azure potrebbe essere attivata in modo imprevisto quando viene visualizzata in Avvisi di Azure.A configured log alert rule in Azure Monitor might be triggered unexpectedly when you view it in Azure [Alerts](../platform/alerts-managing-alert-states.md). Nelle sezioni seguenti vengono descritti alcuni motivi comuni.

### <a name="alert-triggered-by-partial-data"></a>Avviso attivato da dati parziali

Analisi dei log e Application Insights sono soggette a ritardi e processi di inserimento. Quando si esegue una query di avviso del log, è possibile che non vi sia dati disponibili o che solo alcuni dati sono disponibili. Per altre informazioni, vedere Registrare il tempo di inserimento dei dati in Monitoraggio di Azure.For more information, see [Log data ingestion time in Azure Monitor.](../platform/data-ingestion-time.md)

A seconda di come è stata configurata la regola di avviso, potrebbe verificarsi un errore di generazione se non sono presenti dati o dati parziali nei log al momento dell'esecuzione dell'avviso. In questi casi, si consiglia di modificare la query o la configurazione degli avvisi.

Ad esempio, se si configura la regola di avviso del log da attivare quando il numero di risultati da una query di analisi è inferiore a 5, l'avviso viene attivato quando non sono presenti dati (record zero) o risultati parziali (un record). Tuttavia, dopo il ritardo di inserimento dei dati, la stessa query con i dati completi potrebbe fornire un risultato di 10 record.

### <a name="alert-query-output-is-misunderstood"></a>L'output della query di avviso è frainteso

La logica per gli avvisi di log viene specificata in una query di Analytics La query di analisi può utilizzare vari Big Data e funzioni matematiche. Il servizio di avviso esegue la query a intervalli specificati con i dati per un periodo di tempo specificato. Il servizio di avviso apporta piccole modifiche alla query in base al tipo di avviso. È possibile visualizzare questa modifica nella sezione Query da eseguire nella schermata Configura logica del segnale:You can view this change in the **Query to be executed** section on the **Configure signal logic** screen:

![Query da eseguire](media/alert-log-troubleshoot/LogAlertPreview.png)

La casella **Query da eseguire** è ciò che viene eseguito dal servizio di avviso di log. Se si desidera comprendere l'output della query di avviso prima di creare l'avviso, è possibile eseguire la query indicata e l'intervallo di tempo tramite il [portale di Analytics](../log-query/portals.md) o l'API di [analytics.](https://docs.microsoft.com/rest/api/loganalytics/)

## <a name="log-alert-was-disabled"></a>Avviso di registro disabilitato

Nelle sezioni seguenti sono elencati alcuni motivi per cui Monitoraggio azure potrebbe disabilitare la regola di avviso del [log.](../platform/alerts-log.md)

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Risorsa in cui è stato creato l'avviso non esiste più

Le regole di avviso di log create in Monitoraggio di Azure hanno come destinazione una risorsa specifica, ad esempio un'area di lavoro di Azure Log Analytics, un'app di Azure Application Insights e una risorsa di Azure.Log alert rules created in Azure Monitor target a specific resource like an Azure Log Analytics workspace, an Azure Application Insights app, and an Azure resource. Il servizio di avviso del log eseguirà quindi una query di analisi fornita nella regola per la destinazione specificata. Ma dopo la creazione della regola, gli utenti spesso passano all'eliminazione da Azure o si spostano all'interno di Azure, ovvero la destinazione della regola di avviso del log. Poiché la destinazione della regola di avviso non è più valida, l'esecuzione della regola ha esito negativo.

In questi casi, Monitoraggio solo Cost disabilita l'avviso di log e garantisce che non venga fatturato inutilmente quando la regola non può essere eseguita continuamente per un periodo considerevole (ad esempio una settimana). È possibile scoprire l'ora esatta in cui Monitoraggio di Azure ha disabilitato l'avviso di log tramite il [log attività di Azure.You](../../azure-resource-manager/management/view-activity-logs.md)can find out the exact time when Azure Monitor disabled the log alert via Azure Activity Log . In Log attività di Azure viene aggiunto un evento quando Monitoraggio azure disabilita la regola di avviso del log.

L'evento di esempio seguente nel Log attività di Azure è relativo a una regola di avviso disabilitata a causa di un errore continuo.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>La query utilizzata in un avviso di log non è valida

Ogni regola di avviso del log creata in Monitoraggio di Azure come parte della configurazione deve specificare una query di analisi che verrà eseguita periodicamente dal servizio avvisi. La query di analisi potrebbe avere una sintassi corretta al momento della creazione o dell'aggiornamento della regola. Ma a volte, in un periodo di tempo, la query fornita nella regola di avviso del log può sviluppare problemi di sintassi e causare l'esito negativo dell'esecuzione della regola. Alcuni motivi comuni per cui una query di analisi fornita in una regola di avviso del log può sviluppare errori sono:Some common reasons why an analytics query provided in a log alert rule can develop errors are:

- La query viene scritta per [l'esecuzione su più risorse](../log-query/cross-workspace-query.md). E una o più delle risorse specificate non esistono più.
- [L'avviso del log del tipo di misurazione metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) configurato ha una query di avviso non conforme alle norme di sintassi
- Non è stato eseguito alcun flusso di dati alla piattaforma di analisi. L'esecuzione della [query restituisce un errore](https://dev.loganalytics.io/documentation/Using-the-API/Errors) perché non sono presenti dati per la query fornita.
- Le modifiche apportate al [linguaggio](https://docs.microsoft.com/azure/kusto/query/) di query includono un formato rivisto per comandi e funzioni. Pertanto, la query fornita in precedenza in una regola di avviso non è più valida.

[Azure Advisor](../../advisor/advisor-overview.md) avvisa l'utente di questo comportamento. Viene aggiunta una raccomandazione per la regola di avviso del log specifica in Azure Advisor, nella categoria Disponibilità elevata con impatto medio e una descrizione di "Ripristina la regola di avviso del log per garantire il monitoraggio". Se una query di avviso nella regola di avviso del log non viene rettificata dopo che Azure Advisor ha fornito una raccomandazione per sette giorni, Monitoraggio di Azure disabiliterà l'avviso di log e garantisce che non venga fatturato inutilmente quando la regola non può essere eseguita continuamente per un periodo considerevole ( come una settimana).

È possibile trovare l'ora esatta in cui Monitoraggio di Azure ha disabilitato la regola di avviso del log cercando un evento nel Log attività di Azure.You can find the exact time when Azure Monitor disabled the log alert rule by looking for an event in [Azure Activity Log](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, fare riferimento agli [avvisi di log in Azure](../platform/alerts-unified-log.md).
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md).
- Ulteriori informazioni sulle query di [log](../log-query/log-query-overview.md).
