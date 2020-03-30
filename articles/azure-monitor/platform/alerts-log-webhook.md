---
title: Azioni di Webhook per gli avvisi di log negli avvisi di AzureWebhook actions for log alerts in Azure alerts
description: In questo articolo viene descritto come creare una regola di avviso del log usando l'area di lavoro log Analytics o Application Insights, come l'avviso esegue il push dei dati come webhook HTTP e i dettagli delle diverse personalizzazioni possibili.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667704"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Azioni webhook per le regole di avviso relative ai log
Quando [viene creato](alerts-log.md)un avviso di log in Azure , è possibile [configurarlo usando](action-groups.md) i gruppi di azioni per eseguire una o più azioni. Questo articolo descrive le diverse azioni webhook disponibili e mostra come configurare un webhook personalizzato basato su JSON.

> [!NOTE]
> È inoltre possibile utilizzare lo schema di [avviso comune](https://aka.ms/commonAlertSchemaDocs) per le integrazioni webhook. Lo schema degli avvisi comune offre il vantaggio di disporre di un singolo payload di avvisi estensibile e unificato in tutti i servizi di avviso in Monitoraggio di Azure.Si noti che lo schema di avviso comune non rispetta l'opzione JSON personalizzata per gli avvisi di log. Rinvia al payload dello schema di avviso comune se viene selezionato indipendentemente dalla personalizzazione che potrebbe essere stata eseguita a livello di regola di avviso. [Informazioni sulle definizioni dello schema di avviso comuni.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Azioni webhook

Con le azioni webhook, è possibile richiamare un processo esterno tramite una singola richiesta HTTP POST. Il servizio chiamato deve supportare i webhook e determinare come usare qualsiasi payload ricevuto.

Le azioni webhook includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| **URL di webhook** |URL del webhook. |
| **Payload JSON personalizzato** |Payload personalizzato da inviare con il webhook quando questa opzione viene scelta durante la creazione dell'avviso. Per ulteriori informazioni, vedere [Gestire gli avvisi di registro](alerts-log.md).|

> [!NOTE]
> Il pulsante **Visualizza Webhook** accanto all'opzione **Includi payload JSON personalizzato per webhook per** l'avviso di log visualizza il payload webhook di esempio per la personalizzazione fornita. Non contiene dati effettivi, ma è rappresentativo dello schema JSON utilizzato per gli avvisi di log. 

I webhook includono un URL e un payload formattato in JSON che i dati inviati al servizio esterno. Per impostazione predefinita, il payload include i valori riportati nella tabella seguente. È possibile scegliere di sostituire questo payload con un payload personalizzato. In tal caso, usare le variabili nella tabella per ognuno dei parametri per includere i relativi valori nel payload personalizzato.


| Parametro | Variabile | Descrizione |
|:--- |:--- |:--- |
| *AlertRuleName (Nome AlertRule)* |#alertrulename |Nome della regola di avviso. |
| *Gravità* |#severity |Livello di gravità impostato per l'avviso di log attivato. |
| *AlertThresholdOperator* |#thresholdoperator |Operatore di soglia per la regola di avviso, che utilizza maggiore o minore di. |
| *AlertThresholdValue* |#thresholdvalue |Valore di soglia per la regola di avviso. |
| *LinkToSearchRisultati* |#linktosearchresults |Collegamento al portale di Analytics che restituisce i record della query che ha creato l'avviso. |
| *ResultCount* |#searchresultcount |Numero di record nei risultati della ricerca. |
| *Ora di fine dell'intervallo di ricerca* |#searchintervalendtimeutc |Ora di fine della query in formato UTC, con il formato mm/gg/aaaa HH:mm:ss AM/PM. |
| *Intervallo di ricerca* |#searchinterval |Intervallo di tempo per la regola di avviso, con il formato HH:mm:ss. |
| *Ora di inizio dell'intervallo di ricerca* |#searchintervalstarttimeutc |Ora di inizio della query in formato UTC, con il formato mm/gg/aaaa HH:mm:ss AM/PM. 
| *SearchQuery* |#searchquery |Query di ricerca nei log usata dalla regola di avviso. |
| *SearchResults* |"IncludeSearchResults": true|Record restituiti dalla query come tabella JSON, limitata ai primi 1.000 record, se "IncludeSearchResults": true viene aggiunto in una definizione webhook JSON personalizzata come proprietà di primo livello. |
| *Tipo di avviso*| #alerttype | Tipo di regola di avviso del registro configurata come [Misurazione metrica](alerts-unified-log.md#metric-measurement-alert-rules) o [Numero di risultati](alerts-unified-log.md#number-of-results-alert-rules).|
| *ID area di lavoro* |#workspaceid |ID dell'area di lavoro Log Analytics. |
| *ID applicazione* |#applicationid |ID dell'app Application Insights. |
| *ID sottoscrizione* |#subscriptionid |ID della sottoscrizione di Azure usata. 

> [!NOTE]
> *LinkToSearchResults* passa parametri come *SearchQuery*, Ora di inizio *intervallo*di ricerca e Ora di fine *intervallo* di ricerca nell'URL del portale di Azure per la visualizzazione nella sezione Analisi. Il portale di Azure ha un limite di dimensione URI di circa 2.000 caratteri. Il portale *non* aprirà i collegamenti forniti negli avvisi se i valori dei parametri superano il limite. È possibile immettere manualmente i dettagli per visualizzare i risultati nel portale di Analytics. In alternativa, è possibile usare [l'API REST di Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) o l'API REST di Log [Analytics](/rest/api/loganalytics/) per recuperare i risultati a livello di codice. 

Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*. Il servizio chiamato da questo webhook prevede questo parametro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Questo payload di esempio viene risolto in un nome simile al seguente quando viene inviato al webhook:This example payload resolves to something like the following when it's sent to the webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Poiché tutte le variabili in un webhook personalizzato devono essere specificate all'interno di uno chassis JSON, ad esempio "#searchinterval", il webhook risultante dispone anche di dati variabili all'interno di enclosure, ad esempio "00:05:00".

Per includere i risultati della ricerca in un payload personalizzato, assicurati che **IncludeSearchResults** sia impostato come proprietà di primo livello nel payload JSON. 

## <a name="sample-payloads"></a>Payload di esempio
Questa sezione mostra payload di esempio per i webhook per gli avvisi di log. I payload di esempio includono esempi quando il payload è standard e quando è personalizzato.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard per gli avvisi di logStandard webhook for log alerts 
Entrambi questi esempi hanno un payload fittizio con solo due colonne e due righe.

#### <a name="log-alert-for-log-analytics"></a>Log alert for Log Analytics
Il payload di esempio seguente è per un'azione webhook standard *senza un'opzione JSON personalizzata* usata per gli avvisi basati su Log Analytics:The following sample payload is for a standard webhook action without a custom JSON option that's used for alerts based on Log Analytics:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Il valore del campo "Gravità" potrebbe cambiare se hai [cambiato la preferenza API](alerts-log-api-switch.md) per gli avvisi di log in Log Analytics.


#### <a name="log-alert-for-application-insights"></a>Log alert for Application Insights
Il payload di esempio seguente è per un webhook standard *senza un'opzione JSON personalizzata* quando viene usato per registrare avvisi basati su Application Insights:The following sample payload is for a standard webhook without a custom JSON option when it's used for log alerts based on Application Insights:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Avviso di log con payload JSON personalizzatoLog alert with custom JSON payload
Ad esempio, per creare un payload personalizzato che include solo il nome dell'avviso e i risultati della ricerca, è possibile usare quanto segue:For example, to create a custom payload that includes just the alert name and the search results, you can use the following: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Il payload di esempio seguente è per un'azione webhook personalizzata per qualsiasi avviso di log:The following sample payload is for a custom webhook action for any log alert:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sugli avvisi di log negli avvisi di [Azure.](alerts-unified-log.md)
- Informazioni su come [gestire gli avvisi di log in Azure.Understand](alerts-log.md)how to manage log alerts in Azure .
- Creare e gestire [gruppi di azioni in Azure.](action-groups.md)
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md).
- Ulteriori informazioni sulle query di [log](../log-query/log-query-overview.md). 

