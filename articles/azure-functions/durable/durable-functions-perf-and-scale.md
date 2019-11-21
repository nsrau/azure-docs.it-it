---
title: Prestazioni e scalabilità in Funzioni permanenti - Azure
description: Introduzione all'estensione Funzioni permanenti per Funzioni di Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 15302eb4f89c854210d4fc1aba292c57d4757278
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231352"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestazioni e scalabilità in Funzioni permanenti (Funzioni di Azure)

Per ottimizzare le prestazioni e la scalabilità, è importante comprendere le caratteristiche univoche di scalabilità di [Funzioni permanenti](durable-functions-overview.md).

Per comprendere i comportamento in termini di scalabilità, è necessario conoscere alcuni dettagli del provider di Archiviazione di Azure sottostante.

## <a name="history-table"></a>Tabella di cronologia

La tabella **Cronologia** di Archiviazione di Azure contiene gli eventi di cronologia per tutte le istanza di orchestrazione in un hub attività. Il nome della tabella è nel formato *NomeHubAttività*History. Durante l'esecuzione di istanze vengono aggiunte nuove righe alla tabella. La chiave di partizione della tabella deriva dall'ID di istanza dell'orchestrazione. Nella maggior parte dei casi un ID di istanza è casuale, garantendo in tal modo la distribuzione ottimale delle partizioni interne in Archiviazione di Azure.

Quando è necessario eseguire un'istanza di orchestrazione, le righe appropriate della tabella Cronologia vengono caricate in memoria. Tali *eventi di cronologia* vengono riprodotti nel codice di funzione dell'agente di orchestrazione per riportarlo allo stato precedente all'ultimo checkpoint. L'utilizzo della cronologia di esecuzione per ricompilare lo stato in questo modo è influenzato dal [modello di origine evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabella delle istanze

The **Instances** table is another Azure Storage table that contains the statuses of all orchestration and entity instances within a task hub. In seguito alla creazione di istanze, nuove righe vengono aggiunte alla tabella. The partition key of this table is the orchestration instance ID or entity key and the row key is a fixed constant. There is one row per orchestration or entity instance.

This table is used to satisfy instance query requests from the `GetStatusAsync` (.NET) and `getStatus` (JavaScript) APIs as well as the [status query HTTP API](durable-functions-http-api.md#get-instance-status). Il contenuto della tabella viene mantenuto coerente con quello della tabella **Cronologia** citata in precedenza. L'uso di una tabella di Archiviazione di Azure separata per soddisfare in modo efficiente le operazioni di query di istanza in questo modo è influenzata dal [modello di separazione e responsabilità per query e comandi (CQRS, Command and Query Responsibility Segregation)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Trigger di code interne

Le funzioni di orchestrazione e di attività vengono attivate da code interne nell'hub attività dell'app per le funzioni. L'uso delle code in base a questa modalità supporta la garanzia di recapito "At-Least-Once". In Funzioni permanenti esistono due tipi di code: la **coda di controllo** e la **coda di elementi di lavoro**.

### <a name="the-work-item-queue"></a>Coda di elementi di lavoro

Esiste una coda di elementi di lavoro per ogni hub attività in Funzioni permanenti. È una coda di base che funziona in modo analogo a qualsiasi altra coda `queueTrigger` in Funzioni di Azure. Tale coda consente di attivare *funzioni di attività* senza stato rimuovendo dalla coda un solo messaggio alla volta. Ognuno di questi messaggi contiene gli input della funzione di attività e metadati aggiuntivi, ad esempio la funzione da eseguire. Quando viene eseguita la scalabilità orizzontale di un'applicazione di Funzioni permanenti in più macchine virtuali, tutte queste macchine virtuali competono per acquisire lavoro dalla coda di elementi di lavoro.

### <a name="control-queues"></a>Code di controllo

In Funzioni permanenti sono presenti più *code di controllo* per hub attività. Una *coda di controllo* è più complessa della coda di elementi di lavoro. Control queues are used to trigger the stateful orchestrator and entity functions. Because the orchestrator and entity function instances are stateful singletons, it's not possible to use a competing consumer model to distribute load across VMs. Instead, orchestrator and entity messages are load-balanced across the control queues. Altre informazioni su questo comportamento sono disponibili nelle sezioni successive.

Le code di controllo contengono messaggi di diverso tipo relativi al ciclo di vita di orchestrazione. Gli esempi includono i [messaggi di controllo dell'agente di orchestrazione](durable-functions-instance-management.md), i messaggi di *risposta* delle funzioni di attività e i messaggi del timer. In una singola operazione di polling dalla coda di controllo verranno rimossi al massimo 32 messaggi. Tali messaggi contengono dati di payload, nonché altri metadati, ad esempio l'istanza di orchestrazione a cui sono destinati. Se più messaggi rimossi dalla coda sono destinati alla stessa istanza di orchestrazione, verranno elaborati in batch.

### <a name="queue-polling"></a>Queue polling

The durable task extension implements a random exponential back-off algorithm to reduce the effect of idle-queue polling on storage transaction costs. When a message is found, the runtime immediately checks for another message; when no message is found, it waits for a period of time before trying again. After subsequent failed attempts to get a queue message, the wait time continues to increase until it reaches the maximum wait time, which defaults to 30 seconds.

The maximum polling delay is configurable via the `maxQueuePollingInterval` property in the [host.json file](../functions-host-json.md#durabletask). Setting this property to a higher value could result in higher message processing latencies. Higher latencies would be expected only after periods of inactivity. Setting this property to a lower value could result in higher storage costs due to increased storage transactions.

> [!NOTE]
> When running in the Azure Functions Consumption and Premium plans, the [Azure Functions Scale Controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) will poll each control and work-item queue once every 10 seconds. This additional polling is necessary to determine when to activate function app instances and to make scale decisions. At the time of writing, this 10 second interval is constant and cannot be configured.

## <a name="storage-account-selection"></a>Selezione dell'account di archiviazione

The queues, tables, and blobs used by Durable Functions are created in a configured Azure Storage account. The account to use can be specified using the `durableTask/storageProvider/connectionStringName` setting (or `durableTask/azureStorageConnectionStringName` setting in Durable Functions 1.x) in the **host.json** file.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Se non specificato, come valore predefinito viene usato l'account di archiviazione `AzureWebJobsStorage`. Per carichi di lavoro sensibili alle prestazioni, è tuttavia consigliabile configurare un account di archiviazione non predefinito. Funzioni permanenti usa di frequente Archiviazione di Azure e l'uso di un account di archiviazione dedicato separa l'uso dell'archiviazione di Funzioni permanenti dall'uso interno da parte dell'host di Funzioni di Azure.

## <a name="orchestrator-scale-out"></a>Scalabilità orizzontale dell'agente di orchestrazione

Le funzioni di attività sono senza stato e vengono scalate orizzontalmente in modo automatico tramite l'aggiunta di macchine virtuali. Orchestrator functions and entities, on the other hand, are *partitioned* across one or more control queues. Il numero di code di controllo viene definito nel file **host.json**. The following example host.json snippet sets the `durableTask/storageProvider/partitionCount` property (or `durableTask/partitionCount` in Durable Functions 1.x) to `3`.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Un hub attività può essere configurato con un numero di partizioni compreso tra 1 e 16. Se non specificato, il numero di partizioni predefinito è **4**.

Quando si esegue la scalabilità orizzontale in più istanze dell'host di funzioni, in genere su diverse macchine virtuali, ogni istanza acquisisce un blocco su una delle code di controllo. These locks are internally implemented as blob storage leases and ensure that an orchestration instance or entity only runs on a single host instance at a time. If a task hub is configured with three control queues, orchestration instances and entities can be load-balanced across as many as three VMs. È possibile aggiungere altre macchine virtuali per aumentare la capacità per l'esecuzione della funzione di attività.

Il diagramma seguente illustra l'interazione tra l'host di Funzioni di Azure e le entità di archiviazione in un ambiente con scalabilità orizzontale.

![Diagramma di scalabilità](./media/durable-functions-perf-and-scale/scale-diagram.png)

Come illustrato nel diagramma precedente, tutte le macchine virtuali sono in conflitto per i messaggi presenti nella coda degli elementi di lavoro. Tuttavia, solo tre macchine virtuali possono acquisire i messaggi dalle code di controllo e ogni macchina virtuale blocca una singola coda di controllo.

Orchestration instances and entities are distributed across all control queue instances. The distribution is done by hashing the instance ID of the orchestration or the entity name and key pair. Orchestration instance IDs by default are random GUIDs, ensuring that instances are equally distributed across all control queues.

In generale, le funzioni dell'agente di orchestrazione devono essere semplici, senza richiedere potenza di calcolo in grande quantità. It is therefore not necessary to create a large number of control queue partitions to get great throughput for orchestrations. La maggior parte del lavoro più intenso deve essere eseguita nelle funzioni di attività senza stato, che possono essere scalate orizzontalmente all'infinito.

## <a name="auto-scale"></a>Scalabilità automatica

As with all Azure Functions running in the Consumption and Elastic Premium plans, Durable Functions supports auto-scale via the [Azure Functions scale controller](../functions-scale.md#runtime-scaling). Il controller di scalabilità consente di monitorare la latenza di tutte le code eseguendo periodicamente comandi _peek_. In base alle latenze dei messaggi sottoposti al comando peek, il controller di scalabilità stabilisce se aggiungere o rimuovere macchine virtuali.

Se il controller di scalabilità determina che le latenze dei messaggi della coda di controllo sono troppo elevate, aggiunge istanze di macchine virtuali fino a quando la latenza dei messaggi non diminuisce fino a un livello accettabile o non raggiunge il numero di partizioni della coda di controllo. In modo analogo, il controller di scalabilità aggiunge continuamente istanze di macchine virtuali se le latenze della coda di elementi di lavoro sono elevate, indipendentemente dal numero di partizioni.

> [!NOTE]
> Starting with Durable Functions 2.0, function apps can be configured to run within VNET-protected service endpoints in the Elastic Premium plan. In this configuration, the Durable Functions triggers initiate scale requests instead of the Scale Controller.

## <a name="thread-usage"></a>Utilizzo di thread

Le funzioni dell'agente di orchestrazione vengono eseguite su un thread singolo per garantire che l'esecuzione sia deterministica tra più riproduzioni. A causa di questa esecuzione su thread singolo, è importante che i thread delle funzioni dell'agente di orchestrazione non esegua operazioni con uso intensivo della CPU né operazioni di I/O oppure di blocco per qualsiasi motivo. Tutto il lavoro che richiede thread di I/O oppure di blocco o più thread deve essere spostato nelle funzioni di attività.

Le funzioni di attività hanno gli stessi comportamenti delle normali funzioni attivate da coda. In tal modo è possibile eseguire in modo sicuro operazioni di I/O oppure con uso intensivo della CPU e usare più thread. Poiché i trigger di attività sono senza stato, è possibile eseguire la scalabilità orizzontale a un numero illimitato di macchine virtuali.

Entity functions are also executed on a single thread and operations are processed one-at-a-time. However, entity functions do not have any restrictions on the type of code that can be executed.

## <a name="concurrency-throttles"></a>Limitazioni di concorrenza

Funzioni di Azure supporta l'esecuzione di più funzioni contemporaneamente in una singola istanza di app. Tale esecuzione simultanea consente di aumentare il parallelismo e riduce al minimo il numero di "avvii a freddo" che si verificano in genere per un'app tipica. However, high concurrency can exhaust per-VM system resources such network connections or available memory. A seconda dei requisiti dell'app per le funzioni, potrebbe essere necessario limitare la concorrenza per ogni istanza per evitare il rischio di esaurimento della memoria in situazioni di carico elevato.

Activity, orchestrator, and entity function concurrency limits can be configured in the **host.json** file. The relevant settings are `durableTask/maxConcurrentActivityFunctions` for activity functions and `durableTask/maxConcurrentOrchestratorFunctions` for both orchestrator and entity functions.

### <a name="functions-20"></a>Functions 2.0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Funzioni 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

In the previous example, a maximum of 10 orchestrator or entity functions and 10 activity functions can run on a single VM concurrently. If not specified, the number of concurrent activity and orchestrator or entity function executions is capped at 10X the number of cores on the VM.

> [!NOTE]
> Tali impostazioni sono utili per semplificare la gestione della memoria e l'uso della CPU in una singola macchina virtuale. However, when scaled out across multiple VMs, each VM has its own set of limits. These settings can't be used to control concurrency at a global level.

## <a name="extended-sessions"></a>Extended sessions

Extended sessions is a setting that keeps orchestrations and entities in memory even after they finish processing messages. L'abilitazione delle sessioni estese provoca in genere la riduzione delle operazioni di I/O in relazione all'account di Archiviazione di Azure e un miglioramento complessivo della velocità effettiva.

You can enable extended sessions by setting `durableTask/extendedSessionsEnabled` to `true` in the **host.json** file. The `durableTask/extendedSessionIdleTimeoutInSeconds` setting can be used to control how long an idle session will be held in memory:

**Functions 2.0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functions 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

There are two potential downsides of this setting to be aware of:

1. There's an overall increase in function app memory usage.
2. There can be an overall decrease in throughput if there are many concurrent, short-lived orchestrator or entity function executions.

As an example, if `durableTask/extendedSessionIdleTimeoutInSeconds` is set to 30 seconds, then a short-lived orchestrator or entity function episode that executes in less than 1 second still occupies memory for 30 seconds. It also counts against the `durableTask/maxConcurrentOrchestratorFunctions` quota mentioned previously, potentially preventing other orchestrator or entity functions from running.

The specific effects of extended sessions on orchestrator and entity functions are described in the next sections.

### <a name="orchestrator-function-replay"></a>Riproduzione delle funzioni dell'agente di orchestrazione

Come accennato in precedenza, le funzioni dell'agente di orchestrazione vengono riprodotte tramite il contenuto della tabella **Cronologia**. Per impostazione predefinita, il codice della funzione dell'agente di orchestrazione viene riprodotto ogni volta che un batch di messaggi viene rimosso da un coda di controllo. When extended sessions are enabled, orchestrator function instances are held in memory longer and new messages can be processed without a full history replay.

The performance improvement of extended sessions is most often observed in the following situations:

* When there are a limited number of orchestration instances running concurrently.
* When orchestrations have large number of sequential actions (e.g. hundreds of activity function calls) that complete quickly.
* When orchestrations fan-out and fan-in a large number of actions that complete around the same time.
* When orchestrator functions need to process large messages or do any CPU-intensive data processing.

In all other situations, there is typically no observable performance improvement for orchestrator functions.

> [!NOTE]
> Tali impostazioni devono essere usate solo dopo che una funzione dell'agente di orchestrazione è stata completamente sviluppata e testata. The default aggressive replay behavior can useful for detecting [orchestrator function code constraints](durable-functions-code-constraints.md) violations at development time, and is therefore disabled by default.

### <a name="entity-function-unloading"></a>Entity function unloading

Entity functions process up to 20 operations in a single batch. As soon as an entity finishes processing a batch of operations, it persists its state and unloads from memory. You can delay the unloading of entities from memory using the extended sessions setting. Entities continue to persist their state changes as before, but remain in memory for the configured period of time to reduce the number of loads from Azure Storage. This reduction of loads from Azure Storage can improve the overall throughput of frequently accessed entities.

## <a name="performance-targets"></a>Prestazioni richieste

Quando si intende usare Funzioni permanenti per un'applicazione di produzione, è importante prendere in considerazione i requisiti in termini di prestazioni nelle prime fasi del processo di pianificazione. Questa sezione descrive alcuni scenari di uso di base e i valori di velocità effettiva massima prevista.

* **Esecuzione di attività sequenziali**: questo scenario descrive una funzione dell'agente di orchestrazione che esegue una serie di funzioni di attività una dopo l'altra ed è analogo all'esempio descritto in [Concatenamento di funzioni](durable-functions-sequence.md).
* **Esecuzione di attività parallele**: questo scenario descrive una funzione dell'agente di orchestrazione che esegue molte funzioni di attività in parallelo tramite il modello [fan-out, fan-in](durable-functions-cloud-backup.md).
* **Elaborazione di risposte parallele**: questo scenario è la parte complementare del modello [fan-out, fan-in](durable-functions-cloud-backup.md) e si basa sulle prestazioni dello schema fan-in. È importante notare che, a differenza dello schema fan-out, lo schema fan-in viene realizzato da un'unica istanza delle funzioni dell'agente di orchestrazione e pertanto può essere eseguito solo su un'unica macchina virtuale.
* **Elaborazione di eventi esterni**: questo scenario rappresenta un'unica istanza di funzioni dell'agente di orchestrazione che rimane in attesa su [eventi esterni](durable-functions-external-events.md), uno alla volta.
* **Entity operation processing**: This scenario tests how quickly a _single_ [Counter entity](durable-functions-entities.md) can process a constant stream of operations.

> [!TIP]
> A differenza dello schema fan-out, le operazioni fan-in sono limitate a un'unica macchina virtuale. Se l'applicazione usa il modello fan-out, fan-in ed è necessario rispettare le prestazioni in ambito fan-in, è possibile applicare lo schema fan-out della funzione di attività tra più [orchestrazioni secondarie](durable-functions-sub-orchestrations.md).

La tabella seguente mostra i valori *massimi* di velocità effettiva per gli scenari descritti in precedenza. "Istanza" si riferisce a una singola istanza di una funzione dell'agente di orchestrazione in esecuzione in un'unica macchina virtuale di piccole dimensioni ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) in Servizio app di Azure. In tutti i casi si presuppone che le [sessioni estese](#orchestrator-function-replay) siano abilitate. I risultati effettivi possono variare a seconda delle operazioni della CPU o di I/O eseguite dal codice della funzione.

| Scenario | Velocità effettiva massima |
|-|-|
| Esecuzione di attività sequenziali | 5 attività al secondo, per istanza |
| Esecuzione di attività parallele (fan-out) | 100 attività al secondo, per istanza |
| Elaborazione di risposte parallele (fan-in) | 150 risposte al secondo, per istanza |
| Elaborazione di eventi esterni | 50 eventi al secondo, per istanza |
| Entity operation processing | 64 operations per second |

> [!NOTE]
> Questi numeri si riferiscono alla versione corrente v1.4.0 (GA) dell'estensione Funzioni permanenti e possono variare nel tempo in seguito alla crescita e alle ottimizzazioni delle funzionalità.

Se non si realizzano in valori di velocità effettiva previsti e l'uso della CPU e della memoria sembra tuttavia corretto, verificare se la causa è correlata allo [stato dell'account di archiviazione](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). L'estensione Funzioni permanenti può aggiungere carico significativo a un account di Archiviazione di Azure e carichi sufficientemente elevati possono comportare la limitazione delle richieste dell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Learn about disaster recovery and geo-distribution](durable-functions-disaster-recovery-geo-distribution.md)
