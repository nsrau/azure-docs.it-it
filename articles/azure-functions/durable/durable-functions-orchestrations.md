---
title: Orchestrazioni durevoli - Funzioni di Azure
description: Introduzione alla funzionalità di orchestrazione per Durable Functions di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 82c4a27ac2491e668c1d99e2a14b870e82ec5665
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935419"
---
# <a name="durable-orchestrations"></a>Orchestrazioni durevoli

Durable Functions è un'estensione di [Funzioni di Azure](../functions-overview.md). È possibile usare una *funzione di orchestrazione* per orchestrare l'esecuzione di altre istanze di Durable Functions all'interno di un'app per le funzioni. Le funzioni dell'agente di orchestrazione hanno le caratteristiche seguenti:

* Le funzioni dell'agente di orchestrazione definiscono i flussi di lavoro delle funzioni tramite codice procedurale. Non sono necessari schemi dichiarativi o finestre di progettazione.
* Le funzioni dell'agente di orchestrazione possono chiamare altre funzioni durevoli in modo sincrono e asincrono. L'output delle funzioni chiamate può essere salvato in modo affidabile in variabili locali.
* Le funzioni dell'agente di orchestrazione sono durevoli e affidabili. Lo stato di avanzamento dell'esecuzione viene automaticamente sottoposto a checkpoint quando la funzione è in attesa o è sospesa. Lo stato locale non va mai perso quando il processo viene riciclato o la macchina virtuale viene riavviata.
* Le funzioni dell'agente di orchestrazione possono essere a esecuzione prolungata. La durata totale di un'*istanza di orchestrazione* può essere di alcuni secondi, giorni, mesi o infinita.

Questo articolo contiene una panoramica delle funzioni dell'agente di orchestrazione, con informazioni su come usarle per risolvere vari problemi legati allo sviluppo di app. Se non si ha familiarità con i tipi di funzioni disponibili in un'app di Durable Functions, vedere prima l'articolo [Tipi di app di Durable Functions](durable-functions-types-features-overview.md).

## <a name="orchestration-identity"></a>Identità dell'orchestrazione

Ogni *istanza* di un'orchestrazione include un identificatore di istanza (*ID istanza*). Per impostazione predefinita, ogni ID istanza è un GUID generato automaticamente. Tuttavia, gli ID istanza possono anche essere qualsiasi valore di stringa generato dall'utente. Ogni ID istanza di orchestrazione deve essere univoco all'interno di un [hub attività](durable-functions-task-hubs.md).

Di seguito sono riportate alcune regole sugli ID istanza:

* Gli ID istanza devono essere composti da un numero di caratteri compreso tra 1 e 256.
* Gli ID istanza non possono iniziare con `@`.
* Gli ID istanza non possono contenere i caratteri `/`, `\`, `#` o `?`.
* Gli ID istanza non possono contenere caratteri di controllo.

> [!NOTE]
> In generale, è consigliabile usare ID istanza generati automaticamente laddove possibile. Gli ID istanza generati dall'utente sono destinati a scenari in cui esiste un mapping uno a uno tra un'istanza di orchestrazione e un'entità esterna specifica dell'applicazione, come un ordine di acquisto o un documento.

L'ID istanza di un'orchestrazione è un parametro obbligatorio per la maggior parte delle [operazioni di gestione di istanze](durable-functions-instance-management.md). È anche importante per la diagnostica, ad esempio per le [ricerche all'interno dei dati di tracciabilità dell'orchestrazione](durable-functions-diagnostics.md#application-insights) in Application Insights a scopi di analisi o risoluzione dei problemi. Per questo motivo, è consigliabile salvare gli ID istanza generati in una posizione esterna, ad esempio in un database o nei log applicazioni, in cui è facile farvi riferimento in seguito.

## <a name="reliability"></a>Affidabilità

Le funzioni dell'agente di orchestrazione mantengono in modo affidabile il proprio stato di esecuzione tramite il modello progettuale [Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Invece di archiviare direttamente lo stato corrente di un'orchestrazione, Durable Task Framework usa un archivio di solo accodamento per registrare la serie completa di azioni eseguite dall'orchestrazione di funzioni. Rispetto al "dump" dello stato del runtime completo, l'archivio di solo accodamento presenta diversi vantaggi, in termini di prestazioni, scalabilità e reattività. Inoltre, consente di ottenere coerenza finale per i dati transazionali, oltre a dati di cronologia e audit trail completi, che supportano azioni di compensazione affidabili.

Durable Functions usa il modello Event Sourcing in modo trasparente. Dietro le quinte, l'operatore `await` (C#) o `yield` (JavaScript) in una funzione di orchestrazione restituisce il controllo del thread di orchestrazione al dispatcher di Durable Task Framework. Il dispatcher quindi esegue il commit di eventuali nuove azioni pianificate dalla funzione di orchestrazione (ad esempio, la chiamata di una o più funzioni figlio o la pianificazione di un timer permanente) in un archivio. Questa azione di commit trasparente viene accodata alla cronologia di esecuzione dell'istanza di orchestrazione. La cronologia viene archiviata in una tabella di archiviazione. L'azione di commit aggiunge quindi messaggi a una coda per pianificare le operazioni effettive. A questo punto, la funzione di orchestrazione può essere scaricata dalla memoria.

Quando a una funzione di orchestrazione vengono assegnate altre operazioni da eseguire (ad esempio, si riceve un messaggio di risposta oppure un timer durevole scade), l'agente di orchestrazione si riattiva ed esegue nuovamente l'intera funzione dall'inizio per ricreare lo stato locale. Se durante la riesecuzione il codice tenta di chiamare una funzione o di eseguire un'altra operazione asincrona, Durable Task Framework esamina la cronologia di esecuzione dell'orchestrazione corrente. Se rileva che la [funzione di attività](durable-functions-types-features-overview.md#activity-functions) è già stata eseguita e ha restituito un risultato, riesegue il risultato della funzione, mentre il codice dell'agente di orchestrazione continua a essere eseguito. La riesecuzione prosegue fino al termine del codice della funzione oppure finché non viene pianificata una nuova operazione asincrona.

> [!NOTE]
> Per il funzionamento corretto e affidabile del modello di riesecuzione, il codice della funzione dell'agente di orchestrazione deve essere *deterministico*. Per altre informazioni sulle restrizioni del codice per le funzioni dell'agente di orchestrazione, vedere l'argomento sui [vincoli di codice delle funzioni dell'agente di orchestrazione](durable-functions-code-constraints.md).

> [!NOTE]
> Se una funzione dell'agente di orchestrazione emette messaggi di log, la riesecuzione potrebbe causare l'emissione di duplicati. Per altre informazioni sui motivi per cui si verifica questo comportamento e su come correggerlo, vedere l'argomento [Registrazione](durable-functions-diagnostics.md#logging).

## <a name="orchestration-history"></a>Cronologia di orchestrazione

Il comportamento di Event Sourcing di Durable Task Framework è strettamente collegato al codice delle funzioni dell'agente di orchestrazione che si scrive. Si supponga di avere una funzione dell'agente di orchestrazione per il concatenamento di attività, come l'esempio seguente in C#:

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

Se si scrive codice in JavaScript, la funzione dell'agente di orchestrazione per il concatenamento di attività sarà simile all'esempio di codice seguente:

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

In ogni istruzione `await` (C#) o `yield` (JavaScript) Durable Task Framework imposta un checkpoint dello stato di esecuzione della funzione in un back-end di archiviazione durevole, solitamente archiviazione tabelle di Azure. Questo stato viene definito come *cronologia di orchestrazione*.

### <a name="history-table"></a>Tabella di cronologia

A ogni checkpoint, il framework di attività permanenti esegue in genere queste operazioni:

1. Salva la cronologia di esecuzione nelle tabelle di Archiviazione di Azure.
2. Accoda i messaggi per le funzioni che l'agente di orchestrazione intende richiamare.
3. Accoda i messaggi per l'agente di orchestrazione &mdash; ad esempio i messaggi di timer permanenti.

Dopo aver completato l'impostazione dei checkpoint, la funzione dell'agente di orchestrazione può essere rimossa dalla memoria fino a quando non siano ancora presenti operazioni che deve eseguire.

> [!NOTE]
> Archiviazione di Azure non offre alcuna garanzia durante le transazioni tra il salvataggio dei dati in code e in tabelle di archiviazione. Per gestire gli errori, il provider di archiviazione di Funzioni permanenti usa modelli di *coerenza finale*. Tali modelli garantiscono che non viene perso alcun dato se si verifica un arresto anomalo del sistema o la perdita di connettività durante un checkpoint.

Al termine dell'operazione, la cronologia della funzione illustrata in precedenza ha un aspetto simile al seguente nell'archiviazione tabelle di Azure (abbreviata a scopo illustrativo):

| PartitionKey (InstanceId)                     | EventType             | Timestamp               | Input | NOME             | Risultato                                                    | Stato |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
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
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Completi           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Di seguito vengono indicate alcune note sui valori di colonna.

* **PartitionKey**: contiene l'ID istanza dell'orchestrazione.
* **EventType**: rappresenta uno dei tipi di evento indicati di seguito.
  * **OrchestrationStarted**: funzione dell'agente di orchestrazione ripresa da un'istruzione await oppure in esecuzione per la prima volta. La colonna `Timestamp` viene usata per popolare il valore deterministico per l'API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime).
  * **ExecutionStarted**: esecuzione della funzione dell'agente di orchestrazione avviata per la prima volta. Questo evento contiene anche l'input della funzione nella colonna `Input`.
  * **TaskScheduled**: una funzione di attività pianificata. Il nome della funzione di attività viene acquisito nella colonna `Name`.
  * **TaskCompleted**: una funzione di attività completata. Il risultato della funzione è nella colonna `Result`.
  * **TimerCreated**: un timer permanente creato. La colonna `FireAt` contiene l'ora UTC pianificata per la scadenza del timer.
  * **TimerFired**: un timer permanente attivato.
  * **EventRaised**: evento esterno inviato all'istanza di orchestrazione. La colonna `Name` acquisisce il nome dell'evento, mentre la colonna `Input` ne acquisisce il payload.
  * **OrchestratorCompleted**: la funzione dell'agente di orchestrazione attesa.
  * **ContinueAsNew**: la funzione dell'agente di orchestrazione completata e riavviata con un nuovo stato. La colonna `Result` contiene il valore che viene usato come input nell'istanza riavviata.
  * **ExecutionCompleted**: la funzione dell'agente di orchestrazione eseguita fino al completamento (oppure non riuscita). Gli output della funzione o i dettagli dell'errore vengono archiviati nella colonna `Result`.
* **Timestamp**: timestamp UTC dell'evento di cronologia.
* **Name**: nome della funzione richiamata.
* **Input**: l'input in formato JSON della funzione.
* **Result**: l'output della funzione, ovvero il valore restituito.

> [!WARNING]
> Benché sia utile come strumento di debug, usare con cautela questa tabella che può cambiare con l'evoluzione dell'estensione Funzioni permanenti.

Ogni volta che la funzione viene ripresa da un'istruzione `await` (C#) o `yield` (JavaScript), il framework di attività permanenti riesegue la funzione dell'agente di orchestrazione a partire dall'inizio. A ogni riesecuzione, il framework esamina la cronologia di esecuzione per determinare se l'operazione asincrona corrente è stata effettuata.  In caso affermativo, il framework riesegue immediatamente l'output dell'operazione e passa alla successiva istruzione `await` (C#) o `yield` (JavaScript). Questo processo continua fino alla riesecuzione dell'intera cronologia. Una volta rieseguita la cronologia corrente, le variabili locali saranno state ripristinate sui rispettivi valori precedenti.

## <a name="features-and-patterns"></a>Funzionalità e criteri

Le sezioni successive descrivono le funzionalità e i modelli delle funzioni dell'agente di orchestrazione.

### <a name="sub-orchestrations"></a>Orchestrazioni secondarie

Oltre a chiamare le funzioni di attività, le funzioni dell'agente di orchestrazione possono chiamare anche altre funzioni dell'agente di orchestrazione. È possibile ad esempio compilare un'orchestrazione più grande da una raccolta di funzioni dell'agente di orchestrazione. Oppure è possibile eseguire più istanze di una funzione dell'agente di orchestrazione in parallelo.

Per altre informazioni e per alcuni esempi, vedere l'articolo sulle [orchestrazioni secondarie](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Timer durevoli

Le orchestrazioni possono pianificare *timer durevoli* per implementare ritardi oppure per configurare la gestione dei timeout per azioni asincrone. Usare timer durevoli nelle funzioni dell'agente di orchestrazione invece di `Thread.Sleep` e `Task.Delay` (C#) oppure di `setTimeout()` e `setInterval()` (JavaScript).

Per altre informazioni e per alcuni esempi, vedere l'articolo sui [timer durevoli](durable-functions-timers.md).

### <a name="external-events"></a>Eventi esterni

Le funzioni di orchestrazione possono attendere eventi esterni per aggiornare un'istanza di orchestrazione. Questa funzionalità di Durable Functions è spesso utile per gestire l'interazione umana o altri callback esterni.

Per altre informazioni e per alcuni esempi, vedere l'articolo sugli [eventi esterni](durable-functions-external-events.md).

### <a name="error-handling"></a>Gestione degli errori

Le funzioni dell'agente di orchestrazione possono usare le funzionalità di gestione degli errori del linguaggio di programmazione. Gli attuali modelli come `try`/`catch` sono supportati nel codice di orchestrazione.

Le funzioni dell'agente di orchestrazione possono anche aggiungere criteri di ripetizione alle funzioni di attività o dell'agente di orchestrazione secondario che chiamano. Se una funzione di attività o dell'agente di orchestrazione secondario restituisce un errore con un'eccezione, il criterio di ripetizione specificato può ritardare e ripetere automaticamente l'esecuzione fino a un numero specificato di volte.

> [!NOTE]
> Se si verifica un'eccezione non gestita in una funzione dell'agente di orchestrazione, l'istanza di orchestrazione verrà completata con lo stato `Failed`. Le istanze di orchestrazione che non riescono non possono essere ripetute.

Per altre informazioni e per alcuni esempi, vedere l'[articolo sulla gestione degli errori](durable-functions-error-handling.md).

### <a name="critical-sections"></a>Sezioni critiche

Le istanze di orchestrazione sono a thread singolo, quindi non bisogna preoccuparsi di eventuali race condition *all'interno* di un'orchestrazione. Tuttavia, le race condition sono possibili quando le orchestrazioni interagiscono con sistemi esterni. Per mitigare le race condition durante l'interazione con sistemi esterni, le funzioni dell'agente di orchestrazione definiscono *sezioni critiche* usando un metodo `LockAsync` in .NET.

L'esempio di codice seguente mostra una funzione dell'agente di orchestrazione che definisce una sezione critica. Accede alla sezione critica usando il metodo `LockAsync`. Questo metodo richiede il passaggio di uno o più riferimenti a un'[entità durevole](durable-functions-entities.md), che gestisce in modo durevole lo stato di blocco. Il codice della sezione critica può essere eseguito solo da una singola istanza di questa orchestrazione alla volta.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

Il metodo `LockAsync` acquisisce i blocchi durevoli e restituisce `IDisposable` che termina la sezione critica all'eliminazione. Questo risultato `IDisposable` può essere usato insieme a un blocco `using` per ottenere una rappresentazione sintattica della sezione critica. Quando una funzione dell'agente di orchestrazione accede a una sezione critica, questo blocco di codice può essere eseguito da una sola istanza. Tutte le altre istanze che provano ad accedere alla sezione critica verranno bloccate finché l'istanza precedente non ne esce.

La funzionalità delle sezioni critiche è anche utile per coordinare le modifiche delle entità durevoli. Per altre informazioni sulle sezioni critiche, vedere l'argomento sul [coordinamento di entità durevoli](durable-functions-entities.md#entity-coordination).

> [!NOTE]
> Le sezioni critiche sono disponibili in Durable Functions 2.0 e versioni successive. Attualmente, questa funzionalità è implementata solo in orchestrazioni .NET.

### <a name="calling-http-endpoints"></a>Chiamata agli endpoint HTTP

Le funzioni dell'agente di orchestrazione non possono eseguire operazioni di I/O, come descritto nell'articolo sui [vincoli di codice delle funzioni dell'agente di orchestrazione](durable-functions-code-constraints.md). La tipica soluzione alternativa per questa limitazione consiste nell'eseguire il wrapping del codice che deve svolgere operazioni di I/O in una funzione di attività. Le orchestrazioni che interagiscono con sistemi esterni usano spesso funzioni di attività per effettuare chiamate HTTP e restituire il risultato.

Per semplificare questo modello comune, le funzioni dell'agente di orchestrazione possono usare il metodo `CallHttpAsync` in .NET per richiamare direttamente le API HTTP. Oltre a modelli di richiesta/risposta di base, `CallHttpAsync` supporta la gestione automatica di comuni modelli di polling HTTP 202 asincroni, nonché l'autenticazione con servizi esterni tramite [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md).

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

Per altre informazioni e per alcuni esempi dettagliati, vedere l'articolo sulle [funzionalità HTTP](durable-functions-http-features.md).

> [!NOTE]
> La chiamata agli endpoint HTTP direttamente dalle funzioni dell'agente di orchestrazione è disponibile in Durable Functions 2.0 e versioni successive. Attualmente, questa funzionalità è implementata solo in orchestrazioni .NET.

### <a name="passing-multiple-parameters"></a>Passaggio di più parametri

Non è possibile passare più parametri direttamente a una funzione di attività. In questo caso è consigliabile passare una matrice di oggetti o usare oggetti [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) in .NET.

L'esempio seguente usa le nuove funzionalità di [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) aggiunte con [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vincoli di codice dell'agente di orchestrazione](durable-functions-code-constraints.md)
