---
title: Azioni webhook per gli avvisi di log in Avvisi di Azure (anteprima) | Microsoft Docs
description: Questo articolo descrive come una regola di avviso relativa ai log di Log Analytics o Application Insights esegue il push dei dati come webhook HTTP e illustra le diverse personalizzazioni possibili.
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/2/2018
ms.author: vinagara
ms.openlocfilehash: ee7bdf03c96e078d0d64eeaaffc38ff61596d837
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Azioni webhook per le regole di avviso relative ai log
Quando [viene creato un avviso in Avvisi (anteprima) di Azure](monitor-alerts-unified-usage.md), è possibile scegliere di [configurarlo con gruppi di azioni](monitoring-action-groups.md) in modo da eseguire una o più azioni.  Questo articolo descrive le diverse azioni webhook disponibili e spiega come configurare un webhook personalizzato basato su JSON.


## <a name="webhook-actions"></a>Azioni webhook

Le azioni Webhook consentono di richiamare un processo esterno tramite una singola richiesta HTTP POST.  Il servizio chiamato deve supportare i webhook e determinare come usare gli eventuali payload che riceve.   Esempi di utilizzo di un webhook in risposta a un avviso sono l'invio di un messaggio in [Slack](http://slack.com) o la creazione di un evento imprevisto in [PagerDuty](http://pagerduty.com/).  

Le azioni webhook includono le proprietà elencate nella tabella seguente:

| Proprietà | DESCRIZIONE |
|:--- |:--- |
| URL webhook |URL del webhook. |
| Payload JSON personalizzato |Payload personalizzato da inviare con il webhook, quando si sceglie questa opzione durante la creazione di avvisi. Per informazioni, vedere [Gestire gli avvisi tramite Avvisi di Azure (anteprima)](monitor-alerts-unified-usage.md). |

> [!NOTE]
> Il pulsante Test del webhook, insieme all'opzione *Includi payload JSON personalizzato per il webhook* per l'avviso di log, attiva una chiamata fittizia per testare l'URL del webhook. Non contiene dati effettivi e rappresentativi dello schema JSON usato per gli avvisi dei log. 

I webhook includono un URL e un payload in fermato JSON che corrisponde ai dati inviati al servizio esterno.  Per impostazione predefinita, il payload include i valori presenti nella tabella seguente: È possibile scegliere di sostituire il payload con un payload personalizzato.  In questo caso è possibile usare le variabili nella tabella per ognuno dei parametri per includerne il valore nel payload personalizzato.


| Parametro | Variabile | DESCRIZIONE |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nome della regola di avviso. |
| Gravità |#severity |Livello di gravità impostato per l'avviso di log attivato. |
| AlertThresholdOperator |#thresholdoperator |Operatore di soglia per la regola di avviso.  *Maggiore di* o *Minore di*. |
| AlertThresholdValue |#thresholdvalue |Valore di soglia per la regola di avviso. |
| LinkToSearchResults |#linktosearchresults |Collegamento alla ricerca nei log di Log Analytics che restituisce i record della query che ha creato l'avviso. |
| ResultCount |#searchresultcount |Numero di record nei risultati della ricerca. |
| Ora di fine dell'intervallo di ricerca |#searchintervalendtimeutc |Ora di fine per la query in formato UTC. |
| Intervallo di ricerca |#searchinterval |Intervallo di tempo per la regola di avviso. |
| Ora di inizio dell'intervallo di ricerca |#searchintervalstarttimeutc |Ora di inizio per la query in formato UTC. 
| SearchQuery |#searchquery |Query di ricerca nei log usata dalla regola di avviso. |
| SearchResults |"IncludeSearchResults": true|Record restituiti dalla query come tabella JSON, limitati ai primi 1000 record, se il codice "IncludeSearchResults": true viene aggiunto nella definizione personalizzata del webhook JSON come proprietà di primo livello. |
| WorkspaceID |#workspaceid |ID dell'area di lavoro di Log Analytics (OMS). |
| ID applicazione |#applicationid |ID dell'app Application Insights. |
| ID sottoscrizione |#subscriptionid |ID della sottoscrizione di Azure usata con Application Insights. 


Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*.  Il servizio chiamato da questo webhook si aspetta questo parametro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Il payload di esempio viene risolto in una stringa di simile alla seguente quando viene inviato al webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Per includere i risultati della ricerca in un payload personalizzato, verificare che **IncudeSearchResults** sia impostata come proprietà di primo livello nel payload JSON. 

## <a name="sample-payloads"></a>Payload di esempio
Questa sezione mostra un esempio di payload di webhook per gli avvisi di log, facendo distinzione tra payload standard e personalizzato.

> [!NOTE]
> Per garantire la compatibilità con le versioni precedenti, il payload di webhook standard per gli avvisi generati in base ad Azure Log Analytics corrisponde a quello della [gestione degli avvisi OMS](../log-analytics/log-analytics-alerts-creating.md). Per gli avvisi del log generati tramite [Application Insights](../application-insights/app-insights-analytics.md), tuttavia, il payload di webhook standard si basa sullo schema del gruppo di azioni.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard per gli avvisi di log 
In entrambi questi esempi è specificato un payload fittizio con solo due colonne e due righe.

#### <a name="log-alert-for-azure-log-analytics"></a>Avviso di log per Azure Log Analytics
Di seguito è riportato un payload di esempio per un'azione webhook standard *senza l'uso di un'opzione JSON personalizzata* per gli avvisi del log basati su Log Analytics.

    {
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
    }
    


#### <a name="log-alert-for-azure-application-insights"></a>Avviso di log per Azure Application Insights
Di seguito è riportato un payload di esempio per un'azione webhook standard *senza opzione JSON personalizzata* se usato per gli avvisi del log basati su Application Insights.
    

    {
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error"
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>Avviso di log con payload JSON personalizzato
Per creare un payload personalizzato che includa solo il nome dell'avviso e i risultati della ricerca, è ad esempio possibile usare il codice seguente: 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

Di seguito è riportato un esempio di payload per un'azione webhook personalizzata per qualsiasi avviso di log.
    

    {
    "AlertRuleName":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
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




## <a name="next-steps"></a>Passaggi successivi
- Informazioni sugli [avvisi di log in Avvisi di Azure (anteprima)](monitor-alerts-unified-log.md)
- Creare e gestire [gruppi di azioni in Azure](monitoring-action-groups.md)
- Altre informazioni su [Application Insights](../application-insights/app-insights-analytics.md)
- Altre informazioni su [Log Analytics](../log-analytics/log-analytics-overview.md). 