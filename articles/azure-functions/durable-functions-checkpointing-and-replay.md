---
title: Checkpoint e riesecuzione in Funzioni permanenti - Azure
description: Informazioni sul funzionamento dei checkpoint e della riesecuzione nell'estensione Funzioni permanenti di Funzioni di Azure.
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
ms.openlocfilehash: 28c589b6821526fa6b91dc558a08ef2fb68f97df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Checkpoint e riesecuzione in Funzioni permanenti (Funzioni di Azure)

Una delle caratteristiche principali di Funzioni permanenti è un'**esecuzione affidabile**. Le funzioni dell'agente di orchestrazione e di attività possono essere eseguite in macchine virtuali diverse in un data center e l'affidabilità di tali macchine virtuali o dell'infrastruttura di rete sottostante potrebbe non essere completa.

L'estensione Funzioni permanenti garantisce comunque un'esecuzione affidabile delle orchestrazioni grazie all'uso di code di archiviazione per chiamare le funzioni e all'impostazione periodica di checkpoint per la cronologia di esecuzione in tabelle di archiviazione tramite uno schema progettuale cloud noto come [Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)(origine eventi). Tale cronologia può quindi essere rieseguita per ricompilare automaticamente lo stato in memoria di una funzione dell'agente di orchestrazione.

## <a name="orchestration-history"></a>Cronologia di orchestrazione

Si supponga di avere la funzione seguente dell'agente di orchestrazione.

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

In ogni istruzione `await` il framework di attività permanenti imposta un checkpoint dello stato di esecuzione della funzione nella tabella di archiviazione. Questo stato viene definito come *cronologia di orchestrazione*.

## <a name="history-table"></a>Tabella di cronologia

A ogni checkpoint, il framework di attività permanenti esegue in genere queste operazioni:

1. Salva la cronologia di esecuzione nelle tabelle di Archiviazione di Azure.
2. Accoda i messaggi per le funzioni che l'agente di orchestrazione intende richiamare.
3. Accoda i messaggi per l'agente di orchestrazione &mdash; ad esempio i messaggi di timer permanenti.

Dopo aver completato l'impostazione dei checkpoint, la funzione dell'agente di orchestrazione può essere rimossa dalla memoria fino a quando non siano ancora presenti operazioni che deve eseguire.

> [!NOTE]
> Archiviazione di Azure non offre alcuna garanzia durante le transazioni tra il salvataggio dei dati in code e in tabelle di archiviazione. Per gestire gli errori, il provider di archiviazione di Funzioni permanenti usa modelli di *coerenza finale*. Tali modelli garantiscono che non viene perso alcun dato se si verifica un arresto anomalo del sistema o la perdita di connettività durante un checkpoint.

Al termine dell'operazione, la cronologia della funzione illustrata in precedenza ha un aspetto simile al seguente nell'archiviazione tabelle di Azure (abbreviata a scopo illustrativo):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Input | Nome             | Risultato                                                    | Stato | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | Null  | E1_HelloSequence |                                                           |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     | 
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     | 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     | 
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completed           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Di seguito vengono indicate alcune note sui valori di colonna.
* **PartitionKey**: contiene l'ID istanza dell'orchestrazione.
* **EventType**: rappresenta uno dei tipi di evento indicati di seguito.
    * **OrchestrationStarted**: funzione dell'agente di orchestrazione ripresa da un'istruzione await oppure in esecuzione per la prima volta. La colonna `Timestamp` viene usata per popolare il valore deterministico per l'API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime).
    * **ExecutionStarted**: esecuzione della funzione dell'agente di orchestrazione avviata per la prima volta. Questo evento contiene anche l'input della funzione nella colonna `Input`.
    * **TaskScheduled**: funzione di attività pianificata. Il nome della funzione di attività viene acquisito nella colonna `Name`.
    * **TaskCompleted**: funzione di attività completata. Il risultato della funzione è nella colonna `Result`.
    * **TimerCreated**: timer permanente creato. La colonna `FireAt` contiene l'ora UTC pianificata per la scadenza del timer.
    * **TimerFired**: un timer permanente scaduto.
    * **EventRaised**: evento esterno inviato all'istanza di orchestrazione. La colonna `Name` acquisisce il nome dell'evento, mentre la colonna `Input` ne acquisisce il payload.
    * **OrchestratorCompleted**: funzione dell'agente di orchestrazione in attesa.
    * **ContinueAsNew**: la funzione dell'agente di orchestrazione completata e riavviata con un nuovo stato. La colonna `Result` contiene il valore che viene usato come input nell'istanza riavviata.
    * **ExecutionCompleted**: funzione dell'agente di orchestrazione stata eseguita fino al completamento (oppure non riuscita). Gli output della funzione o i dettagli dell'errore vengono archiviati nella colonna `Result`.
* **Timestamp**: timestamp UTC dell'evento di cronologia.
* **Name**: nome della funzione richiamata.
* **Input**: input in formato JSON della funzione.
* **Output**: output della funzione, ovvero il valore restituito.

> [!WARNING]
> Benché sia utile come strumento di debug, usare con cautela questa tabella che può cambiare con l'evoluzione dell'estensione Funzioni permanenti.

Ogni volta che la funzione viene ripresa da un'istruzione `await`, il framework di attività permanenti riesegue la funzione dell'agente di orchestrazione a partire dall'inizio. A ogni riesecuzione, il framework esamina la cronologia di esecuzione per determinare se l'operazione asincrona corrente è stata effettuata.  In caso affermativo, il framework riesegue immediatamente l'output dell'operazione e passa alla successiva istruzione `await`. Questo processo continua fino a quando l'intera cronologia è stata rieseguita e a quel punto tutte le variabili locali nella funzione dell'agente di orchestrazione vengono ripristinate in base ai valori precedenti.

## <a name="orchestrator-code-constraints"></a>Vincoli del codice dell'agente di orchestrazione

Questo comportamento di riesecuzione crea vincoli sul tipo di codice che è possibile scrivere in una funzione dell'agente di orchestrazione.

* Il codice dell'agente di orchestrazione deve essere **deterministico**, verrà rieseguito più volte e deve restituire ogni volta lo stesso risultato. Il codice, ad esempio, non può eseguire chiamate dirette per ottenere la data e l'ora correnti o numeri casuali, per generare GUID casuali o per chiamare endpoint remoti.

  Se il codice dell'agente di orchestrazione deve ottenere la data e l'ora correnti, deve usare l'API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime), che consente una riesecuzione sicura.

  Le operazioni non deterministiche devono essere eseguite nelle funzioni di attività. Sono incluse anche tutte le interazioni con altre associazioni di input oppure output. In questo modo i valori non deterministici vengono generati una volta alla prima esecuzione e salvati nella cronologia di esecuzione. Nelle esecuzioni successive verrà quindi usato automaticamente il valore salvato.

* Il codice dell'agente di orchestrazione deve essere **non bloccante**. Ciò significa che non può usare `Thread.Sleep` né API equivalenti.

  Se l'agente di orchestrazione deve essere ritardato, è possibile usare l'API [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_).

* Il codice dell'agente di orchestrazione **non deve mai avviare un'operazione asincrona**, a meno che non usi l'API [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Non può usare, ad esempio, `Task.Run`, `Task.Delay` né `HttpClient.SendAsync`. Il framework di attività permanenti esegue il codice dell'agente di orchestrazione in un singolo thread e non può interagire con alcun altro thread che possa essere pianificato da altre API asincrone.

* Nel codice dell'agente di orchestrazione **devono essere evitati i cicli infiniti**. Poiché il framework di attività permanenti salva la cronologia di esecuzione durante l'avanzamento della funzione di orchestrazione, un ciclo infinito potrebbe causare un problema di memoria insufficiente per un'istanza dell'agente di orchestrazione. Per scenari che prevedono la presenza di cicli infiniti, usare API come [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) per riavviare l'esecuzione della funzione e rimuovere la cronologia di esecuzione precedente.

Sebbene questi vincoli possano sembrare stringenti, non sono difficili da usare. Il framework di attività permanenti tenta di rilevare le violazioni delle regole indicate in precedenza e genera un'eccezione `NonDeterministicOrchestrationException`. Questa operazione di rilevamento è tuttavia onerosa ed è consigliabile non usarla spesso.

> [!NOTE]
> Tutte queste regole si applicano solo a funzioni attivate dall'associazione `orchestrationTrigger`. Le funzioni di attività attivato dall'associazione `activityTrigger` e le funzioni che usano l'associazione `orchestrationClient` non sono vincolate da tali limitazioni.

## <a name="durable-tasks"></a>Attività permanenti

> [!NOTE]
> Questa sezione descrive i dettagli di implementazione interna del framework di attività permanenti. È possibile usare funzioni permanenti senza conoscere queste informazioni che vengono date al solo scopo di comprendere il comportamento di riesecuzione.

Le attività che possono essere messe in attesa in modo sicuro nelle funzioni dell'agente di orchestrazione vengono talvolta definite come *attività permanenti*. Si tratta di attività create e gestite dal framework di attività permanenti. Esempi di tali l'attività sono quelle restituite da `CallActivityAsync`, `WaitForExternalEvent` e `CreateTimer`.

Tali *attività permanenti* vengono gestite internamente tramite un elenco di oggetti `TaskCompletionSource`. Durante la riesecuzione queste attività vengono create come parte dell'esecuzione di codice dell'agente di orchestrazione e vengono completate quando il dispatcher enumera gli eventi di cronologia corrispondenti. Questa operazione viene eseguita in modo sincrono usando un singolo thread fino a quando tutta la cronologia non viene riprodotta. Tutte le attività permanenti non completate entro la fine della riesecuzione della cronologia sono associate ad azioni appropriate. Un messaggio ad esempio può essere accodata per chiamare una funzione di attività.

Il comportamento di esecuzione descritto consente di comprendere i motivi per cui il codice della funzione dell'agente di orchestrazione non deve mai eseguire un'istruzione `await` per un'attività permanente. Il thread del dispatcher infatti non può attendere il completamento e i callback eseguiti da tale attività potrebbero danneggiare lo stato di rilevamento stato della funzione dell'agente di orchestrazione. Per tentare di evitare questo problema, sono disponibili alcuni controlli di runtime.

Se si desiderano altre informazioni sul modo in cui il framework di attività permanenti esegue le funzioni dell'agente di orchestrazione, vedere il [codice sorgente delle attività permanenti in GitHub](https://github.com/Azure/durabletask), in particolare [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) e [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulla gestione delle istanze](durable-functions-instance-management.md)