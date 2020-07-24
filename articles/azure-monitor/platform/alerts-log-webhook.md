---
title: Azioni webhook per gli avvisi del log in avvisi di Azure
description: Questo articolo descrive come creare una regola di avviso del log usando l'area di lavoro Log Analytics o Application Insights, il modo in cui l'avviso inserisce i dati come un webhook HTTP e i dettagli delle diverse personalizzazioni possibili.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 3311819f021533a28a41daf2c2f08193218fae96
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075278"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Azioni webhook per le regole di avviso relative ai log
Quando [viene creato un avviso del log in Azure](alerts-log.md), è possibile [configurarlo usando i gruppi di azioni](action-groups.md) per eseguire una o più azioni. Questo articolo descrive le diverse azioni webhook disponibili e Mostra come configurare un webhook basato su JSON personalizzato.

> [!NOTE]
> È anche possibile usare lo [schema di avviso comune](https://aka.ms/commonAlertSchemaDocs) per le integrazioni del webhook. Lo schema di avviso comune offre il vantaggio di avere un singolo payload di avvisi estendibile e unificato in tutti i servizi Alert di monitoraggio di Azure. si noti che lo schema di avviso comune non rispetta l'opzione JSON personalizzata per gli avvisi del log. Viene rinviato il payload dello schema di avviso comune se selezionato indipendentemente dalla personalizzazione che è possibile eseguire a livello di regola di avviso. [Informazioni sulle definizioni comuni dello schema di avviso.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Azioni webhook

Con le azioni webhook è possibile richiamare un processo esterno tramite una singola richiesta HTTP POST. Il servizio chiamato deve supportare i webhook e determinare come usare qualsiasi payload che riceve.

Le azioni webhook includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| **URL webhook** |URL del webhook. |
| **Payload JSON personalizzato** |Payload personalizzato da inviare con il webhook quando si sceglie questa opzione durante la creazione dell'avviso. Per altre informazioni, vedere [gestire gli avvisi del log](alerts-log.md).|

> [!NOTE]
> Il pulsante **Visualizza webhook** insieme all'opzione **Includi payload JSON personalizzato per il webhook** per l'avviso di log Visualizza il payload del webhook di esempio per la personalizzazione fornita. Non contiene dati effettivi, ma è rappresentativo dello schema JSON usato per gli avvisi del log. 

I webhook includono un URL e un payload formattato in JSON che i dati sono stati inviati al servizio esterno. Per impostazione predefinita, il payload include i valori riportati nella tabella seguente. È possibile scegliere di sostituire questo payload con un payload personalizzato. In tal caso, usare le variabili nella tabella per ognuno dei parametri per includere i relativi valori nel payload personalizzato.


| Parametro | Variabile | Descrizione |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nome della regola di avviso. |
| *Gravità* |#severity |Livello di gravità impostato per l'avviso di log attivato. |
| *AlertThresholdOperator* |#thresholdoperator |Operatore di soglia per la regola di avviso, che usa maggiore di o minore di. |
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
| *Tipo di avviso*| #alerttype | Tipo di regola di avviso del log configurata come [misurazione metrica](alerts-unified-log.md#metric-measurement-alert-rules) o [numero di risultati](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID dell'area di lavoro Log Analytics. |
| *ID applicazione* |#applicationid |ID dell'app Application Insights. |
| *ID sottoscrizione* |#subscriptionid |ID della sottoscrizione di Azure usata. 

> [!NOTE]
> I collegamenti forniti passano parametri come *SearchQuery*, l' *intervallo di ricerca StartTime*e l' *ora di fine dell'intervallo di ricerca* nell'URL per il portale di Azure o l'API.

Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*. Il servizio chiamato da questo webhook prevede questo parametro.

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
Poiché tutte le variabili in un webhook personalizzato devono essere specificate all'interno di un'enclosure JSON, ad esempio "#searchinterval", il webhook risultante contiene anche dati variabili all'interno di enclosure, ad esempio "00:05:00".

Per includere i risultati della ricerca in un payload personalizzato, assicurarsi che **IncludeSearchResults** sia impostato come proprietà di primo livello nel payload JSON. 

## <a name="sample-payloads"></a>Payload di esempio
Questa sezione illustra i payload di esempio per i webhook per gli avvisi del log. I payload di esempio includono esempi quando il payload è standard e quando è personalizzato.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard per gli avvisi del log 
In entrambi questi esempi è presente un payload fittizio con solo due colonne e due righe.

#### <a name="log-alert-for-log-analytics"></a>Avviso di log per Log Analytics
Il payload di esempio seguente è per un'azione webhook standard *senza un'opzione JSON personalizzata* utilizzata per gli avvisi basati su log Analytics:

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

> [!NOTE]
> Il valore del campo "gravità" potrebbe cambiare se è stata cambiata [la preferenza dell'API per gli](alerts-log-api-switch.md) avvisi del log in log Analytics.


#### <a name="log-alert-for-application-insights"></a>Avviso di log per Application Insights
Il payload di esempio seguente è relativo a un webhook standard *senza un'opzione JSON personalizzata* quando viene usato per gli avvisi di log in base a Application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Avviso di log con payload JSON personalizzato
Ad esempio, per creare un payload personalizzato che includa solo il nome dell'avviso e i risultati della ricerca, è possibile usare quanto segue: 

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
