---
title: Risposte agli avvisi in Log Analytics di OMS | Documentazione Microsoft
description: Gli avvisi in Log Analytics identificano le informazioni importanti nel repository OMS e possono notificare i problemi all&quot;utente in modo proattivo o richiamare le azioni per tentare di correggerle.  Questo articolo descrive come creare una regola di avviso e include i dettagli relativi alle diverse azioni che possono attivare.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fdf22ff85a3a76be5de50632c4948df44c2312df
ms.openlocfilehash: b8731e1fe48b7d809b113eb5273e3962542b8f34
ms.lasthandoff: 03/01/2017


---

# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Aggiungere azioni alle regole di avviso in Log Analytics
Quando [viene creato un avviso in Log Analytics](log-analytics-alerts.md), è possibile scegliere di [configurare la regola di avviso](log-analytics-alerts.md) per eseguire una o più azioni.  Questo articolo descrive le diverse azioni disponibili e offre informazioni sulla configurazione di ogni tipologia di azione.

| Azione | Descrizione |
|:--|:--|
| [Indirizzo di posta elettronica](#email-actions) |    Inviare un messaggio di posta elettronica con i dettagli dell'avviso a uno o più destinatari. |
| [Webhook](#webhook-actions) | Richiamare un processo esterno tramite una singola richiesta HTTP POST. |
| [Runbook](#runbook-actions) | Avviare un runbook in Automazione di Azure. |


## <a name="email-actions"></a>Azioni di posta elettronica
Le azioni di posta elettronica inviano un messaggio di posta elettronica con i dettagli dell'avviso a uno o più destinatari.  È possibile specificare l'oggetto del messaggio, ma il contenuto è un formato standard creato da Log Analytics.  Include informazioni di riepilogo, come il nome dell'avviso, oltre ai dettagli di un massimo di dieci record restituiti dalla ricerca nei log.  Include anche un collegamento a una ricerca nei log in Log Analytics che restituirà l'intero set di record dalla query.   Il mittente del messaggio è *Team di Microsoft Operations Management Suite &lt;noreply@oms.microsoft.com&gt;*. 

Le azioni di posta elettronica includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| Oggetto |Oggetto nel messaggio di posta elettronica.  È possibile modificare il corpo del messaggio. |
| Destinatari |Indirizzi di tutti i destinatari di posta elettronica.  Se si specifica più di un indirizzo, separare ognuno con un punto e virgola (;). |


## <a name="webhook-actions"></a>Azioni webhook

Le azioni Webhook consentono di richiamare un processo esterno tramite una singola richiesta HTTP POST.  Il servizio chiamato deve supportare i webhook e determinare come usare gli eventuali payload che riceve.  È anche possibile chiamare un'API REST che non supporta in modo specifico i webhook, purché la richiesta sia in un formato l'API riconosce.  Esempi di utilizzo di un webhook in risposta a un avviso sono l'invio di un messaggio in [Slack](http://slack.com) o la creazione di un evento imprevisto in [PagerDuty](http://pagerduty.com/).  Una procedura dettagliata completa della creazione di una regola di avviso con un webhook per chiamare Slack è disponibile nell'articolo relativo ai [webhook negli avvisi di Log Analytics](log-analytics-alerts-webhooks.md).

Le azioni webhook includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| URL webhook |URL del webhook. |
| Payload JSON personalizzato |Payload personalizzato da inviare insieme al webhook.  Vedere di seguito per altri dettagli. |


I webhook includono un URL e un payload in fermato JSON che corrisponde ai dati inviati al servizio esterno.  Per impostazione predefinita, il payload include i valori riportati nella tabella seguente.  È possibile scegliere di sostituire questo payload con un payload personalizzato.  In questo caso è possibile usare le variabili nella tabella per ognuno dei parametri per includerne il valore nel payload personalizzato.

| Parametro | Variabile | Descrizione |
|:--- |:--- |:--- |
| AlertRuleName |#AlertRuleName |Nome della regola di avviso. |
| AlertThresholdOperator |#thresholdoperator |Operatore di soglia per la regola di avviso.  *Maggiore di* o *Minore di*. |
| AlertThresholdValue |#thresholdvalue |Valore di soglia per la regola di avviso. |
| LinkToSearchResults |#LinkToSearchResults |Collegamento alla ricerca nei log di Log Analytics che restituisce i record della query che ha creato l'avviso. |
| ResultCount |#searchresultcount |Numero di record nei risultati della ricerca. |
| SearchIntervalEndtimeUtc |#SearchIntervalEndtimeUtc |Ora di fine per la query in formato UTC. |
| SearchIntervalInSeconds |#searchinterval |Intervallo di tempo per la regola di avviso. |
| SearchIntervalStartTimeUtc |#SearchIntervalStartTimeUtc |Ora di inizio per la query in formato UTC. |
| SearchQuery |#SearchQuery |Query di ricerca nei log usata dalla regola di avviso. |
| SearchResults |Vedere di seguito |Record restituiti dalla query in formato JSON.  Limitati ai primi 5.000 record. |
| WorkspaceID |#WorkspaceID |ID dell'aea di lavoro di OMS. |

Ad esempio, è possibile specificare il payload personalizzato seguente che include un singolo parametro denominato *text*.  Il servizio chiamato da questo webhook si aspetta questo parametro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Il payload di esempio viene risolto in una stringa di simile alla seguente quando viene inviato al webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Per includere i risultati della ricerca in un payload personalizzato, aggiungere la riga seguente come proprietà di primo livello nel payload JSON.  

    "IncludeSearchResults":true

Ad esempio, per creare un payload personalizzato che include solo il nome dell'avviso e i risultati della ricerca, è possibile usare quanto segue. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Per un esempio completo di creazione di una regola di avviso con un webhook per avviare un servizio esterno, vedere l'articolo su come [creare un'azione webhook di avviso in Log Analytics di OMS per inviare messaggi a Slack](log-analytics-alerts-webhooks.md).

## <a name="runbook-actions"></a>Azioni runbook
Le azioni runbook avviano un runbook in Automazione di Azure.  Per usare questo tipo di azione, è necessario che la [soluzione di automazione](log-analytics-add-solutions.md) sia installata e configurata nell'area di lavoro di OMS.  È possibile selezionare i runbook nell'account di automazione che è stato configurato nella soluzione di automazione.

Le azioni runbook includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:---|
| Runbook | Runbook da avviare quando viene creato un avviso. |
| Esegui in | Specificare **Azure** per eseguire il runbook nel cloud.  Specificare **Ruolo di lavoro ibrido** per eseguire il runbook in un agente in cui è installato un [ruolo di lavoro ibrido per runbook](../automation/automation-hybrid-runbook-worker.md ).  |

Le azioni runbook avviano il runbook tramite un [webhook](../automation/automation-webhooks.md).  Quando si crea la regola di avviso, viene creato automaticamente un nuovo webhook per il runbook con il nome **OMS Alert Remediation** seguito da un GUID.  

Non è possibile popolare direttamente alcun parametro del runbook, ma il [parametro $WebhookData](../automation/automation-webhooks.md) includerà i dettagli dell'avviso, inclusi i risultati della ricerca nei log che lo ha creato.  Il runbook dovrà definire **$WebhookData** come parametro per consentire l'accesso alle proprietà dell'avviso.  I dati dell'avviso sono disponibili in formato JSON in una singola proprietà denominata **SearchResults** nella proprietà **RequestBody** di **$WebhookData**.  Saranno incluse le proprietà riportate nella tabella seguente.

| Nodo | Description |
|:--- |:--- |
| id |Percorso e GUID della ricerca. |
| __metadata |Informazioni sull'avviso, inclusi il numero di record e lo stato dei risultati della ricerca. |
| value |Voce separata per ogni record nei risultati della ricerca.  I dettagli della voce corrisponderanno alle proprietà e ai valori del record. |

Ad esempio, il runbook seguente estrae i record restituiti dalla ricerca nel log e assegna proprietà diverse in base al tipo di ogni record.  Si noti che il runbook converte prima di tutto **RequestBody** da JSON, in modo che possa essere usato come oggetto in PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="next-steps"></a>Passaggi successivi
- Completare una procedura dettagliata per la [configurazione di un webhook](log-analytics-alerts-webhooks.md) con una regola di avviso.  
- Informazioni su come scrivere [runbook in Automazione di Azure](https://azure.microsoft.com/documentation/services/automation) per la risoluzione dei problemi identificati dagli avvisi.
