---
title: Prestazioni e scalabilità in Funzioni permanenti - Azure
description: Introduzione all'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 3c9227a34c1b7208210b84b5b7d64ecdc8654a83
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286381"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestazioni e scalabilità in Funzioni permanenti (Funzioni di Azure)

Per ottimizzare le prestazioni e la scalabilità, è importante comprendere le caratteristiche univoche di scalabilità di [Funzioni permanenti](durable-functions-overview.md).

Per comprendere i comportamento in termini di scalabilità, è necessario conoscere alcuni dettagli del provider di Archiviazione di Azure sottostante.

## <a name="history-table"></a>Tabella di cronologia

La tabella **Cronologia** di Archiviazione di Azure contiene gli eventi di cronologia per tutte le istanza di orchestrazione in un hub attività. Il nome della tabella è nel formato *NomeHubAttività*History. Durante l'esecuzione di istanze vengono aggiunte nuove righe alla tabella. La chiave di partizione della tabella deriva dall'ID di istanza dell'orchestrazione. Nella maggior parte dei casi un ID di istanza è casuale, garantendo in tal modo la distribuzione ottimale delle partizioni interne in Archiviazione di Azure.

Quando è necessario eseguire un'istanza di orchestrazione, le righe appropriate della tabella Cronologia vengono caricate in memoria. Tali *eventi di cronologia* vengono riprodotti nel codice di funzione dell'agente di orchestrazione per riportarlo allo stato precedente all'ultimo checkpoint. L'utilizzo della cronologia di esecuzione per ricompilare lo stato in questo modo è influenzato dal [modello di origine evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabella delle istanze

La tabella **Istanze** tabella è un'altra tabella di Archiviazione di Azure che contiene gli stati di tutte le istanze di orchestrazione all'interno di un hub attività. In seguito alla creazione di istanze, nuove righe vengono aggiunte alla tabella. La chiave di partizione della tabella è l'ID dell'istanza di orchestrazione, mentre la chiave di riga è una costante fissa. Per ogni istanza di orchestrazione, è presente una riga.

Questa tabella viene usata per soddisfare le richieste delle query di istanza delle API [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) e `getStatus` (JavaScript) e anche dell'[API HTTP della query sullo stato](durable-functions-http-api.md#get-instance-status). Il contenuto della tabella viene mantenuto coerente con quello della tabella **Cronologia** citata in precedenza. L'uso di una tabella di Archiviazione di Azure separata per soddisfare in modo efficiente le operazioni di query di istanza in questo modo è influenzata dal [modello di separazione e responsabilità per query e comandi (CQRS, Command and Query Responsibility Segregation)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Trigger di code interne

Le funzioni di orchestrazione e di attività vengono attivate da code interne nell'hub attività dell'app per le funzioni. L'uso delle code in base a questa modalità supporta la garanzia di recapito "At-Least-Once". In Funzioni permanenti esistono due tipi di code: la **coda di controllo** e la **coda di elementi di lavoro**.

### <a name="the-work-item-queue"></a>Coda di elementi di lavoro

Esiste una coda di elementi di lavoro per ogni hub attività in Funzioni permanenti. È una coda di base che funziona in modo analogo a qualsiasi altra coda `queueTrigger` in Funzioni di Azure. Tale coda consente di attivare *funzioni di attività* senza stato rimuovendo dalla coda un solo messaggio alla volta. Ognuno di questi messaggi contiene gli input della funzione di attività e metadati aggiuntivi, ad esempio la funzione da eseguire. Quando viene eseguita la scalabilità orizzontale di un'applicazione di Funzioni permanenti in più macchine virtuali, tutte queste macchine virtuali competono per acquisire lavoro dalla coda di elementi di lavoro.

### <a name="control-queues"></a>Code di controllo

In Funzioni permanenti sono presenti più *code di controllo* per hub attività. Una *coda di controllo* è più complessa della coda di elementi di lavoro. Le code di controllo vengono usate per attivare le funzioni dell'agente di orchestrazione con stato. Poiché le istanze della funzione dell'agente di orchestrazione sono di tipo singleton con stato, non è possibile usare un modello di consumer concorrenti per distribuire il carico tra le macchine virtuali. I messaggi dell'agente di orchestrazione sono distribuiti con bilanciamento del carico tra le code di controllo. Altre informazioni su questo comportamento sono disponibili nelle sezioni successive.

Le code di controllo contengono messaggi di diverso tipo relativi al ciclo di vita di orchestrazione. Gli esempi includono i [messaggi di controllo dell'agente di orchestrazione](durable-functions-instance-management.md), i messaggi di *risposta* delle funzioni di attività e i messaggi del timer. In una singola operazione di polling dalla coda di controllo verranno rimossi al massimo 32 messaggi. Tali messaggi contengono dati di payload, nonché altri metadati, ad esempio l'istanza di orchestrazione a cui sono destinati. Se più messaggi rimossi dalla coda sono destinati alla stessa istanza di orchestrazione, verranno elaborati in batch.

### <a name="queue-polling"></a>Polling della coda

L'estensione durable task implementa un algoritmo esponenziale casuale back-off per ridurre l'effetto di code inattive sui costi delle transazioni di archiviazione di polling. Quando viene trovato un messaggio, il runtime controlla immediatamente per un altro messaggio. Quando viene trovato alcun messaggio, rimane in attesa per un periodo di tempo prima di riprovare. Dopo alcuni tentativi non riusciti per ottenere un messaggio della coda, il tempo di attesa continua ad aumentare finché non raggiunge il tempo di attesa massimo, impostazione predefinita è 30 secondi.

L'intervallo di polling massimo è configurabile tramite il `maxQueuePollingInterval` proprietà il [file host. JSON](../functions-host-json.md#durabletask). Impostazione di un valore più elevato potrebbe causare latenze di elaborazione dei messaggi superiore. Latenze più elevate dovrebbe solo dopo i periodi di inattività. Questa impostazione su un valore inferiore potrebbe causare un aumento dei costi di archiviazione a causa di transazioni di archiviazione maggiore.

> [!NOTE]
> Durante l'esecuzione nei piani di consumo di funzioni di Azure e Premium, il [Controller di scalabilità di funzioni di Azure](../functions-scale.md#how-the-consumption-plan-works) eseguirà il polling ogni coda di controllo e di elemento di lavoro una volta ogni 10 secondi. Polling aggiuntivi è necessario determinare il momento di attivazione di istanze di app di funzione e per prendere decisioni di scalabilità. Al momento della scrittura, questo intervallo di 10 secondi è costante e non può essere configurato.

## <a name="storage-account-selection"></a>Selezione dell'account di archiviazione

Le code, tabelle e i BLOB usati da funzioni permanenti vengono creati in un account di archiviazione di Azure configurato. L'account da usare può essere specificato tramite l'impostazione `durableTask/azureStorageConnectionStringName` nel file **host.json**.

### <a name="functions-1x"></a>Funzioni 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Funzioni 2.x

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

Le funzioni di attività sono senza stato e vengono scalate orizzontalmente in modo automatico tramite l'aggiunta di macchine virtuali. D'altra parte le funzioni di orchestrazione vengono *partizionate* tra una o più code di controllo. Il numero di code di controllo viene definito nel file **host.json**. Nel frammento di codice host.json di esempio la proprietà `durableTask/partitionCount` viene impostata su `3`.

### <a name="functions-1x"></a>Funzioni 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Funzioni 2.x

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

Quando si esegue la scalabilità orizzontale in più istanze dell'host di funzioni, in genere su diverse macchine virtuali, ogni istanza acquisisce un blocco su una delle code di controllo. Tali blocchi vengono implementati internamente come lease di archiviazione BLOB e garantiscono che un'istanza di orchestrazione venga eseguita solo in un'unica istanza di host alla volta. Se un hub attività viene configurato con tre code di controllo, il bilanciamento del carico delle istanze di orchestrazione può essere applicato a un massimo di tre macchine virtuali. È possibile aggiungere altre macchine virtuali per aumentare la capacità per l'esecuzione della funzione di attività.

Il diagramma seguente illustra l'interazione tra l'host di Funzioni di Azure e le entità di archiviazione in un ambiente con scalabilità orizzontale.

![Diagramma di scalabilità](./media/durable-functions-perf-and-scale/scale-diagram.png)

Come illustrato nel diagramma precedente, tutte le macchine virtuali sono in conflitto per i messaggi presenti nella coda degli elementi di lavoro. Tuttavia, solo tre macchine virtuali possono acquisire i messaggi dalle code di controllo e ogni macchina virtuale blocca una singola coda di controllo.

Le istanze di orchestrazione vengono distribuite tra tutte le istanze della coda di controllo e la distribuzione viene eseguita tramite l'hash dell'ID istanza dell'orchestrazione. Per impostazione predefinita, gli ID istanza sono GUID casuali, garantendo in tal modo che le istanze siano distribuite in modo uniforme tra tutte le code di controllo.

In generale, le funzioni dell'agente di orchestrazione devono essere semplici, senza richiedere potenza di calcolo in grande quantità. Non è necessario pertanto creare un numero elevato di partizioni delle code di controllo per ottenere una velocità effettiva ottimale. La maggior parte del lavoro più intenso deve essere eseguita nelle funzioni di attività senza stato, che possono essere scalate orizzontalmente all'infinito.

## <a name="auto-scale"></a>Scalabilità automatica

In modo analogo a tutte le Funzioni di Azure in esecuzione nel piano a consumo, le Funzioni permanenti supportano la scalabilità automatica tramite il [controller di scalabilità di Funzioni di Azure](../functions-scale.md#runtime-scaling). Il controller di scalabilità consente di monitorare la latenza di tutte le code eseguendo periodicamente comandi _peek_. In base alle latenze dei messaggi sottoposti al comando peek, il controller di scalabilità stabilisce se aggiungere o rimuovere macchine virtuali.

Se il controller di scalabilità determina che le latenze dei messaggi della coda di controllo sono troppo elevate, aggiunge istanze di macchine virtuali fino a quando la latenza dei messaggi non diminuisce fino a un livello accettabile o non raggiunge il numero di partizioni della coda di controllo. In modo analogo, il controller di scalabilità aggiunge continuamente istanze di macchine virtuali se le latenze della coda di elementi di lavoro sono elevate, indipendentemente dal numero di partizioni.

## <a name="thread-usage"></a>Utilizzo di thread

Le funzioni dell'agente di orchestrazione vengono eseguite su un thread singolo per garantire che l'esecuzione sia deterministica tra più riproduzioni. A causa di questa esecuzione su thread singolo, è importante che i thread delle funzioni dell'agente di orchestrazione non esegua operazioni con uso intensivo della CPU né operazioni di I/O oppure di blocco per qualsiasi motivo. Tutto il lavoro che richiede thread di I/O oppure di blocco o più thread deve essere spostato nelle funzioni di attività.

Le funzioni di attività hanno gli stessi comportamenti delle normali funzioni attivate da coda. In tal modo è possibile eseguire in modo sicuro operazioni di I/O oppure con uso intensivo della CPU e usare più thread. Poiché i trigger di attività sono senza stato, è possibile eseguire la scalabilità orizzontale a un numero illimitato di macchine virtuali.

## <a name="concurrency-throttles"></a>Limitazioni di concorrenza

Funzioni di Azure supporta l'esecuzione di più funzioni contemporaneamente in una singola istanza di app. Tale esecuzione simultanea consente di aumentare il parallelismo e riduce al minimo il numero di "avvii a freddo" che si verificano in genere per un'app tipica. Un alto grado di concorrenza può causare tuttavia un elevato uso della memoria per ogni macchina virtuale. A seconda dei requisiti dell'app per le funzioni, potrebbe essere necessario limitare la concorrenza per ogni istanza per evitare il rischio di esaurimento della memoria in situazioni di carico elevato.

I limiti per la concorrenza per la funzione di attività e per la funzione dell'agente di orchestrazione possono essere configurati nel file **host.json**. Le impostazioni pertinenti sono `durableTask/maxConcurrentActivityFunctions` e `durableTask/maxConcurrentOrchestratorFunctions` rispettivamente.

### <a name="functions-1x"></a>Funzioni 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Funzioni 2.x

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

Nell'esempio precedente al massimo 10 funzioni dell'agente di orchestrazione e 10 funzioni di attività possono essere eseguite in una singola macchina virtuale contemporaneamente. Se non specificato, il numero di esecuzioni di funzioni di attività e dell'agente di orchestrazione è limitato a 10 volte il numero di core nella macchina virtuale.

> [!NOTE]
> Tali impostazioni sono utili per semplificare la gestione della memoria e l'uso della CPU in una singola macchina virtuale. Tuttavia, se si applica la scalabilità orizzontale a più macchine virtuali, per ogni macchina virtuale verrà impostato un set di limiti specifico. Tali impostazioni non possono essere usate per controllare la concorrenza a livello globale.

## <a name="orchestrator-function-replay"></a>Riproduzione delle funzioni dell'agente di orchestrazione

Come accennato in precedenza, le funzioni dell'agente di orchestrazione vengono riprodotte tramite il contenuto della tabella **Cronologia**. Per impostazione predefinita, il codice della funzione dell'agente di orchestrazione viene riprodotto ogni volta che un batch di messaggi viene rimosso da un coda di controllo.

Per disabilitare questo comportamento di riproduzione aggressivo, è possibile abilitare le **sessioni estese**. Quando le sessioni estese vengono abilitate, le istanze delle funzioni dell'agente di orchestrazione sono conservate in memoria più a lungo ed è possibile elaborare nuovi messaggi senza una riproduzione completa. Le sessioni estese vengono abilitate impostando `durableTask/extendedSessionsEnabled` su `true` nel file **host.json**. L'impostazione `durableTask/extendedSessionIdleTimeoutInSeconds` consente di controllare per quanto tempo una sessione inattiva verrà conservata in memoria:

### <a name="functions-1x"></a>Funzioni 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Funzioni 2.x

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

L'abilitazione delle sessioni estese provoca in genere la riduzione delle operazioni di I/O in relazione all'account di Archiviazione di Azure e un miglioramento complessivo della velocità effettiva.

Uno svantaggio potenziale di questa funzionalità è tuttavia un tempo di permanenza in memoria maggiore delle istanze delle funzioni dell'agente di orchestrazione. È necessario tenere presente due effetti:

1. Aumento complessivo dell'uso della memoria da parte delle app per le funzioni.
2. Diminuzione complessiva della velocità effettiva se sono presenti più esecuzioni delle funzioni dell'agente di orchestrazione simultanee e di breve durata.

Se `durableTask/extendedSessionIdleTimeoutInSeconds` è impostato su 30 secondi, ad esempio, un episodio di funzione dell'agente di orchestrazione di breve durata eseguito in meno di 1 secondo occupa memoria ancora per 30 secondi. L'esecuzione verrà conteggiata anche rispetto alla quota `durableTask/maxConcurrentOrchestratorFunctions` indicata in precedenza, impedendo potenzialmente l'esecuzione di altre funzioni dell'agente di orchestrazione.

> [!NOTE]
> Tali impostazioni devono essere usate solo dopo che una funzione dell'agente di orchestrazione è stata completamente sviluppata e testata. Il comportamento di riproduzione aggressivo predefinito è utile per rilevare gli errori di idempotenza nelle funzioni dell'agente di orchestrazione in fase di sviluppo.

## <a name="performance-targets"></a>Prestazioni richieste

Quando si intende usare Funzioni permanenti per un'applicazione di produzione, è importante prendere in considerazione i requisiti in termini di prestazioni nelle prime fasi del processo di pianificazione. Questa sezione descrive alcuni scenari di uso di base e i valori di velocità effettiva massima prevista.

* **Esecuzione di attività sequenziali**: questo scenario descrive una funzione dell'agente di orchestrazione che esegue una serie di funzioni di attività una dopo l'altra ed è analogo all'esempio descritto in [Concatenamento di funzioni](durable-functions-sequence.md).
* **Esecuzione di attività parallele**: questo scenario descrive una funzione dell'agente di orchestrazione che esegue molte funzioni di attività in parallelo tramite il modello [fan-out, fan-in](durable-functions-cloud-backup.md).
* **Elaborazione di risposte parallele**: questo scenario è la parte complementare del modello [fan-out, fan-in](durable-functions-cloud-backup.md) e si basa sulle prestazioni dello schema fan-in. È importante notare che, a differenza dello schema fan-out, lo schema fan-in viene realizzato da un'unica istanza delle funzioni dell'agente di orchestrazione e pertanto può essere eseguito solo su un'unica macchina virtuale.
* **Elaborazione di eventi esterni**: questo scenario rappresenta un'unica istanza di funzioni dell'agente di orchestrazione che rimane in attesa su [eventi esterni](durable-functions-external-events.md), uno alla volta.

> [!TIP]
> A differenza dello schema fan-out, le operazioni fan-in sono limitate a un'unica macchina virtuale. Se l'applicazione usa il modello fan-out, fan-in ed è necessario rispettare le prestazioni in ambito fan-in, è possibile applicare lo schema fan-out della funzione di attività tra più [orchestrazioni secondarie](durable-functions-sub-orchestrations.md).

La tabella seguente mostra i valori *massimi* di velocità effettiva per gli scenari descritti in precedenza. "Istanza" si riferisce a una singola istanza di una funzione dell'agente di orchestrazione in esecuzione in un'unica macchina virtuale di piccole dimensioni ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) in Servizio app di Azure. In tutti i casi si presuppone che le [sessioni estese](#orchestrator-function-replay) siano abilitate. I risultati effettivi possono variare a seconda delle operazioni della CPU o di I/O eseguite dal codice della funzione.

| Scenario | Velocità effettiva massima |
|-|-|
| Esecuzione di attività sequenziali | 5 attività al secondo, per istanza |
| Esecuzione di attività parallele (fan-out) | 100 attività al secondo, per istanza |
| Elaborazione di risposte parallele (fan-in) | 150 risposte al secondo, per istanza |
| Elaborazione di eventi esterni | 50 eventi al secondo, per istanza |

> [!NOTE]
> Questi numeri si riferiscono alla versione corrente v1.4.0 (GA) dell'estensione Funzioni permanenti e possono variare nel tempo in seguito alla crescita e alle ottimizzazioni delle funzionalità.

Se non si realizzano in valori di velocità effettiva previsti e l'uso della CPU e della memoria sembra tuttavia corretto, verificare se la causa è correlata allo [stato dell'account di archiviazione](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). L'estensione Funzioni permanenti può aggiungere carico significativo a un account di Archiviazione di Azure e carichi sufficientemente elevati possono comportare la limitazione delle richieste dell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare la prima funzione permanente in C#](durable-functions-create-first-csharp.md)
