---
title: Ottimizzazione delle query di avviso del log | Microsoft Docs
description: Suggerimenti per la scrittura di query di avviso efficaci
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: 7f03858b2427b2a2069ebe2c9d06425e7a741e2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294360"
---
# <a name="optimizing-log-alert-queries"></a>Ottimizzazione delle query di avviso del log
Questo articolo descrive come scrivere e convertire le query di [Avviso del log](alerts-unified-log.md) per ottenere prestazioni ottimali. Le query ottimizzate riducono la latenza e il carico degli avvisi che vengono eseguiti di frequente.

## <a name="how-to-start-writing-an-alert-log-query"></a>Come iniziare a scrivere una query del log degli avvisi

Le query di avviso iniziano a [eseguire query sui dati di log in log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) che indica il problema. È possibile usare l' [argomento degli esempi di query di avviso](../log-query/saved-queries.md) per comprendere cosa è possibile individuare. È anche possibile [iniziare a scrivere una query personalizzata](../log-query/get-started-portal.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Query che indicano il problema e non l'avviso

Il flusso di avvisi è stato creato per trasformare i risultati che indicano il problema a un avviso. Ad esempio, nel caso di una query come la seguente:

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

### <a name="avoid-limit-and-take-operators"></a>Evitare `limit` `take` operatori and

L'uso di `limit` e `take` nelle query può aumentare la latenza e il carico degli avvisi perché i risultati non sono coerenti nel tempo. È preferibile utilizzarlo solo se necessario.

## <a name="log-query-constraints"></a>Vincoli di query di log
Le [query di log nel monitoraggio di Azure](../log-query/log-query-overview.md) iniziano con un [`search`](/azure/kusto/query/searchoperator) operatore Table, o [`union`](/azure/kusto/query/unionoperator) .

Le query per le regole di avviso del log devono sempre iniziare con una tabella per definire un ambito chiaro, che consente di migliorare le prestazioni delle query e la pertinenza dei risultati. Le query nelle regole di avviso vengono eseguite di frequente, pertanto l'utilizzo `search` `union` di e può comportare un sovraccarico eccessivo nell'aggiunta di latenza all'avviso, in quanto richiede l'analisi tra più tabelle. Questi operatori riducono inoltre la capacità del servizio di avvisi di ottimizzare la query.

Non è supportata la creazione o la modifica delle regole di avviso del log che usano `search` `union` gli operatori o, che prevedono query tra risorse.

La query di avviso seguente, ad esempio, ha come ambito la tabella _SecurityEvent_ e cerca un ID evento specifico. Si tratta dell'unica tabella che la query deve elaborare.

``` Kusto
SecurityEvent
| where EventID == 4624
```

Le regole di avviso del log che usano [query tra risorse](../log-query/cross-workspace-query.md) non sono interessate da questa modifica, poiché le query tra risorse usano un tipo di `union` , che limita l'ambito della query a risorse specifiche. L'esempio seguente è una query di avviso del log valida:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> Le [query tra risorse](../log-query/cross-workspace-query.md) sono supportate nella nuova [API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Se si usa ancora l' [API legacy log Analytics Alert](api-alerts.md) per creare gli avvisi del log, è possibile ottenere informazioni [sul cambio.](alerts-log-api-switch.md)

## <a name="examples"></a>Esempi
Gli esempi seguenti includono query di log che usano `search` e `union` e forniscono passaggi che è possibile usare per modificare queste query da usare nelle regole di avviso.

### <a name="example-1"></a>Esempio 1
Si desidera creare una regola di avviso del log utilizzando la query seguente che recupera le informazioni sulle prestazioni utilizzando `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Per modificare questa query, iniziare usando la query seguente per identificare la tabella a cui appartengono le proprietà:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

Il risultato di questa query indicherebbe che la proprietà _CounterName_ proviene dalla tabella _Perf_.

È possibile utilizzare questo risultato per creare la query seguente che verrà utilizzata per la regola di avviso:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Esempio 2
Si desidera creare una regola di avviso del log utilizzando la query seguente che recupera le informazioni sulle prestazioni utilizzando `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Per modificare questa query, iniziare usando la query seguente per identificare la tabella a cui appartengono le proprietà:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

Il risultato di questa query indicherebbe che le proprietà _ObjectName_ e _CounterName_ provengono dalla tabella _Perf_.

È possibile utilizzare questo risultato per creare la query seguente che verrà utilizzata per la regola di avviso:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Esempio 3

Si vuole creare una regola di avviso del log usando la query seguente che usa sia che `search` `union` per recuperare le informazioni sulle prestazioni: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Per modificare questa query, iniziare usando la query seguente per identificare la tabella a cui appartengono le proprietà nella prima parte della query: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

Il risultato di questa query indicherebbe che tutte queste proprietà provengono dalla tabella _Perf_.

A questo punto usare `union` con il comando `withsource` per identificare la tabella di origine da cui proviene ogni riga.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

Il risultato di questa query indicherebbe che anche queste proprietà provengono dalla tabella _Perf_.

È possibile utilizzare questi risultati per creare la query seguente che verrà utilizzata per la regola di avviso: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Esempio 4
Si desidera creare una regola di avviso del log utilizzando la query seguente che unisce i risultati di due `search` query:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Per modificare questa query, iniziare usando la query seguente per identificare la tabella che contiene le proprietà nel lato sinistro del join: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

Il risultato indica che le proprietà nel lato sinistro del join appartengono alla tabella _SecurityEvent_. 

Ora è possibile usare la query seguente per identificare la tabella che contiene le proprietà sul lato destro del join: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
Il risultato indica che le proprietà sul lato destro del join appartengono alla tabella _heartbeat_ .

È possibile utilizzare questi risultati per creare la query seguente che verrà utilizzata per la regola di avviso: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sugli [avvisi del log](alerts-log.md) in Monitoraggio di Azure.
- Informazioni sulle [query nei log](../log-query/log-query-overview.md).
