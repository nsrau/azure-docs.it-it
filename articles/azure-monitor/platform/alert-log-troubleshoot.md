---
title: Risolvere gli avvisi del log in Monitoraggio di Azure | Microsoft Docs
description: Problemi comuni, errori e soluzioni per le regole di avviso di log in Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683399"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Risolvere gli avvisi del log in Monitoraggio di Azure  

Questo articolo illustra come risolvere i problemi comuni che possono verificarsi quando si sta configurando gli avvisi del log in Monitoraggio di Azure. Fornisce anche soluzioni ai problemi comuni relativi alla configurazione degli avvisi del log o funzionalità. 

Il termine *gli avvisi del log* descrive gli avvisi che fire basato su una query di log in un [dell'area di lavoro di Azure Log Analitica](../learn/tutorial-viewdata.md) o nella [Azure Application Insights](../../azure-monitor/app/analytics.md). Altre informazioni sulle funzionalità, terminologia e i tipi nella [gli avvisi del Log in Monitoraggio di Azure](../platform/alerts-unified-log.md).

> [!NOTE]
> Questo articolo non considera i casi in cui il portale di Azure Mostra una regola di avviso attivata e una notifica non viene eseguita da un gruppo di azioni associato. Per questi casi, vedere i dettagli nel [creare e gestire gruppi di azione nel portale di Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Avviso del log non attivato

Ecco alcuni motivi comuni per cui lo stato di un'applicazione configurata [regola di avviso di log in Monitoraggio di Azure](../platform/alerts-log.md) non viene visualizzato [come *generato* previsto](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Ora di inserimento dei dati per i log

Un avviso del log viene eseguito periodicamente la query di base [Log Analitica](../learn/tutorial-viewdata.md) oppure [Application Insights](../../azure-monitor/app/analytics.md). Poiché il monitoraggio di Azure elabora diversi terabyte di dati da migliaia di clienti provenienti da origini diverse in tutto il mondo, il servizio è soggetto a ritardi diversi. Per altre informazioni, vedere [ora di inserimento dei dati nei log di monitoraggio di Azure](../platform/data-ingestion-time.md).

Per ridurre ritardi, il sistema attende e riprova la query di avviso più volte se rileva che i dati necessari non vengono ancora inseriti. Il sistema ha un tempo di attesa impostato che aumenta in modo esponenziale. Avviso del log viene attivato solo dopo che i dati sono disponibili, in modo che il ritardo può essere dovuto a lenta inserimento dei dati di log. 

### <a name="incorrect-time-period-configured"></a>Periodo di tempo errato configurato

Come descritto nell'articolo sul [terminologia specifica per gli avvisi del log](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), il periodo di tempo indicato nella configurazione specifica l'intervallo di tempo per la query. La query restituisce solo i record che sono stati creati in questo intervallo. 

Il periodo di tempo limita i dati recuperati per una query di log di evitare abusi e aggira qualsiasi comando ora (ad esempio **fa**) utilizzato in una query di log. Se ad esempio il periodo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15, solo i record creati tra le 12.15 e le 13.15 vengono usati per la query di log. Se la query di log Usa un comando ora simile **fa (1 giorno)**, la query utilizza ancora solo i dati tra 12.15 e 1 15 PM perché il periodo di tempo è impostato su quell'intervallo.

Verificare che il periodo di tempo nella configurazione corrisponde alla query. Per l'esempio illustrato in precedenza, se la query di log viene utilizzato **fa (1 giorno)** con l'indicatore di colore verde, il periodo di tempo deve essere impostato su 24 ore o 1440 minuti (indicati in rosso). Questa impostazione garantisce che la query viene eseguita come previsto.

![Periodo di tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Opzione Elimina avvisi impostata

Come descritto nel passaggio 8 dell'articolo sul [creazione di una regola di avviso di log nel portale di Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), gli avvisi di log forniscono una **Elimina avvisi** opzione per eliminare le azioni di notifica e attivare per un periodo di tempo. Di conseguenza, si potrebbe pensare che non venga attivato un avviso. In effetti, è stato generato, ma è stata eliminata.  

![Elimina avvisi](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Regola di avviso di unità di misura della metrica errata

*Gli avvisi del log di misura della metrica* sono un sottotipo di avvisi del log che hanno una sintassi di query di avviso con restrizioni e funzionalità speciali. Una regola per un avviso di log di misurazione delle metriche richiede output da una serie temporale delle metriche della query. L'output è una tabella con periodi di tempo distinti, dimensioni insieme ai corrispondenti valori aggregati. 

È possibile scegliere di avere variabili aggiuntive nella tabella insieme a **AggregatedValue**. Queste variabili possono essere utilizzate per ordinare la tabella. 

Si supponga, ad esempio, che una regola per un avviso del log di misura della metrica è stata configurata come:

- Query di `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- periodo di 6 ore
- soglia di 50
- avviso per la logica di tre violazioni consecutive
- **Aggregazione al momento** scelto come **$table**

Poiché il comando include **riepilogare... dal** e fornisce due variabili (**timestamp** e **$table**), il sistema sceglie **$table** per **aggregazione al momento** . Il sistema ordina la tabella dei risultati per il **$table** campo, come illustrato nello screenshot seguente. Quindi esamina il multiplo **AggregatedValue** istanze per ogni tipo di tabella (ad esempio **availabilityResults**) per verificare se sono tre o più violazioni consecutive.

![Esecuzione della query con più valori di misura della metrica](media/alert-log-troubleshoot/LogMMQuery.png)

Poiché **aggregazione al momento** definito nella **$table**, i dati vengono ordinati in un **$table** colonna (indicata in rosso). Quindi raggruppare e cercare i tipi dei **aggregazione al momento** campo. 

Ad esempio, per **$table**, i valori per **availabilityResults** verrà considerato come uno dei tracciati/entità (indicate in arancione). In questo valore tracciato/entità, il servizio di avvisi Cerca tre violazioni consecutive (indicate in verde). Le violazioni della sicurezza attiva un avviso per il valore di tabella **availabilityResults**. 

Analogamente, se si verificano violazioni consecutive tre per qualsiasi altro valore di **$table**, un'altra notifica di avviso viene attivata per la stessa cosa. Il servizio di avvisi Ordina automaticamente i valori in un tracciato/entità (indicate in arancione) dal tempo.

Si supponga ora che è stata modificata la regola di avviso del log di misura della metrica e la query era `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. Il resto della configurazione è rimasto lo stesso come prima, tra cui la logica degli avvisi per tre violazioni consecutive. Il **aggregazione al momento** opzione è in questo caso **timestamp** per impostazione predefinita. Viene fornito un solo valore nella query per **riepilogare... dal** (vale a dire **timestamp**). Come nell'esempio precedente, l'output alla fine dell'esecuzione dovrebbe essere come illustrato nel modo seguente.

   ![Esecuzione di query di misurazione delle metriche con valori singolari](media/alert-log-troubleshoot/LogMMtimestamp.png)

Poiché **aggregazione al momento** definito nella **timestamp**, i dati viene ordinato in base il **timestamp** colonna (indicata in rosso). Quindi si Raggruppa per **timestamp**. Ad esempio, i valori per `2018-10-17T06:00:00Z` verrà considerato come uno dei tracciati/entità (indicate in arancione). In questo valore tracciato/entità, il servizio Avvisi non disponibili violazioni consecutive (poiché ciascuna **timestamp** valore ha solo una voce). Pertanto, l'avviso non viene mai attivato. In tal caso, l'utente deve:

- Aggiungere una variabile fittizia o una variabile esistente (ad esempio **$table**) per ordinare in modo corretto usando la **aggregazione al momento** campo.
- Riconfigurare la regola di avviso per l'uso degli avvisi per la logica in base **totale violazioni** invece.

## <a name="log-alert-fired-unnecessarily"></a>Avviso del log inutilmente attivato

Un configurata [regola di avviso di log in Monitoraggio di Azure](../platform/alerts-log.md) potrebbe essere attivato in modo imprevisto quando vengono visualizzati nella [avvisi di Azure](../platform/alerts-managing-alert-states.md). Le sezioni seguenti descrivono alcuni motivi comuni.

### <a name="alert-triggered-by-partial-data"></a>Avviso attivato da dati parziali

Application Insights e log Analitica sono soggetti a elaborazione e l'inserimento dei ritardi. Quando si esegue una query di avviso di log, si noterà che non sono disponibili dati o solo alcuni dati sono disponibili. Per altre informazioni, vedere [ora di inserimento dei dati di Log in Monitoraggio di Azure](../platform/data-ingestion-time.md).

A seconda del modo in cui è configurata la regola di avviso, responsabili potrebbe verificarsi se è presente alcun data o dati parziali nel log durante la fase di esecuzione degli avvisi. In questi casi, si consiglia di modificare la query di avviso o la configurazione. 

Ad esempio, se si configura la regola di avviso di log venga attivato quando il numero di risultati da una query analitica è inferiore a 5, l'avviso viene attivato quando non sono risultati parziali (un record) o dati (pari a zero record). Tuttavia, dopo il ritardo di inserimento dei dati, la stessa query con i dati completi potrebbe fornire un risultato di 10 record.

### <a name="alert-query-output-is-misunderstood"></a>È stata male interpretata output di query di avviso

La logica per gli avvisi di log viene specificata in una query di Analytics Query analitica è possibile usare varie funzioni matematiche e big data. Il servizio avvisi viene eseguita la query a intervalli specificati con i dati per un periodo di tempo specificato. Il servizio di avvisi rende piccole modifiche a query in base al tipo di avviso. È possibile visualizzare questa modifica il **deve essere eseguita** sezione il **Configura logica dei segnali** schermata:

![Query da eseguire](media/alert-log-troubleshoot/LogAlertPreview.png)

Il **deve essere eseguita** casella è ciò che viene eseguito il servizio di avvisi di log. Se si desidera comprendere ciò che la query di avviso di output potrebbe essere prima di creare l'avviso, è possibile eseguire la query specificata e l'intervallo di tempo tramite il [portale di Analitica](../log-query/portals.md) o nella [Analitica API](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Avviso del log è stata disabilitata

Le sezioni seguenti elencano alcuni dei motivi per cui potrebbe disabilitare il monitoraggio di Azure i [regola di avviso di log](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Risorse in cui l'avviso è stato creato non è più presente

Le regole di avviso di log create in Monitoraggio di Azure come destinazione una risorsa specifica, ad esempio un'area di lavoro di Analitica di Log di Azure, un'app di Azure Application Insights e una risorsa di Azure. Il servizio di avvisi di log quindi eseguirà una query analitica fornita nella regola per la destinazione specificata. Ma dopo la creazione di regole, gli utenti spesso andare al Elimina da Azure, o si sposta all'interno di Azure, la destinazione della regola di avviso di log. Poiché la destinazione della regola di avviso non è più valida, l'esecuzione della regola ha esito negativo.

In questi casi, monitoraggio di Azure consente di disattivare l'avviso del log e assicura che non vengono addebitati inutilmente quando la regola non può essere continuamente in esecuzione per un periodo di tempo considerevole (ad esempio, una settimana). È possibile trovare l'ora esatta in Monitoraggio di Azure disabilitata l'avviso del log tramite [Log attività di Azure](../../azure-resource-manager/resource-group-audit.md). Nel Log attività di Azure, viene aggiunto un evento quando il monitoraggio di Azure disabilita la regola di avviso di log.

L'evento di esempio seguente nel Log attività di Azure è per una regola di avviso che è stata disabilitata a causa di un errore causa dei continui.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Query usata in un avviso del log non è valido

Ogni regola di avviso di log creato nel monitoraggio di Azure come parte della configurazione è necessario specificare una query analitica che verrà eseguito periodicamente il servizio di avvisi. Query analitica potrebbe essere la sintassi corretta al momento della creazione della regola o aggiornamento. Ma in alcuni casi, in un periodo di tempo, può sviluppare problemi di sintassi e causare l'errore di esecuzione regola la query specificata nella regola di avviso di log. Alcuni dei motivi per cui una query analitica fornita in una regola di avviso di log può sviluppare gli errori sono:

- La query viene scritto [eseguito su più risorse](../log-query/cross-workspace-query.md). E uno o più risorse specificate non sono più presenti.
- Non si è verificato alcun flusso di dati per la piattaforma analitica. Il [esecuzione di query restituisce un errore](https://dev.loganalytics.io/documentation/Using-the-API/Errors) perché non sono presenti dati per la query specificata.
- Modifiche nel [linguaggio di query](https://docs.microsoft.com/azure/kusto/query/) includono un formato rivisto per comandi e funzioni. Pertanto, la query specificata in precedenza in una regola di avviso non è più valida.

[Azure Advisor](../../advisor/advisor-overview.md) avvisa l'utente su questo comportamento. Viene aggiunta una raccomandazione per la regola di avviso di log specifico su Azure Advisor, sotto la categoria della disponibilità elevata con impatto medio e una descrizione di "Ripristina la regola di avviso di log per verificare che il monitoraggio". Se una query di avviso nella regola di avviso di log non viene rettificata dopo che Azure Advisor ha fornito una raccomandazione per sette giorni, monitoraggio di Azure verrà disattivata l'avviso del log e assicurarsi che non vengono addebitati inutilmente quando la regola non può essere continuamente in esecuzione per una (di period ridimensionabile ad esempio una settimana).

È possibile trovare l'ora esatta quando monitoraggio di Azure disabilitata la regola di avviso di log mediante la ricerca di un evento nel [Log attività di Azure](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, fare riferimento agli [avvisi di log in Azure](../platform/alerts-unified-log.md).
- Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md).
- Altre informazioni sulle [registrare query](../log-query/log-query-overview.md).
