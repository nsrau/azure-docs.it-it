---
title: Eseguire query su più risorse con Monitoraggio di Azure | Microsoft Docs
description: Questo articolo illustra come eseguire query sulle risorse di più aree di lavoro e sull'app di Application Insights nella sottoscrizione.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: b0d12021be5a5dca348ea3ffa3f0b853725812da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589286"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Eseguire query di log su più risorse in Monitoraggio di Azure  

Monitoraggio di Azure consentiva in precedenza di analizzare i dati solo all'interno dell'area di lavoro corrente, limitando la possibilità di eseguire query su più aree di lavoro definite nella sottoscrizione.  Inoltre, era possibile solo cercare gli elementi di telemetria raccolti dall'applicazione basata sul Web con Application Insights direttamente in Application Insights o in Visual Studio.  Per questi motivi, risultava difficile anche analizzare insieme in modo nativo i dati operativi e quelli dell'applicazione.   

Ora è possibile eseguire query non solo tra più aree di lavoro di Log Analytics, ma anche su dati di un'app specifica di Application Insights nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione. Si ottiene così una vista dei dati dell'intero sistema.  È possibile eseguire questi tipi di query solo in [Log Analytics](portals.md).

## <a name="cross-resource-query-limits"></a>Limiti di query tra risorse 

* Il numero di risorse di Application Insights che è possibile includere in una singola query è limitato a 100.
* Query tra risorse non è supportata in visualizzazione progettazione. È possibile creare una query in Log Analitica e aggiungerla al dashboard di Azure e [visualizzare una ricerca log](../../azure-monitor/learn/tutorial-logs-dashboards.md#visualize-a-log-search). 
* Query tra risorse degli avvisi del log è supportata nella nuova [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Per impostazione predefinita, Monitoraggio di Azure usa l'[API legacy degli avvisi di Log Analytics](../platform/api-alerts.md) per la creazione di nuove regole di avviso relative ai log dal portale di Azure, a meno che non si esegua la commutazione dall'[API legacy degli avvisi relativi ai log](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Dopo la commutazione, la nuova API diventa quella predefinita per le nuove regole di avviso nel portale di Azure e consente di creare regole di avviso dei log basate su query su più risorse. È possibile creare le regole di avviso del log di query tra risorse senza effettuare il passaggio con il [modello ARM per scheduledQueryRules API](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) –, ma questa regola di avviso è gestibile tuttavia [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e non dal portale di Azure.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Esecuzione di query tra aree di lavoro di Log Analytics e da Application Insights
Per fare riferimento a un'altra area di lavoro nella query, usare l'identificatore [*workspace*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression), mentre per un'app di Application Insights usare l'identificatore [*app*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression).  

### <a name="identifying-workspace-resources"></a>Identificazione delle risorse dell'area di lavoro
Gli esempi seguenti dimostrano che le query eseguite su più aree di lavoro di Log Analytics restituiscono un riepilogo del numero di log dalla tabella di aggiornamento in un'area di lavoro denominata *contosoretail-it*. 

Esistono vari modi per identificare un'area di lavoro:

* Nome risorsa - Un nome dell'area di lavoro leggibile dall'utente, spesso definito genericamente *nome del componente*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >L'identificazione di un'area di lavoro in base al nome presuppone che il nome sia univoco in tutte le sottoscrizioni accessibili. Se sono presenti più applicazioni con il nome specificato, la query avrà esito negativo a causa dell'ambiguità. In questo caso è necessario usare uno degli altri identificatori.

* Nome completo - È il nome completo dell'area di lavoro, composto dal nome della sottoscrizione, dal gruppo di risorse e dal nome del componente nel formato seguente: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Poiché i nomi delle sottoscrizioni di Azure non sono univoci, questo identificatore potrebbe essere ambiguo. 
    >

* ID area di lavoro - L'ID dell'area di lavoro è l'identificatore univoco e non modificabile assegnato a ogni area di lavoro rappresentato come identificatore univoco globale (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID risorsa Azure - L'identità univoca definita da Azure dell'area di lavoro. Si usa quando il nome della risorsa è ambiguo.  Per le aree di lavoro, il formato è: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Ad esempio: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identificazione di un'applicazione
Gli esempi seguenti restituiscono il riepilogo del numero di richieste eseguite su un'app denominata *fabrikamapp* in Application Insights. 

L'identificazione di un'applicazione in Application Insights può essere eseguita con l'espressione *app(Identifier)*.  L'argomento *Identifier* specifica l'app usando uno degli elementi seguenti:

* Nome risorsa - Nome dell'app leggibile dall'utente, spesso definito genericamente *nome del componente*.  

    `app("fabrikamapp")`

* Nome completo - Nome completo dell'app, composto dal nome della sottoscrizione, dal gruppo di risorse e dal nome del componente nel formato seguente: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Poiché i nomi delle sottoscrizioni di Azure non sono univoci, questo identificatore potrebbe essere ambiguo. 
    >

* ID - GUID dell'app.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID risorsa Azure - Identità univoca dell'app definita da Azure. Si usa quando il nome della risorsa è ambiguo. Il formato è: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*.  

    Ad esempio: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Esecuzione di una query in più risorse
È possibile eseguire una query su più risorse da una qualsiasi delle istanze di risorse, ad esempio aree di lavoro e app combinate.
    
Esempio di query in due aree di lavoro:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Uso di una query tra risorse per più risorse
Quando si usano query tra risorse per correlare i dati di più aree di lavoro di Log Analytics e di risorse di Application Insights, la query può diventare complessa e difficile da gestire. È consigliabile sfruttare le [funzioni delle query di log di Monitoraggio di Azure](functions.md) per separare la logica della query dall'ambito delle risorse della query, semplificando così la struttura della query stessa. L'esempio seguente mostra come è possibile monitorare più risorse di Application Insights e visualizzare il numero di richieste non riuscite per nome applicazione. 

Creare una query come la seguente, che fa riferimento all'ambito delle risorse di Application Insights. Il comando `withsource= SourceApp` aggiunge una colonna che indica il nome dell'applicazione che ha inviato il log. [Salvare la query come funzione](functions.md#create-a-function) con l'alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



A questo punto è possibile [usare questa funzione](../../azure-monitor/log-query/functions.md#use-a-function) in una query tra risorse come la seguente. L'alias di funzione _applicationsScoping_ restituisce l'unione della tabella delle richieste da tutte le applicazioni definite. La query filtra quindi le richieste non riuscite e visualizza le tendenze per applicazione. L'operatore _parse_ è facoltativo in questo esempio. Estrae il nome dell'applicazione dalla proprietà _SourceApp_.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![Diagramma temporale](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Analizzare i dati di log in Monitoraggio di Azure](log-query-overview.md) per una panoramica delle query di log e di come sono strutturati i dati di log di Monitoraggio di Azure.
- Vedere [Query di log di Monitoraggio di Azure](query-language.md) per visualizzare tutte le risorse per le query di log di Monitoraggio di Azure.
