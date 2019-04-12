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
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429517"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificare più risorse di Application Insights in Monitoraggio di Azure 
Questo articolo descrive come eseguire query su tutti i dati dei log applicazioni di Application Insights e visualizzarli in un'unica posizione, anche quando si trovano in sottoscrizioni di Azure diverse, in sostituzione del Connettore di Application Insights, ora deprecato. Il numero di risorse di Application Insights che è possibile includere in una singola query è limitato a 100.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Approccio consigliato per eseguire query su più risorse di Application Insights 
Includere l'elenco di più risorse di Application Insights in una query può risultare impegnativo e difficile da gestire. In alternativa, è possibile sfruttare la funzione che consente di separare la logica di query dalla definizione dell'ambito delle applicazioni.  

Questo esempio mostra come monitorare più risorse di Application Insights e visualizzare il numero di richieste non riuscite per nome di applicazione. Prima di iniziare, eseguire questa query nell'area di lavoro connessa alle risorse di Application Insights per ottenere l'elenco delle applicazioni connesse: 

```
ApplicationInsights
| summarize by ApplicationName
```

Creare una funzione usando l'operatore di unione con l'elenco delle applicazioni e quindi salvare la query nell'area di lavoro come funzione con l'alias *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>È possibile modificare in qualsiasi momento le applicazioni elencate nel portale passando a Esplora query nell'area di lavoro e selezionando la funzione di modifica e salvando oppure usando il cmdlet `SavedSearch` di PowerShell. Il comando `withsource= SourceApp` aggiunge ai risultati una colonna che indica l'applicazione da cui è stato inviato il log. 
>
>La query usa lo schema di Application Insights, anche se viene eseguita nell'area di lavoro, perché la funzione applicationsScoping restituisce la struttura dei dati di Application Insights. 
>
>In questo esempio l'operatore parse è facoltativo. Estrae il nome dell'applicazione dalla proprietà SourceApp. 

A questo punto si può usare la funzione applicationsScoping nella query su più risorse:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

L'alias della funzione restituisce l'unione delle richieste da tutte le applicazioni definite. La query filtra quindi le richieste non riuscite e visualizza le tendenze per applicazione.

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
| AvailabilityMessage | Message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp |  timestamp |
| Browser | client_browser |
| city | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Paese | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | Message | 
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
| RequestSuccess | esito positivo | 
| ResponseCode | resultCode | 
| Ruolo | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Passaggi successivi

Usare la [ricerca log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare informazioni dettagliate per le app Application Insights.
