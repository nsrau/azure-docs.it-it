---
title: Azioni Webhook per gli avvisi del log in avvisi di Azure
description: Questo articolo descrive come creare una regola di avviso di log usando l'area di lavoro di Log Analitica o Application Insights, come l'avviso esegue il push dei dati come un webhook HTTP e i dettagli le diverse personalizzazioni possibili.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705181"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Azioni webhook per le regole di avviso relative ai log
Quando un [avviso di log viene creato in Azure](alerts-log.md), è possibile scegliere di [configurazione mediante gruppi di azioni](action-groups.md) per eseguire una o più azioni. Questo articolo descrive le diverse azioni webhook disponibili e illustra come configurare un webhook personalizzato basato su JSON.

> [!NOTE]
> È anche possibile usare la [common schema avviso](https://aka.ms/commonAlertSchemaDocs) per le integrazioni di webhook. Lo schema comune degli avvisi fornisce il vantaggio di un singolo payload avviso estendibile e unificato in tutti i servizi degli avvisi in Monitoraggio di Azure. [Scopri le definizioni di avviso dello schema comune.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Azioni webhook

Con le azioni webhook, è possibile richiamare un processo esterno tramite una singola richiesta HTTP POST. Il servizio chiamato deve supportare i webhook e determinare come usare gli eventuali payload che riceve.

Le azioni webhook includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| **URL di webhook** |URL del webhook. |
| **Payload JSON personalizzato** |Il payload personalizzato da inviare insieme al webhook quando si sceglie questa opzione durante la creazione dell'avviso. Per altre informazioni, vedere [gli avvisi del log Gestisci](alerts-log.md).|

> [!NOTE]
> Il **vista Webhook** pulsante insieme il **Includi payload JSON personalizzato per il webhook** opzione per l'avviso del log viene visualizzato il payload di webhook di esempio per la personalizzazione che è stato specificato. Non contiene dati effettivi, ma è rappresentativo dello schema JSON che viene usato per gli avvisi del log. 

I Webhook includono un URL e un payload in fermato JSON che i dati inviati al servizio esterno. Per impostazione predefinita, il payload include i valori riportati nella tabella seguente. È possibile scegliere di sostituire questo payload con un payload personalizzato. In tal caso, usare le variabili nella tabella per ognuno dei parametri da includere i valori nel payload personalizzato.


| Parametro | Variabile | DESCRIZIONE |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nome della regola di avviso. |
| *Severity* |#severity |Livello di gravità impostato per l'avviso di log attivato. |
| *AlertThresholdOperator* |#thresholdoperator |Operatore di soglia per la regola di avviso, che usa maggiore o minore rispetto a. |
| *AlertThresholdValue* |#thresholdvalue |Valore di soglia per la regola di avviso. |
| *LinkToSearchResults* |#linktosearchresults |Collegamento al portale di Analitica che restituisce i record della query che ha creato l'avviso. |
| *ResultCount* |#searchresultcount |Numero di record nei risultati della ricerca. |
| *Ora di fine intervallo di ricerca* |#searchintervalendtimeutc |Fine ora per la query in formato UTC, con il formato mm/gg/aaaa hh: mm: ss AM/PM. |
| *Intervallo di ricerca* |#searchinterval |Intervallo di tempo per la regola di avviso, con il formato hh: mm:. |
| *Ora di inizio intervallo ricerca* |#searchintervalstarttimeutc |Ora di inizio per la query in formato UTC, con il formato mm/gg/aaaa hh: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |Query di ricerca nei log usata dalla regola di avviso. |
| *SearchResults* |"IncludeSearchResults": true|Record restituiti dalla query come tabella JSON, limitati ai primi 1000 record, se "IncludeSearchResults": true viene aggiunto in una definizione personalizzata del webhook JSON come proprietà di primo livello. |
| *Tipo di avviso*| #alerttype | Il tipo di regola di avviso di log configurato come [misurazione della metrica](alerts-unified-log.md#metric-measurement-alert-rules) oppure [numero di risultati](alerts-unified-log.md#number-of-results-alert-rules).|
| *WorkspaceID* |#workspaceid |ID dell'area di lavoro Log Analytics. |
| *ID dell'applicazione* |#applicationid |ID della finestra di Application Insights app. |
| *ID sottoscrizione* |#subscriptionid |ID della sottoscrizione di Azure usata. 

> [!NOTE]
> *LinkToSearchResults* passa i parametri, ad esempio *SearchQuery*, *StartTime intervallo ricerca*, e *ora di fine intervallo ricerca* nell'URL di Azure portale per la visualizzazione nella sezione Analitica. Il portale di Azure ha un limite di dimensioni URI di circa 2.000 caratteri. Il portale verrà *non* aprire collegamenti forniti negli avvisi se i valori dei parametri superano il limite. È possibile specificare manualmente i dettagli per visualizzare i risultati nel portale di Analitica. In alternativa, è possibile usare il [API REST di Application Insights Analitica](https://dev.applicationinsights.io/documentation/Using-the-API) o nella [API REST di Log Analitica](/rest/api/loganalytics/) per recuperare i risultati a livello di codice. 

Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*. Il servizio chiamato da questo webhook si aspetta questo parametro.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Il payload di esempio viene risolto in qualcosa simile al seguente quando viene inviato al webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Perché tutte le variabili in un webhook personalizzato devono essere specificate all'interno di una enclosure JSON, ad esempio "#searchinterval", il webhook risultante anche Usa i dati delle variabili all'interno di allegati, ad esempio "00: 05:00."

Per includere i risultati della ricerca in un payload personalizzato, verificare che **IncludeSearchResults** viene impostato come proprietà di primo livello nel payload JSON. 

## <a name="sample-payloads"></a>Payload di esempio
Questa sezione illustra i payload di esempio per i webhook per gli avvisi del log. I payload di esempio includono gli esempi di quando il payload è standard e quando è personalizzata.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard per gli avvisi del log 
Entrambi questi esempi dispone di un payload fittizio con solo due colonne e due righe.

#### <a name="log-alert-for-log-analytics"></a>Avviso del log per Log Analitica
Il payload di esempio seguente è per un'azione webhook standard *senza un'opzione JSON personalizzata* che viene usato per avvisi basati sui Log Analitica:

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
> Il valore del campo "Gravità" potrebbe cambiare se hai [passa la preferenza di API](alerts-log-api-switch.md) per gli avvisi del log in Log Analitica.


#### <a name="log-alert-for-application-insights"></a>Avviso del log per Application Insights
Il payload di esempio seguente è per un'azione webhook standard *senza un'opzione JSON personalizzata* quando viene usato per gli avvisi del log basati su Application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>Avviso del log con payload JSON personalizzato
Ad esempio, per creare un payload personalizzato che include solo il nome dell'avviso e i risultati della ricerca, è possibile usare quanto segue: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Il payload di esempio seguente è per un'azione webhook personalizzati per qualsiasi avviso di log:
    
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
- Scopri [gli avvisi del log in avvisi di Azure](alerts-unified-log.md).
- Comprendere come [gestire gli avvisi del log in Azure](alerts-log.md).
- Creare e gestire [gruppi di azioni in Azure](action-groups.md).
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md).
- Altre informazioni sulle [registrare query](../log-query/log-query-overview.md). 

