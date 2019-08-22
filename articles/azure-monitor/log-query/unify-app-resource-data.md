---
title: Unificare più risorse di Application Insights in Monitoraggio di Azure | Microsoft Docs
description: Questo articolo include informazioni dettagliate su come usare una funzione disponibile in Log di Monitoraggio di Azure per eseguire query su più risorse di Application Insights e visualizzare i dati.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: d441b72b34da6146eba523563a09c2908cdcbbf4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650143"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificare più risorse di Application Insights in Monitoraggio di Azure 
Questo articolo descrive come eseguire una query e visualizzare tutti i dati di log Application Insights in un'unica posizione, anche quando si trovano in sottoscrizioni di Azure diverse, sostituendo la deprecazione del Connettore di Application Insights. Il numero di risorse di Application Insights che è possibile includere in una singola query è limitato a 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Approccio consigliato per eseguire query su più risorse di Application Insights 
Includere l'elenco di più risorse di Application Insights in una query può risultare impegnativo e difficile da gestire. In alternativa, è possibile sfruttare la funzione che consente di separare la logica di query dalla definizione dell'ambito delle applicazioni.  

Questo esempio mostra come monitorare più risorse di Application Insights e visualizzare il numero di richieste non riuscite per nome di applicazione. Prima di iniziare, eseguire questa query nell'area di lavoro connessa alle risorse di Application Insights per ottenere l'elenco delle applicazioni connesse: 

```
ApplicationInsights
| summarize by ApplicationName
```

Creare una funzione usando l'operatore di unione con l'elenco delle applicazioni e quindi salvare la query nell'area di lavoro come funzione con l'alias *applicationsScoping*. 

È possibile modificare in qualsiasi momento le applicazioni elencate nel portale passando a Esplora query nell'area di lavoro e selezionando la funzione di modifica e salvando oppure usando il cmdlet `SavedSearch` di PowerShell. 

>[!NOTE]
>Questo metodo non può essere usato con gli avvisi del log perché la convalida dell'accesso delle risorse della regola di avviso, incluse le aree di lavoro e le applicazioni, viene eseguita al momento della creazione dell'avviso. L'aggiunta di nuove risorse alla funzione dopo la creazione dell'avviso non è supportata. Se si preferisce usare la funzione per l'ambito delle risorse negli avvisi del log, è necessario modificare la regola di avviso nel portale o con un modello di Gestione risorse per aggiornare le risorse con ambito. In alternativa, è possibile includere l'elenco delle risorse nella query di avviso del log.

Il comando `withsource= SourceApp` aggiunge ai risultati una colonna che indica l'applicazione da cui è stato inviato il log. L'operatore Parse è facoltativo in questo esempio e USA per estrarre il nome dell'applicazione dalla proprietà SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

A questo punto si può usare la funzione applicationsScoping nella query su più risorse:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

La query usa lo schema di Application Insights, anche se viene eseguita nell'area di lavoro, perché la funzione applicationsScoping restituisce la struttura dei dati di Application Insights. L'alias della funzione restituisce l'unione delle richieste da tutte le applicazioni definite. La query filtra quindi le richieste non riuscite e visualizza le tendenze per applicazione.

![Esempio di risultati di query su più risorse](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Query sulle risorse di Application Insights e sui dati dell'area di lavoro 
Quando si arresta il Connettore e si devono eseguire query nel corso di un intervallo di tempo definito in base alla conservazione dei dati di Application Insights (90 giorni), per un periodo intermedio è necessario eseguire [query su più risorse](../../azure-monitor/log-query/cross-workspace-query.md), ovvero nell'area di lavoro e nelle risorse di Application Insights. Ciò è necessario finché i dati delle applicazioni non si accumulano in base al nuovo periodo di conservazione dei dati di Application Insights sopra indicato. La query richiede alcune modifiche poiché gli schemi in Application Insights e nell'area di lavoro sono diversi. Vedere più avanti in questa sezione la tabella che mostra le differenze tra gli schemi. 

>[!NOTE]
>Le [query su più risorse](../log-query/cross-workspace-query.md) negli avvisi dei log sono supportate nella nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Per impostazione predefinita, Monitoraggio di Azure usa l'[API legacy degli avvisi di Log Analytics](../platform/api-alerts.md) per la creazione di nuove regole di avviso relative ai log dal portale di Azure, a meno che non si esegua la commutazione dall'[API legacy degli avvisi relativi ai log](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Dopo la commutazione, la nuova API diventa quella predefinita per le nuove regole di avviso nel portale di Azure e consente di creare regole di avviso dei log basate su query su più risorse. È possibile creare regole di avviso dei log basate su [query su più risorse](../log-query/cross-workspace-query.md) senza effettuare la commutazione usando il [modello ARM per l'API scheduledQueryRules](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template), ma questa regola di avviso è gestibile tramite l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e non dal portale di Azure.

Se ad esempio il Connettore ha smesso di funzionare il giorno 01/11/2018, quando si esegue una query sui log delle risorse di Application Insights e sui dati delle applicazioni nell'area di lavoro, la query avrà un aspetto simile all'esempio seguente:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Differenze tra gli schemi dell'area di lavoro Log Analytics e delle risorse di Application Insights
La tabella seguente illustra le differenze tra gli schemi di Log Analytics e Application Insights.  

| Proprietà dell'area di lavoro Log Analytics| Proprietà delle risorse di Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Passaggi successivi

Usare la [ricerca log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare informazioni dettagliate per le app Application Insights.
