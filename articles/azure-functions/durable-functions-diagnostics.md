---
title: Diagnostica in Funzioni permanenti - Azure
description: Informazioni su come eseguire la diagnostica dei problemi con l'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5ebab8660dfe21984e1a7f9a1cb925aea60de213
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnostica in Funzioni permanenti (Funzioni di Azure)

Per la diagnostica dei problemi con [Funzioni permanenti](durable-functions-overview.md) sono disponibili diverse opzioni. Alcune opzioni sono le stesse disponibili per le funzioni normali e alcune sono specifiche di Funzioni permanenti.

## <a name="application-insights"></a>Application Insights

Per eseguire la diagnostica e il monitoraggio in Funzioni di Azure, è consigliabile usare [Application Insights](../application-insights/app-insights-overview.md). Lo stesso consiglio vale per Funzioni permanenti. Per una panoramica su come usare Application Insights nell'app per le funzioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

L'estensione Funzioni permanenti di Funzioni di Azure genera anche *eventi di rilevamento* che consentono di tenere traccia dell'esecuzione end-to-end di un'orchestrazione. È possibile individuarli ed eseguire query su di essi usando lo strumento [Application Insights Analytics](../application-insights/app-insights-analytics.md) nel portale di Azure.

### <a name="tracking-data"></a>Dati di rilevamento

Ogni evento del ciclo di vita di un'istanza di orchestrazione genera la scrittura di un evento di rilevamento nella raccolta **traces** in Application Insights. Questo evento contiene un payload **customDimensions** con diversi campi.  Ai nomi dei campi viene anteposta la stringa `prop__`.

* **hubName**: il nome dell'hub attività in cui vengono eseguite le orchestrazioni.
* **appName**: il nome dell'app per le funzioni. Questo campo è utile quando più app per le funzioni condividono la stessa istanza di Application Insights.
* **slotName**: [slot di distribuzione](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) in cui è in esecuzione l'app per le funzioni corrente. Questo campo è utile quando si usano gli slot di distribuzione per controllare le versioni delle orchestrazioni.
* **functionName**: il nome della funzione dell'agente di orchestrazione o di attività.
* **functionType**: il tipo di funzione, ad esempio **agente di orchestrazione** o **attività**.
* **instanceId**: l'ID univoco dell'istanza di orchestrazione.
* **state**: lo stato di esecuzione del ciclo di vita dell'istanza. I valori validi includono:
    * **Scheduled**: la funzione è stata pianificata per l'esecuzione, ma non è stata ancora avviata.
    * **Started**: la funzione è stata avviata, ma non è ancora in stato di attesa o non è stata completata.
    * **Awaited**: l'agente di orchestrazione ha pianificato un lavoro ed è in attesa del completamento.
    * **Listening**: l'agente di orchestrazione è in ascolto di una notifica degli eventi esterni.
    * **Completed**: la funzione è stata completata.
    * **Failed**: la funzione ha avuto esito negativo generando un errore.
* **reason**: dati aggiuntivi associati all'evento di rilevamento. Ad esempio, se un'istanza è in attesa di una notifica degli eventi esterni, questo campo indica il nome dell'evento di cui è in attesa. Se una funzione ha avuto esito negativo, il campo contiene i dettagli dell'errore.
* **isReplay**: valore booleano che indica se per l'evento di rilevamento è prevista la riesecuzione.
* **extensionVersion**: la versione dell'estensione Durable Task. Questi dati sono particolarmente importanti quando si segnalano possibili bug nell'estensione. Le istanze con esecuzione prolungata possono segnalare più versioni, se si verifica un aggiornamento durante l'esecuzione. 

Il livello di dettaglio dei dati di rilevamento generati ad Application Insights può essere configurato nella sezione `logger` del file `host.json`.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

Per impostazione predefinita, vengono generati tutti gli eventi di rilevamento. Il volume dei dati può essere ridotto impostando `Host.Triggers.DurableTask` su `"Warning"` o `"Error"`; in questo caso gli eventi di rilevamento verranno generati solo per le situazioni eccezionali.

> [!WARNING]
> Per impostazione predefinita, la telemetria di Application Insights viene campionata dal runtime di Funzioni di Azure per evitare di generare dati con eccessiva frequenza. In questo modo è possibile che le informazioni di rilevamento vengano perse quando si verificano molti eventi del ciclo di vita in un breve periodo di tempo. L'[articolo sul monitoraggio in Funzioni di Azure](functions-monitoring.md#configure-sampling) illustra come configurare questo comportamento.

### <a name="single-instance-query"></a>Query per singola istanza

La query seguente mostra i dati di rilevamento cronologici per una singola istanza di orchestrazione della funzione [Hello Sequence](durable-functions-sequence.md). È scritta con [Application Insights Query Language (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Esclude la riesecuzione in modo da mostrare solo il percorso di esecuzione *logico*.

```AIQL
let targetInstanceId = "bf71335b26564016a93860491aa50c7f";
let start = datetime(2017-09-29T00:00:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| where isReplay == false
| where instanceId == targetInstanceId
| project timestamp, functionName, state, instanceId, appName = cloud_RoleName
```
Il risultato è un elenco di eventi di rilevamento che mostrano il percorso di esecuzione dell'orchestrazione, incluse eventuali funzioni di attività.

![Query di Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-query.png)

> [!NOTE]
> Alcuni di questi eventi di rilevamento potrebbero non essere nell'ordine corretto per la mancanza di precisione nella colonna `timestamp`. Questo aspetto è in corso di rilevamento in GitHub come [Issue 71](https://github.com/Azure/azure-functions-durable-extension/issues/71).

### <a name="instance-summary-query"></a>Query di riepilogo dell'istanza

La query seguente mostra lo stato di tutte le istanze di orchestrazione eseguite in un intervallo di tempo specificato.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
Il risultato è un elenco di ID istanza e dello stato di runtime corrente.

![Query di Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registrazione

È importante tenere presente il comportamento di riesecuzione dell'agente di orchestrazione quando si scrivono i log direttamente da una funzione dell'agente di orchestrazione. Ad esempio, si consideri la seguente funzione dell'agente di orchestrazione:

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

I dati di log risultanti eseguono una ricerca simile alla seguente:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Tenere presente che mentre i registri contengono la dichiarazione di chiamata F1, F2 e F3, queste funzioni vengono chiamate *effettivamente* solo al primo rilevamento. Le chiamate successive eseguite durante la riesecuzione vengono ignorate e gli output vengono rieseguiti nella logica dell'agente di orchestrazione.

Se si vuole eseguire la registrazione solo con l'esecuzione senza riesecuzione, è possibile scrivere un'espressione condizionale per eseguire la registrazione solo se `IsReplaying` è `false`. Si consideri l'esempio precedente, ma questa volta con i controlli di riesecuzione.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Con questa modifica, l'output del log è il seguente:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="debugging"></a>Debug

Funzioni di Azure supporta direttamente il debug del codice della funzione e lo stesso supporto si estende a Funzioni permanenti, in esecuzione in Azure o in locale. Quando si esegue il debug, è tuttavia opportuno conoscere alcuni comportamenti:

* **Replay**: le funzioni dell'agente di orchestrazione vengono rieseguite regolarmente alla ricezione di nuovi input. Ciò implica che una singola esecuzione *logica* di una funzione dell'agente di orchestrazione può comportare l'accesso più volte allo stesso punto di interruzione, soprattutto se è impostata all'inizio del codice della funzione.
* **Await**: ogni volta che viene rilevato `await`, il controllo viene restituito al dispatcher di Durable Task Framework. Se è la prima volta che uno specifico `await` è stato rilevato, l'attività associata non viene ripresa *mai*. Poiché l'attività non viene ripresa mai, non è possibile eseguire lo *step over* per await (F10 in Visual Studio). Questa operazione funziona solo se un'attività è in corso di riesecuzione.
* **Timeout di messaggistica**: Funzioni permanenti usa internamente i messaggi in coda per gestire l'esecuzione delle funzioni sia di orchestrazione sia di attività. In un ambiente con più macchine virtuali, a causa dell'interruzione del debug per lunghi periodi di tempo è possibile che un'altra macchina virtuale prelevi il messaggio, generando un'esecuzione duplicata. Questo comportamento esiste anche per le normali funzioni attivate da coda, ma è importante sottolinearlo in questo contesto poiché le code sono un dettaglio dell'implementazione.

> [!TIP]
> Quando si impostano i punti di interruzione, se si vuole impostare il punto di interruzione solo su un'esecuzione senza riesecuzione, è possibile impostare un punto di interruzione condizionale con arresto solo se `IsReplaying` è `false`.

## <a name="storage"></a>Archiviazione

Per impostazione predefinita, Funzioni permanenti archivia lo stato in Archiviazione di Azure. È quindi possibile controllare lo stato delle orchestrazioni tramite strumenti come [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Schermata di Azure Storage Explorer](media/durable-functions-diagnostics/storage-explorer.png)

Risulta utile per il debug perché viene visualizzato esattamente lo stato in cui potrebbe trovarsi un'orchestrazione. È possibile esaminare i messaggi nelle code anche per individuare le attività in sospeso o, in alcuni casi, bloccate.

> [!WARNING]
> Sebbene sia utile esaminare la cronologia di esecuzioni in archiviazione tabelle, evitare di creare qualsiasi dipendenza in questa tabella, che può cambiare con l'evoluzione dell'estensione Funzioni permanenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare i timer permanenti](durable-functions-timers.md)