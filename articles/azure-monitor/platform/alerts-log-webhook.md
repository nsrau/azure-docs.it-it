---
title: Azioni webhook per gli avvisi del log in avvisi di Azure
description: Viene descritto come configurare un avviso di log push con azione webhook e personalizzazioni disponibili
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 9a074be9bcc62d8c20635400f462f52fb796d2fe
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012323"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Azioni webhook per le regole di avviso relative ai log

[Log Alert](alerts-log.md) supporta la [configurazione di gruppi di azioni webhook](action-groups.md#webhook). In questo articolo verranno descritte le proprietà disponibili e le modalità di configurazione di un webhook JSON personalizzato.

> [!NOTE]
> Il webhook basato su JSON personalizzato non è attualmente supportato nella versione dell'API `2020-05-01-preview`

> [!NOTE]
> Si consiglia di usare lo [schema di avviso comune](alerts-common-schema.md) per le integrazioni del webhook. Lo schema di avviso comune offre il vantaggio di avere un singolo payload di avviso estendibile e unificato in tutti i servizi Alert di monitoraggio di Azure. Per le regole degli avvisi di log con un payload JSON personalizzato definito, l'abilitazione dello schema comune ripristina lo schema del payload a quello descritto [qui](alerts-common-schema-definitions.md#log-alerts). Gli avvisi con lo schema comune abilitato hanno un limite di dimensione superiore di 256 KB per avviso. l'avviso più grande non includerà i risultati della ricerca. Quando non sono inclusi i risultati della ricerca, è necessario usare `LinkToFilteredSearchResultsAPI` o `LinkToSearchResultsAPI` per accedere ai risultati di query tramite l'API log Analytics.

## <a name="webhook-payload-properties"></a>Proprietà del payload del webhook

Le azioni webhook consentono di richiamare una singola richiesta HTTP POST. Il servizio chiamato deve supportare i webhook e sa come usare il payload che riceve.

Proprietà dell'azione webhook predefinita e i relativi nomi di parametri JSON personalizzati:

| Parametro | Variabile | Descrizione |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nome della regola di avviso. |
| *Gravità* |#severity |Livello di gravità impostato per l'avviso di log attivato. |
| *AlertThresholdOperator* |#thresholdoperator |Operatore di soglia per la regola di avviso. |
| *AlertThresholdValue* |#thresholdvalue |Valore di soglia per la regola di avviso. |
| *LinkToSearchResults* |#linktosearchresults |Collegamento al portale di Analytics che restituisce i record della query che ha creato l'avviso. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Collegamento all'API Analytics che restituisce i record della query che ha creato l'avviso. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Collegamento al portale di Analytics che restituisce i record dalla query filtrata in base alle combinazioni di valori di dimensioni che hanno creato l'avviso. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Collegamento all'API Analytics che restituisce i record dalla query filtrata in base alle combinazioni di valori di dimensioni che hanno creato l'avviso. |
| *ResultCount* |#searchresultcount |Numero di record nei risultati della ricerca. |
| *Ora di fine dell'intervallo di ricerca* |#searchintervalendtimeutc |Ora di fine della query in formato UTC, con formato MM/GG/AAAA HH: mm: ss AM/PM. |
| *Intervallo di ricerca* |#searchinterval |Intervallo di tempo per la regola di avviso, con formato HH: mm: SS. |
| *Ora di inizio dell'intervallo di ricerca* |#searchintervalstarttimeutc |Ora di inizio per la query in formato UTC, con formato MM/GG/AAAA HH: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |Query di ricerca nei log usata dalla regola di avviso. |
| *SearchResults* |"IncludeSearchResults": true|Record restituiti dalla query come tabella JSON, limitati ai primi 1.000 record. "IncludeSearchResults": true viene aggiunto in una definizione di Webhook JSON personalizzata come proprietà di primo livello. |
| *Dimensioni* |"IncludeDimensions": true|Dimensioni combinazioni di valori che hanno attivato l'avviso come sezione JSON. "IncludeDimensions": true viene aggiunto in una definizione di Webhook JSON personalizzata come proprietà di primo livello. |
| *Tipo di avviso*| #alerttype | Tipo di regola di avviso del log configurata come [misurazione metrica o numero di risultati](alerts-unified-log.md#measure).|
| *WorkspaceID* |#workspaceid |ID dell'area di lavoro Log Analytics. |
| *ID applicazione* |#applicationid |ID dell'app Application Insights. |
| *ID sottoscrizione* |#subscriptionid |ID della sottoscrizione di Azure usata. |

## <a name="custom-webhook-payload-definition"></a>Definizione del payload del webhook personalizzato

È possibile usare il **payload JSON personalizzato per il webhook** per ottenere un payload JSON personalizzato usando i parametri precedenti. È anche possibile generare proprietà aggiuntive.
Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*. Il servizio chiamato da questo webhook prevede questo parametro:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Questo payload di esempio viene risolto in un modo simile al seguente quando viene inviato al webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Le variabili in un webhook personalizzato devono essere specificate all'interno di un'enclosure JSON. Ad esempio, se si fa riferimento a "#searchresultcount" nell'esempio precedente, l'output viene restituito in base ai risultati dell'avviso.

Per includere i risultati della ricerca, aggiungere **IncludeSearchResults** come proprietà di primo livello nel file JSON personalizzato. I risultati della ricerca sono inclusi come una struttura JSON, pertanto non è possibile fare riferimento ai risultati in campi definiti personalizzati. 

> [!NOTE]
> Il pulsante **Visualizza webhook** accanto all'opzione **Includi payload JSON personalizzato per il webhook** Visualizza l'anteprima di ciò che è stato fornito. Non contiene dati effettivi, ma è rappresentativo dello schema JSON che verrà usato. 

## <a name="sample-payloads"></a>Payload di esempio
Questa sezione illustra i payload di esempio per i webhook per gli avvisi del log. I payload di esempio includono esempi quando il payload è standard e quando è personalizzato.

### <a name="log-alert-for-log-analytics"></a>Avviso di log per Log Analytics
Il payload di esempio seguente è per un'azione webhook standard usata per gli avvisi basati su Log Analytics:

> [!NOTE]
> Il valore del campo "Severity" viene modificato se è stata [impostata l'API scheduledQueryRules corrente](alerts-log-api-switch.md) dall' [API legacy log Analytics Alert](api-alerts.md).

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Avviso di log per Application Insights
Il payload di esempio seguente è relativo a un webhook standard quando viene usato per gli avvisi di log basati su risorse Application Insights:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Avviso di log per altri log delle risorse (dalla versione dell'API `2020-05-01-preview` )

> [!NOTE]
> Non sono attualmente previsti addebiti aggiuntivi per la versione dell'API `2020-05-01-preview` e gli avvisi del log incentrato sulle risorse.  I prezzi per le funzionalità in anteprima verranno annunciati in futuro e un avviso fornito prima di iniziare la fatturazione. Se si sceglie di continuare a usare la nuova versione dell'API e gli avvisi del log incentrato sulle risorse dopo il periodo di preavviso, l'addebito sarà addebitato alla tariffa applicabile.

Il payload di esempio seguente è relativo a un webhook standard quando viene usato per gli avvisi del log basati su altri log delle risorse (escluse le aree di lavoro e Application Insights):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Avviso di log con un payload JSON personalizzato
Ad esempio, per creare un payload personalizzato che includa solo il nome dell'avviso e i risultati della ricerca, usare questa configurazione: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Il payload di esempio seguente è per un'azione webhook personalizzata per qualsiasi avviso di log:
    
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
- Informazioni sugli [avvisi del log in avvisi di Azure](alerts-unified-log.md).
- Informazioni su come [gestire gli avvisi del log in Azure](alerts-log.md).
- Creare e gestire [gruppi di azioni in Azure](action-groups.md).
- Altre informazioni su [Application Insights](../log-query/log-query-overview.md).
- Altre informazioni sulle [query di log](../log-query/log-query-overview.md). 
