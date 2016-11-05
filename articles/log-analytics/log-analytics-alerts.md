---
title: Avvisi in Log Analytics | Microsoft Docs
description: Gli avvisi in Log Analytics identificano le informazioni importanti nel repository OMS e possono notificare i problemi all'utente in modo proattivo o richiamare le azioni per tentare di correggerle.  Questo articolo descrive come creare una regola di avviso e include i dettagli relativi alle diverse azioni che possono attivare.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2016
ms.author: bwren

---
# <a name="alerts-in-log-analytics"></a>Avvisi in Log Analytics
Gli avvisi in Log Analytics identificano informazioni importanti nel repository OMS.  Le regole di avviso eseguono automaticamente ricerche nei log in base a una pianificazione e creano un record di avviso se i risultati corrispondano a criteri particolari.  La regola può quindi eseguire automaticamente una o più azioni per notificare l'avviso all'utente in modo proattivo o richiamare un altro processo.   

![Avvisi Log Analytics](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Creazione di una regola di avviso
Per creare una regola di avviso, è necessario creare prima di tutto una ricerca nei log per i record che dovranno richiamare l'avviso.  Il pulsante **Avviso** diventerà disponibile per poter creare e configurare la regola di avviso.

1. Dalla pagina di panoramica di OMS fare clic su **Ricerca log**.
2. Creare una nuova query di ricerca nei log o selezionarne una salvata. 
3. Fare clic su **Avviso** nella parte superiore della pagina per aprire la schermata **Aggiungi regola di avviso**.
4. Per informazioni dettagliate sulle opzioni per configurare l'avviso, vedere le tabelle seguenti.
5. Quando si specifica l'intervallo di tempo per la regola di avviso, verrà visualizzato il numero di record esistenti che corrispondono ai criteri di ricerca per tale intervallo di tempo.  Ciò consente di determinare la frequenza con cui verrà visualizzato il numero di risultati previsti.
6. Fare clic su **Salva** per salvare la regola di avviso.  Verrà avviata immediatamente l'esecuzione.

![Aggiungi regola di avviso](media/log-analytics-alerts/add-alert-rule.png)

| Proprietà | Descrizione |
|:--- |:--- |
| **Informazioni sull'avviso** | |
| Nome |Nome univoco per identificare la regola di avviso. |
| Gravità |Gravità dell'avviso creato da questa regola. |
| Query di ricerca |Selezionare **Usa query di ricerca corrente** per usare la query corrente o selezionare una ricerca salvata dall'elenco.  La sintassi della query è disponibile nella casella di testo dove può essere modificata, se necessario. |
| Intervallo di tempo |Specifica l'intervallo di tempo per la query.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore.  Deve essere maggiore o uguale alla frequenza degli avvisi.  <br><br>  Ad esempio, se l'intervallo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15 , verranno restituiti solo i record creati tra 12.15 e le 13.15. |
| **Pianificazione** | |
| Soglia |Criteri per la creazione di un avviso.  Viene creato un avviso se il numero di record restituiti dalla query corrisponde a questi criteri. |
| Frequenza di avviso |Specifica la frequenza con cui deve essere eseguita la query.  Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore.  Deve essere uguale o minore dell'intervallo di tempo. |
| Elimina avvisi |Quando si attiva l'eliminazione per la regola di avviso, le azioni della regola vengono disabilitate per un periodo di tempo definito dopo la creazione di un nuovo avviso.  La regola è ancora in esecuzione e, se i criteri vengono soddisfatti, creerà i record di avviso.  In questo modo, l'utente ha il tempo necessario per risolvere il problema senza eseguire azioni duplicate. |
| **Actions** | |
| Notifica tramite posta elettronica |Specificare **Sì** se si vuole inviare un messaggio di posta elettronica quando viene generato l'avviso. |
| Oggetto |Oggetto nel messaggio di posta elettronica.  È possibile modificare il corpo del messaggio. |
| Destinatari |Indirizzi di tutti i destinatari di posta elettronica.  Se si specifica più di un indirizzo, separare ognuno con un punto e virgola (;). |
| webhook |Specificare **Sì** se si vuole chiamare un webhook quando viene generato l'avviso. |
| URL webhook |URL del webhook. |
| Includi payload JSON personalizzato |Selezionare questa opzione se si vuole sostituire il payload predefinito con un payload personalizzato. |
| Specificare il payload JSON personalizzato |Payload personalizzato per il webhook.  Per informazioni dettagliate, vedere la sezione precedente. |
| Runbook |Specificare **Sì** se si vuole avviare un runbook di Automazione di Azure quando viene generato l'avviso. |
| Selezionare un runbook |Selezionare il runbook da avviare dai runbook disponibili nell'account di automazione configurato nella soluzione di automazione. |
| Esegui in |Selezionare **Azure** per eseguire il runbook nel cloud di Azure.  Selezionare **Ruolo di lavoro ibrido** per eseguire il runbook su un [ruolo di lavoro ibrido per runbook](../automation/automation-hybrid-runbook-worker.md) nell'ambiente locale. |

## <a name="manage-alert-rules"></a>Gestire le regole di avviso
È possibile ottenere un elenco di tutte le regole di avviso nel menu **Avvisi** delle **Impostazioni** di Log Analytics.  

![Gestisci avvisi](./media/log-analytics-alerts/configure.png)

1. Nella console di OMS selezionare il riquadro **Impostazioni** .
2. Selezionare **Avvisi**.

Da questa visualizzazione è possibile eseguire più azioni.

* Disabilitare una regola selezionando **No** accanto alla regola desiderata.
* Modificare una regola di avviso facendo clic sull'icona a forma di matita accanto alla regola.
* Rimuovere una regola di avviso facendo clic sull'icona a forma di **X** accanto alla regola. 

## <a name="setting-time-windows"></a>Impostazione di intervalli di tempo
### <a name="event-alerts"></a>Avvisi di eventi
Gli eventi includono origini dati, ad esempio registri eventi di Windows, Syslog e log personalizzati.  È consigliabile creare un avviso quando viene creato un evento di errore specifico o vengono creati più eventi di errore all'interno di un intervallo di tempo specifico.

Per generare un avviso su un singolo evento, impostare un numero di risultati maggiore di 0 e la frequenza e l'intervallo di tempo su 5 minuti.  Queste impostazioni attivano l'esecuzione della query ogni 5 minuti e la ricerca dell'occorrenza di un singolo evento creato dall'ultima esecuzione della query.  Una frequenza maggiore può ritardare il tempo tra l'evento raccolto e la creazione dell'avviso.

Alcune applicazioni possono registrare un errore occasionale che non deve necessariamente generare un avviso.  Ad esempio, l'applicazione può ripetere il processo che ha creato l'evento di errore e riuscire quindi al tentativo al successivo.  In questo caso, non è consigliabile creare l'avviso, a meno che in un intervallo di tempo specifico non vengano creati più eventi.  

In alcuni casi, è possibile creare un avviso in assenza di un evento.  Ad esempio, un processo può registrare eventi regolari per indicare che funziona correttamente.  Se non viene registrato uno di questi eventi in un intervallo di tempo specifico, dovrà essere creato un avviso.  In questo caso è necessario impostare la soglia *Minore di 1*.

### <a name="performance-alerts"></a>Avvisi di prestazioni
[I dati sulle prestazioni](log-analytics-data-sources-performance-counters.md) vengono archiviato come record nel repository OMS in modo analogo agli eventi.  Il valore in ogni record è la media misurata negli ultimi 30 minuti.  Se si vuole generare un avviso quando un contatore delle prestazioni supera una determinata soglia, tale soglia deve essere inclusa nella query.

Ad esempio, se si vuole generare un avviso quando il processore è in esecuzione oltre il 90% per 30 minuti, usare una query quale *Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90* e impostare la soglia per la regola di avviso *maggiore di 0*.  

 Poiché i [record delle prestazioni](log-analytics-data-sources-performance-counters.md) vengono aggregati ogni 30 minuti indipendentemente dalla frequenza di raccolta di ogni contatore, un intervallo di tempo inferiore a 30 minuti potrebbe non restituire alcun record.  L'impostazione dell'intervallo di tempo su 30 minuti assicura di ottenere un singolo record per ogni origine connessa che rappresenta la media in quell'intervallo.

## <a name="alert-actions"></a>Azioni di avviso
Oltre a creare un record di avviso, è possibile configurare la regola di avviso per eseguire automaticamente una o più azioni.  Le azioni possono notificare l'avviso all'utente in modo proattivo o richiamare un processo che prova a risolvere il problema rilevato.  Le sezioni seguenti descrivono le azioni attualmente disponibili.

### <a name="email-actions"></a>Azioni di posta elettronica
Le azioni di posta elettronica inviano un messaggio di posta elettronica con i dettagli dell'avviso a uno o più destinatari.  È possibile specificare l'oggetto del messaggio, ma il contenuto è un formato standard creato da Log Analytics.  Include informazioni di riepilogo, come il nome dell'avviso, oltre ai dettagli di un massimo di dieci record restituiti dalla ricerca nei log.  Include anche un collegamento a una ricerca nei log in Log Analytics che restituirà l'intero set di record dalla query.   Il mittente del messaggio è *Team di Microsoft Operations Management Suite &lt;noreply@oms.microsoft.com&gt;*. 

### <a name="webhook-actions"></a>Azioni webhook
Le azioni Webhook consentono di richiamare un processo esterno tramite una singola richiesta HTTP POST.  Il servizio chiamato deve supportare i webhook e determinare come usare gli eventuali payload che riceve.  È anche possibile chiamare un'API REST che non supporta in modo specifico i webhook, purché la richiesta sia in un formato l'API riconosce.  L'uso di un webhook in risposta a un avviso consiste, ad esempio, nell'usare un servizio come [Slack](http://slack.com) per inviare un messaggio con i dettagli dell'avviso oppure nel creare un evento imprevisto in un servizio come [PagerDuty](http://pagerduty.com/).  

Una procedura dettagliata completa della creazione di una regola di avviso con un webhook per chiamare un servizio di esempio è disponibile nell'articolo relativo ai [webhook negli avvisi di Log Analytics](log-analytics-alerts-webhooks.md).

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


Per un esempio completo di creazione di una regola di avviso con un webhook per avviare un servizio esterno, vedere l'articolo relativo ai [webhook negli avvisi di Log Analytics](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Azioni runbook
Le azioni runbook avviano un runbook in Automazione di Azure.  Per usare questo tipo di azione, è necessario che la [soluzione di automazione](log-analytics-add-solutions.md) sia installata e configurata nell'area di lavoro di OMS.  Se non è installata quando si crea una nuova regola di avviso, viene visualizzato un collegamento alla relativa installazione.  È possibile selezionare i runbook nell'account di automazione che è stato configurato nella soluzione di automazione.

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


## <a name="alert-records"></a>Record di avvisi
I record degli avvisi creati dalle regole di avviso in Log Analytics hanno un **Tipo** impostato su **Avviso** e **SourceSystem** impostato su **OMS**.  Includono le proprietà elencate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| Tipo |*Avviso* |
| SourceSystem |*OMS* |
| AlertSeverity |Livello di gravità dell'avviso. |
| AlertName |Nome dell'avviso. |
| Query |Testo della query eseguita. |
| QueryExecutionEndTime |Fine dell'intervallo di tempo per la query. |
| QueryExecutionStartTime |Inizio dell'intervallo di tempo per la query. |
| TimeGenerated |Data e ora in cui è stato creato l'avviso. |

Sono disponibili altri tipi di record di avviso creati dalla [soluzione Gestione avvisi](log-analytics-solution-alert-management.md) e dalle [esportazioni di Power BI](log-analytics-powerbi.md).  Questi record hanno un **Tipo** impostato su **Avviso** ma vengono distinti da relativo **SourceSystem**.

## <a name="next-steps"></a>Passaggi successivi
* Installare la [soluzione Gestione avvisi](log-analytics-solution-alert-management.md) per analizzare gli avvisi creati in Log Analytics con gli avvisi raccolti da System Center Operations Manager (SCOM).
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) che possono generare avvisi.
* Completare una procedura dettagliata per la [configurazione di un webhook](log-analytics-alerts-webhooks.md) con una regola di avviso.  
* Informazioni su come scrivere [runbook in Automazione di Azure](https://azure.microsoft.com/documentation/services/automation) per la risoluzione dei problemi identificati dagli avvisi.

<!--HONumber=Oct16_HO2-->


