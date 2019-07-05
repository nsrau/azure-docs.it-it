---
title: Azioni webhook per gli avvisi di log in Avvisi di Azure
description: Questo articolo descrive come a una regola di avviso di log di log analitica dell'area di lavoro o application insights, esegue il push dei dati come webhook HTTP e illustra le diverse personalizzazioni possibili.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: cad1b0ab484d172000bd62146a88a27bfab1e9f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448778"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Azioni webhook per le regole di avviso relative ai log
Quando [viene creato un avviso di log in Azure](alerts-log.md), è possibile scegliere di [configurarlo con gruppi di azioni](action-groups.md) in modo da eseguire una o più azioni.  Questo articolo descrive le diverse azioni webhook disponibili e spiega come configurare un webhook personalizzato basato su JSON.

> [!NOTE]
> È anche possibile usare la [common schema avviso](https://aka.ms/commonAlertSchemaDocs), che offre il vantaggio di una singola estendibile e unificato payload avviso tra avviso tutti i servizi in Monitoraggio di Azure, per le integrazioni di webhook. [Scopri le definizioni di avviso dello schema comune.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Azioni webhook

Le azioni Webhook consentono di richiamare un processo esterno tramite una singola richiesta HTTP POST.  Il servizio chiamato deve supportare i webhook e determinare come usare gli eventuali payload che riceve.    

Le azioni webhook includono le proprietà elencate nella tabella seguente:

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| URL webhook |URL del webhook. |
| Payload JSON personalizzato |Payload personalizzato da inviare con il webhook, quando si sceglie questa opzione durante la creazione di avvisi. Per informazioni, vedere [Gestisci gli avvisi del log](alerts-log.md) |

> [!NOTE]
> Il pulsante Visualizzazione del webhook, insieme all'opzione *Includi payload JSON personalizzato per il webhook* per l'avviso di log, visualizzerà il payload del webhook di esempio per la personalizzazione fornita. Non contiene dati effettivi e rappresentativi dello schema JSON usato per gli avvisi dei log. 

I webhook includono un URL e un payload in fermato JSON che corrisponde ai dati inviati al servizio esterno.  Per impostazione predefinita, il payload include i valori riportati nella tabella seguente:  È possibile scegliere di sostituire questo payload con un payload personalizzato.  In questo caso è possibile usare le variabili nella tabella per ognuno dei parametri per includerne il valore nel payload personalizzato.


| Parametro | Variabile | DESCRIZIONE |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nome della regola di avviso. |
| Gravità |#severity |Livello di gravità impostato per l'avviso di log attivato. |
| AlertThresholdOperator |#thresholdoperator |Operatore di soglia per la regola di avviso.  *Maggiore di* o *Minore di*. |
| AlertThresholdValue |#thresholdvalue |Valore di soglia per la regola di avviso. |
| LinkToSearchResults |#linktosearchresults |Collegamento al portale di Analytics che restituisce i record della query che ha creato l'avviso. |
| ResultCount |#searchresultcount |Numero di record nei risultati della ricerca. |
| Ora di fine dell'intervallo di ricerca |#searchintervalendtimeutc |Ora di fine per la query in UTC, formato mm/gg/aaaa HH:mm:ss AM/PM. |
| Intervallo di ricerca |#searchinterval |Intervallo di tempo per la regola di avviso, formato HH:mm:ss. |
| Ora di inizio dell'intervallo di ricerca |#searchintervalstarttimeutc |Ora di inizio per la query in UTC, formato mm/gg/aaaa HH:mm:ss AM/PM. 
| SearchQuery |#searchquery |Query di ricerca nei log usata dalla regola di avviso. |
| SearchResults |"IncludeSearchResults": true|Record restituiti dalla query come tabella JSON, limitati ai primi 1000 record, se il codice "IncludeSearchResults": true viene aggiunto nella definizione personalizzata del webhook JSON come proprietà di primo livello. |
| Tipo di avviso| #alerttype | Il tipo di regola di avviso di log configurato - [misura metrica](alerts-unified-log.md#metric-measurement-alert-rules) oppure [numero di risultati](alerts-unified-log.md#number-of-results-alert-rules).|
| WorkspaceID |#workspaceid |ID dell'area di lavoro Log Analytics. |
| ID applicazione |#applicationid |ID dell'app Application Insights. |
| ID sottoscrizione |#subscriptionid |ID della sottoscrizione di Azure usato. 

> [!NOTE]
> LinkToSearchResults passa i parametri come SearchQuery, Ora di inizio dell'intervallo di ricerca e Ora di fine dell'intervallo di ricerca nell'URL al portale di Azure per visualizzarli nella sezione relativa all'analisi. Portale di Azure include URI limite circa 2.000 caratteri di dimensione e verrà *non* aprire il collegamento fornito nel servizio avvisi, se i valori dei parametri superano il limite di tale. Gli utenti possono immettere manualmente i dettagli per visualizzare i risultati nel portale Analytics o usare l'[API REST di Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) o l'[API REST di Log Analytics](/rest/api/loganalytics/) per recuperare i risultati a livello di codice 

Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*.  Il servizio chiamato da questo webhook si aspetta questo parametro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Il payload di esempio viene risolto in una stringa di simile alla seguente quando viene inviato al webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Poiché tutte le variabili in un webhook personalizzato devono essere specificate all'interno dell'enclosure JSON, ad esempio "#searchinterval", il webhook risultante avrà anche i dati delle variabili all'interno dell'enclosure, ad esempio "00:05:00".

Per includere i risultati della ricerca in un payload personalizzato, verificare che **IncludeSearchResults** sia impostata come proprietà di primo livello nel payload JSON. 

## <a name="sample-payloads"></a>Payload di esempio
Questa sezione mostra un esempio di payload di webhook per gli avvisi di log, facendo distinzione tra payload standard e personalizzato.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard per gli avvisi di log 
In entrambi questi esempi è specificato un payload fittizio con solo due colonne e due righe.

#### <a name="log-alert-for-azure-log-analytics"></a>Avviso di log per Azure Log Analytics
Di seguito è riportato un payload di esempio per un'azione webhook standard *senza l'uso di un'opzione JSON personalizzata* per gli avvisi del log basati su Log Analytics.

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
> Valore del campo gravità potrebbe cambiare se hai [passa la preferenza di API](alerts-log-api-switch.md) per gli avvisi del log in Log Analitica.


#### <a name="log-alert-for-azure-application-insights"></a>Avviso di log per Azure Application Insights
Di seguito è riportato un payload di esempio per un'azione webhook standard *senza opzione JSON personalizzata* se usato per gli avvisi del log basati su Application Insights.
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Avviso di log con payload JSON personalizzato
Per creare un payload personalizzato che includa solo il nome dell'avviso e i risultati della ricerca, è ad esempio possibile usare il codice seguente: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Di seguito è riportato un esempio di payload per un'azione webhook personalizzata per qualsiasi avviso di log.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
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
        }
    }
```


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sugli [avvisi del log in Avvisi di Azure](alerts-unified-log.md)
- Acquisire familiarità con la [gestione degli avvisi di log in Azure](alerts-log.md)
- Creare e gestire [gruppi di azioni in Azure](action-groups.md)
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md)
- Altre informazioni sulle [registrare query](../log-query/log-query-overview.md). 

