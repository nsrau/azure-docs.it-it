---
title: Risolvere gli avvisi del log in monitoraggio di Azure | Microsoft Docs
description: Problemi comuni, errori e soluzioni per le regole di avviso del log in Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: ec2ffe71a32781a855da258f3621738f1a5f6be4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294292"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Risolvere gli avvisi del log in monitoraggio di Azure  

Questo articolo illustra come risolvere i problemi comuni con gli avvisi del log in monitoraggio di Azure. Fornisce anche soluzioni ai problemi comuni relativi alla funzionalità e alla configurazione degli avvisi del log.

Gli avvisi del log consentono agli utenti di usare una query [log Analytics](../log-query/get-started-portal.md) per valutare le risorse registra ogni frequenza impostata e generare un avviso in base ai risultati. Le regole possono attivare una o più azioni utilizzando i [gruppi di azioni](./action-groups.md). [Altre informazioni sulla funzionalità e la terminologia degli avvisi del log](alerts-unified-log.md).

> [!NOTE]
> Questo articolo non considera i casi in cui il portale di Azure Mostra una regola di avviso attivata e una notifica non viene eseguita da un gruppo di azione associato. Per questi casi, vedere i dettagli sulla risoluzione dei problemi [qui](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

## <a name="log-alert-didnt-fire"></a>Avviso del log non attivato

### <a name="data-ingestion-time-for-logs"></a>Tempo di inserimento dati per i log

Monitoraggio di Azure elabora i terabyte dei log dei clienti in tutto il mondo, che possono causare [latenza](./data-ingestion-time.md)di inserimento dei log.

I log sono dati semi-strutturati e intrinsecamente più latenti rispetto alle metriche. Se si verificano più di 4 minuti di ritardo negli avvisi generati, è consigliabile usare gli [avvisi delle metriche](alerts-metric-overview.md). È possibile inviare i dati all'archivio delle metriche dai log usando gli [avvisi delle metriche per i log](alerts-metric-logs.md).

Il sistema ripete la valutazione degli avvisi più volte per attenuare la latenza. Una volta ricevuti i dati, viene generato l'avviso, che nella maggior parte dei casi non è uguale all'ora del record di log.

### <a name="incorrect-query-time-range-configured"></a>Intervallo di tempo della query non corretto configurato

L'intervallo di tempo della query è impostato nella definizione della condizione della regola. Questo campo è denominato **period** per le aree di lavoro e Application Insights e ha chiamato **l'intervallo di tempo della query di override** per tutti gli altri tipi di risorse. Analogamente a log Analytics, l'intervallo di tempo limita i dati delle query al periodo specificato. Anche se nella query viene usato il comando **fa** , verrà applicato l'intervallo di tempo. 

Una query, ad esempio, analizza 60 minuti, quando l'intervallo di tempo è di 60 minuti, anche se il testo contiene **ago (1D)**. È necessario che l'intervallo di tempo e il filtro del tempo di query corrispondano. Nel caso di esempio, la modifica **Period**dell'  /  **intervallo di tempo della query di sostituzione** del periodo su un giorno, funziona come previsto.

![Periodo di tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>Le azioni vengono disabilitate nella regola di avviso

Gli avvisi del log offrono un'opzione per disattivare le azioni degli avvisi attivati per un periodo di tempo specifico. Questo campo è denominato non **visualizzare avvisi** nelle aree di lavoro e Application Insights. In tutti gli altri tipi di risorse, viene chiamato **azioni mute**. 

Un problema comune è che si ritiene che l'avviso non abbia attivato le azioni a causa di un problema del servizio. Inoltre, è stato disattivato dalla configurazione della regola.

![Elimina avvisi](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Regola di avviso di misurazione delle metriche con suddivisione con l'API Log Analytics legacy

La [misurazione delle metriche](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) è un tipo di avviso del log basato sui risultati della serie temporale riepilogati. Queste regole consentono il raggruppamento in base alle colonne per [suddividere gli avvisi](alerts-unified-log.md#split-by-alert-dimensions). Se si usa l'API Log Analytics legacy, la suddivisione non funzionerà come previsto. La scelta del raggruppamento nell'API legacy non è supportata.

L'API ScheduledQueryRules corrente consente di impostare l' **aggregazione su** nelle regole di [misurazione delle metriche](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) , che funzioneranno come previsto. [Altre informazioni sul trasferimento all'API ScheduledQueryRules corrente](alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>Avviso del log inutilmente attivato

Una [regola di avviso del log configurata in monitoraggio di Azure](./alerts-log.md) potrebbe essere attivata in modo imprevisto. Le sezioni seguenti descrivono alcuni motivi comuni.

### <a name="alert-triggered-by-partial-data"></a>Avviso attivato da dati parziali

Monitoraggio di Azure elabora i terabyte dei log dei clienti in tutto il mondo, che possono causare [latenza](./data-ingestion-time.md)di inserimento dei log.

I log sono dati semi-strutturati e intrinsecamente più latenti rispetto alle metriche. Se si verificano molti incendi negli avvisi attivati, è consigliabile usare gli [avvisi delle metriche](alerts-metric-overview.md). È possibile inviare i dati all'archivio delle metriche dai log usando gli [avvisi delle metriche per i log](alerts-metric-logs.md).

Gli avvisi del log funzionano meglio quando si tenta di rilevare i dati nei log. Funziona meno bene quando si tenta di rilevare la mancanza di dati nei log. Ad esempio, avvisi sull'heartbeat della macchina virtuale. 

Sebbene esistano funzionalità predefinite per la prevenzione di falsi avvisi, possono comunque verificarsi su dati molto latenti (oltre circa 30 minuti) e dati con picchi di latenza.

### <a name="query-optimization-issues"></a>Problemi di ottimizzazione delle query

Il servizio di avvisi modifica la query per ottimizzare il carico e la latenza di avviso inferiori. Il flusso di avvisi è stato creato per trasformare i risultati che indicano il problema a un avviso. Ad esempio, nel caso di una query come la seguente:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Se lo scopo dell'utente è di avvisare, quando si verifica questo tipo di evento, la logica di avviso viene accodata `count` alla query. La query che verrà eseguita sarà:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Non è necessario aggiungere la logica di avviso alla query e questa operazione potrebbe causare problemi. Nell'esempio precedente, se si include `count` nella query, il valore risultante sarà sempre 1, dal momento che il servizio Alert esegue `count` `count` .

La query ottimizzata è il servizio di avviso del log eseguito. È possibile eseguire la query modificata nel [portale](../log-query/log-query-overview.md) log Analytics o nell' [API](/rest/api/loganalytics/).

Per le aree di lavoro e Application Insights, viene chiamato **query da eseguire** nel riquadro condizione. In tutti gli altri tipi di risorse selezionare **Visualizza query di avviso finale** nella scheda condizione.

![Query da eseguire](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>Avviso del log disabilitato

Le sezioni seguenti elencano alcuni motivi per cui monitoraggio di Azure potrebbe disabilitare una regola di avviso del log. È stato inoltre incluso un [esempio del log attività che viene inviato quando una regola è disabilitata](#activity-log-example-when-rule-is-disabled).

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>L'ambito di avviso non esiste più o è stato spostato

Quando le risorse dell'ambito di una regola di avviso non sono più valide, l'esecuzione della regola ha esito negativo. In questo caso, anche la fatturazione viene arrestata.

Monitoraggio di Azure Disabilita l'avviso del log dopo una settimana in caso di errore continuo.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>La query utilizzata in un avviso del log non è valida

Quando viene creata una regola di avviso del log, la query viene convalidata per la sintassi corretta. In alcuni casi, tuttavia, la query fornita nella regola di avviso del log può iniziare a non riuscire. Di seguito sono riportati alcuni motivi comuni:

- Le regole sono state create tramite l'API e la convalida è stata ignorata dall'utente.
- La query [viene eseguita su più risorse](../log-query/cross-workspace-query.md) e una o più risorse sono state eliminate o spostate.
- La [query ha esito negativo](https://dev.loganalytics.io/documentation/Using-the-API/Errors) perché:
    - La soluzione di registrazione non è stata [distribuita nell'area di lavoro](../insights/solutions.md#install-a-monitoring-solution), quindi le tabelle non vengono create.
    - I dati sono stati interrotti a una tabella nella query per più di 30 giorni.
    - Le [tabelle dei log personalizzati](data-sources-custom-logs.md) non sono ancora state create perché il flusso di dati non è stato avviato.
- Le modifiche nel [linguaggio di query](/azure/kusto/query/) includono un formato modificato per i comandi e le funzioni. Quindi, la query fornita in precedenza non è più valida.

[Azure Advisor](../../advisor/advisor-overview.md) segnala questo comportamento. Viene aggiunta una raccomandazione sulla regola di avviso del log interessata. La categoria utilizzata è "disponibilità elevata" con un effetto medio e una descrizione della regola di avviso "Ripristina la regola di avviso del log per garantire il monitoraggio".

## <a name="alert-rule-quota-was-reached"></a>È stata raggiunta la quota della regola di avviso

Il numero di regole di avviso per la ricerca log per ogni sottoscrizione e risorsa è soggetto ai limiti di quota descritti [qui](../service-limits.md).

### <a name="recommended-steps"></a>Procedure consigliate
    
Se è stato raggiunto il limite di quota, i passaggi seguenti possono contribuire a risolvere il problema.

1. Provare a eliminare o disabilitare le regole di avviso di ricerca log che non vengono più usate.
1. Provare a utilizzare [la suddivisione degli avvisi in base alle dimensioni](alerts-unified-log.md#split-by-alert-dimensions) per ridurre il numero di regole. Queste regole consentono di monitorare molte risorse e i casi di rilevamento.
1. Se è necessario aumentare il limite di quota, continuare a aprire una richiesta di supporto e fornire le informazioni seguenti:

    - ID di sottoscrizione e ID di risorsa per i quali è necessario aumentare il limite di quota.
    - Motivo dell'aumento della quota.
    - Tipo di risorsa per l'aumento della quota: **log Analytics**, **Application Insights**e così via.
    - Limite di quota richiesto.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Per controllare l'utilizzo corrente delle nuove regole di avviso del log
    
#### <a name="from-the-azure-portal"></a>Dal portale di Azure

1. Aprire la schermata *avvisi* e selezionare *Gestisci regole di avviso*
2. Applicare un filtro per la sottoscrizione pertinente usando il controllo dell'elenco a discesa *Sottoscrizione*
3. Assicurarsi di non filtrare per un gruppo di risorse, un tipo di risorsa o una risorsa specifici
4. Selezionare "Ricerca log" nel controllo dell'elenco a discesa *Tipo di segnale*
5. Verificare che il controllo dell'elenco a discesa *Stato* sia impostato su "Abilitato"
6. Il numero totale di regole di avviso della ricerca log verrà visualizzato al di sopra dell'elenco delle regole

#### <a name="from-api"></a>Nell'API

- PowerShell- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [Elenco per sottoscrizione](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Esempio di log attività quando la regola è disabilitata

Se la query ha esito negativo per sette giorni in modo continuo, monitoraggio di Azure Disabilita l'avviso del log e arresta la fatturazione della regola. È possibile individuare l'ora esatta in cui monitoraggio di Azure ha disabilitato l'avviso del log nel [log attività di Azure](../../azure-resource-manager/management/view-activity-logs.md). Vedere l'esempio seguente:

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

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, fare riferimento agli [avvisi di log in Azure](./alerts-unified-log.md).
- Altre informazioni sulla [configurazione degli avvisi del log](../log-query/log-query-overview.md).
- Altre informazioni sulle [query di log](../log-query/log-query-overview.md).
