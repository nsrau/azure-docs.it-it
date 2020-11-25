---
title: Eseguire query su più risorse con Monitoraggio di Azure | Microsoft Docs
description: Questo articolo illustra come eseguire query sulle risorse di più aree di lavoro e sull'app di Application Insights nella sottoscrizione.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 8eb163c95fb1426ebae8956d50f6d8f6aec6fd7f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013650"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Eseguire query di log in monitoraggio di Azure che si estendono su più aree di lavoro e app

I log di monitoraggio di Azure supportano query tra più aree di lavoro Log Analytics e app Application Insights nello stesso gruppo di risorse, in un altro gruppo di risorse o in un'altra sottoscrizione. Si ottiene così una vista dei dati dell'intero sistema.

Esistono due metodi per eseguire query sui dati archiviati in più aree di lavoro e app:
1. In modo esplicito specificando i dettagli dell'area di lavoro e dell'app. Questa tecnica è descritta in dettaglio in questo articolo.
2. Utilizzando in modo implicito le [query del contesto delle risorse](../platform/design-logs-deployment.md#access-mode). Quando si esegue una query nel contesto di una risorsa specifica, un gruppo di risorse o una sottoscrizione, i dati rilevanti verranno recuperati da tutte le aree di lavoro che contengono i dati per queste risorse. I dati Application Insights archiviati nelle app non verranno recuperati.

> [!IMPORTANT]
> Se si usa una [risorsa di Application Insights basata sull'area di lavoro](../app/create-workspace-resource.md), i dati di telemetria vengono archiviati in un'area di lavoro Log Analytics con tutti gli altri dati di log. Usare l'espressione log () per scrivere una query che includa l'applicazione in più aree di lavoro. Per più applicazioni nella stessa area di lavoro, non è necessaria una query tra aree di lavoro.


## <a name="cross-resource-query-limits"></a>Limiti di query tra risorse 

* Il numero di risorse Application Insights e le aree di lavoro Log Analytics che è possibile includere in una singola query sono limitate a 100.
* Le query su più risorse non sono supportate Progettazione visualizzazioni. È possibile creare una query in Log Analytics e aggiungerla al dashboard di Azure per [visualizzare una query di log](../learn/tutorial-logs-dashboards.md). 
* Le query tra risorse negli avvisi del log sono supportate solo nell' [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)corrente. Se si usa l'API legacy Log Analytics Alerts, sarà necessario [passare all'API corrente](../platform/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Esecuzione di query tra aree di lavoro di Log Analytics e da Application Insights
Per fare riferimento a un'altra area di lavoro nella query, usare l'identificatore [*workspace*](./workspace-expression.md), mentre per un'app di Application Insights usare l'identificatore [*app*](./app-expression.md).  

### <a name="identifying-workspace-resources"></a>Identificazione delle risorse dell'area di lavoro
Gli esempi seguenti dimostrano che le query eseguite su più aree di lavoro di Log Analytics restituiscono un riepilogo del numero di log dalla tabella di aggiornamento in un'area di lavoro denominata *contosoretail-it*. 

Esistono vari modi per identificare un'area di lavoro:

* Nome risorsa - Un nome dell'area di lavoro leggibile dall'utente, spesso definito genericamente *nome del componente*. 

    `workspace("contosoretail-it").Update | count`

* Nome completo-è il nome completo dell'area di lavoro, composto dal nome della sottoscrizione, dal gruppo di risorse e dal nome del componente nel formato seguente: *subscriptionname/resourceGroup/ComponentName*. 

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

    >[!NOTE]
    >L'identificazione di un'applicazione in base al nome presuppone l'univocità tra tutte le sottoscrizioni accessibili. Se sono presenti più applicazioni con il nome specificato, la query avrà esito negativo a causa dell'ambiguità. In questo caso è necessario usare uno degli altri identificatori.

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



A questo punto è possibile [usare questa funzione](./functions.md#use-a-function) in una query tra risorse come la seguente. L'alias di funzione _applicationsScoping_ restituisce l'unione della tabella delle richieste da tutte le applicazioni definite. La query filtra quindi le richieste non riuscite e visualizza le tendenze per applicazione. L'operatore _parse_ è facoltativo in questo esempio. Estrae il nome dell'applicazione dalla proprietà _SourceApp_.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Questo metodo non può essere usato con gli avvisi del log perché la convalida dell'accesso delle risorse della regola di avviso, incluse le aree di lavoro e le applicazioni, viene eseguita al momento della creazione dell'avviso. L'aggiunta di nuove risorse alla funzione dopo la creazione dell'avviso non è supportata. Se si preferisce usare la funzione per l'ambito delle risorse negli avvisi del log, è necessario modificare la regola di avviso nel portale o con un modello di Gestione risorse per aggiornare le risorse con ambito. In alternativa, è possibile includere l'elenco delle risorse nella query di avviso del log.


![Diagramma temporale](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Analizzare i dati di log in Monitoraggio di Azure](log-query-overview.md) per una panoramica delle query di log e di come sono strutturati i dati di log di Monitoraggio di Azure.

