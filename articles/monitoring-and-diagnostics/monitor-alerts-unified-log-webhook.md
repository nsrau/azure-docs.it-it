---
title: Azioni webhook per gli avvisi di log in Avvisi di Azure (anteprima) | Microsoft Docs
description: Questo articolo descrive come una regola di avviso relativa ai log di Log Analytics o Application Insights esegue il push dei dati come webhook HTTP e illustra le diverse personalizzazioni possibili.
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 49905638-f9f2-427b-8489-a0bcc7d8b9fe
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 4af1bb61888810011ce64fde7931cabfefe76ab6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>Azioni webhook per le regole di avviso relative ai log
Quando [viene creato un avviso in Avvisi (anteprima) di Azure](monitor-alerts-unified-usage.md), è possibile scegliere di [configurarlo con gruppi di azioni](monitoring-action-groups.md) in modo da eseguire una o più azioni.  Questo articolo descrive le diverse azioni webhook disponibili e spiega come configurare un webhook personalizzato basato su JSON.


## <a name="webhook-actions"></a>Azioni webhook

Le azioni webhook consentono di richiamare un processo esterno tramite una singola richiesta HTTP POST.  Il servizio chiamato deve supportare i webhook e determinare come usare gli eventuali payload che riceve.  È anche possibile chiamare un'API REST che non supporta in modo specifico i webhook, purché la richiesta sia in un formato l'API riconosce.  Esempi di utilizzo di un webhook in risposta a un avviso sono l'invio di un messaggio in [Slack](http://slack.com) o la creazione di un evento imprevisto in [PagerDuty](http://pagerduty.com/).  Una procedura dettagliata completa della creazione di una regola di avviso con un webhook per chiamare Slack è disponibile nell'articolo relativo ai [webhook negli avvisi di Log Analytics](../log-analytics/log-analytics-alerts-webhooks.md).

Le azioni webhook includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| URL webhook |URL del webhook. |
| Payload JSON personalizzato |Payload personalizzato da inviare insieme al webhook,  se l'opzione viene selezionata durante la creazione dell'avviso. Per informazioni, vedere [Gestire gli avvisi tramite Avvisi di Azure (anteprima)](monitor-alerts-unified-usage.md). |

> [!NOTE]
> Il pulsante Test del webhook, insieme all'opzione *Includi payload JSON personalizzato per il webhook* per l'avviso di log, attiva una chiamata fittizia per testare l'URL del webhook. Non contiene dati effettivi o rappresentativi dello schema JSON usato per gli avvisi di log. Mentre è possibile testare qualsiasi webhook con JSON personalizzato rappresentativo, tutti i webhook configurati in Gruppo di azioni vengono inviati con payload JSON personalizzato.

I webhook includono un URL e un payload in fermato JSON che corrisponde ai dati inviati al servizio esterno.  Per impostazione predefinita, il payload include i valori riportati nella tabella seguente.  È possibile scegliere di sostituire questo payload con un payload personalizzato.  In questo caso è possibile usare le variabili nella tabella per ognuno dei parametri per includerne il valore nel payload personalizzato.


| Parametro | Variabile | Descrizione |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nome della regola di avviso. |
| AlertThresholdOperator |#thresholdoperator |Operatore di soglia per la regola di avviso.  *Maggiore di* o *Minore di*. |
| AlertThresholdValue |#thresholdvalue |Valore di soglia per la regola di avviso. |
| LinkToSearchResults |#linktosearchresults |Collegamento alla ricerca nei log di Log Analytics che restituisce i record della query che ha creato l'avviso. |
| ResultCount |#searchresultcount |Numero di record nei risultati della ricerca. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |Ora di fine per la query in formato UTC. |
| SearchIntervalInSeconds |#searchinterval |Intervallo di tempo per la regola di avviso. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |Ora di inizio per la query in formato UTC. |
| SearchQuery |#searchquery |Query di ricerca nei log usata dalla regola di avviso. |
| SearchResults |"IncludeSearchResults":true|Record restituiti dalla query come tabella JSON, limitati ai primi 1000 record/righe; se "IncludeSearchResults" è true, viene aggiunta nella definizione personalizzata del webhook JSON come proprietà di primo livello |
| WorkspaceID |#workspaceid |ID dell'area di lavoro di Log Analytics. |
| Gravità |#severity |Livello di gravità impostato per l'avviso di log attivato. |

Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*.  Il servizio chiamato da questo webhook si aspetta questo parametro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Il payload di esempio viene risolto in una stringa di simile alla seguente quando viene inviato al webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Per includere i risultati della ricerca in un payload personalizzato, verificare che **IncudeSearchResults** sia impostata come proprietà di primo livello nel payload JSON.


Per un esempio completo di creazione di una regola di avviso con un webhook per avviare un servizio esterno, vedere l'articolo [Creare un'azione di avviso webhook in Log Analytics di OMS per inviare messaggi a Slack](../log-analytics/log-analytics-alerts-webhooks.md).

## <a name="sample-payload"></a>Esempio di payload
Questa sezione mostra un esempio di payload di webhook per gli avvisi di log, facendo distinzione tra payload standard e personalizzato.

> [!NOTE]
> Per garantire la compatibilità con le versioni precedenti, il payload di webhook standard per gli avvisi generati in base ad Azure Log Analytics corrisponde a quello della [gestione degli avvisi OMS](../log-analytics/log-analytics-solution-alert-management.md). Tuttavia, per gli avvisi di log generati in base ad [Application Insights](../application-insights/app-insights-analytics.md), il payload di webhook standard è basato sullo schema del gruppo di azioni.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard per gli avvisi di log
Per entrambi questi esempi è stato specificato un payload fittizio con solo due colonne e due righe.

#### <a name="log-alert-for-azure-log-analytics"></a>Avviso di log per Azure Log Analytics
Di seguito è riportato un esempio di payload per un'azione webhook standard senza codice JSON personalizzato che viene usato per gli avvisi di log basati su Log Analytics.

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
    "Severity": "Low"
    }



#### <a name="log-alert-for-azure-application-insights"></a>Avviso di log per Azure Application Insights
Di seguito è riportato un esempio di payload per un'azione webhook standard senza codice JSON personalizzato che viene usato per gli avvisi di log basati su Application Insights.


    {
    "schemaId":"LogAlert","data":
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
    "Severity": "Low"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>Avviso di log con payload JSON personalizzato
Ad esempio, per creare un payload personalizzato che include solo il nome dell'avviso e i risultati della ricerca, è possibile usare quanto segue.

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
- Creare e gestire [gruppi di azioni in Azure](monitoring-action-groups.md)
- Informazioni sugli [avvisi di log in Avvisi di Azure (anteprima)](monitor-alerts-unified-log.md)
