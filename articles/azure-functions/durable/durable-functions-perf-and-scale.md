---
title: Prestazioni e scalabilità in Funzioni permanenti - Azure
description: Introduzione all'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5efe571e2c7ff75ace584755324964003176b5f0
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614703"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestazioni e scalabilità in Funzioni permanenti (Funzioni di Azure)

Per ottimizzare le prestazioni e la scalabilità, è importante comprendere le caratteristiche univoche di scalabilità di [Funzioni permanenti](durable-functions-overview.md).

Per comprendere i comportamento in termini di scalabilità, è necessario conoscere alcuni dettagli del provider di Archiviazione di Azure sottostante.

## <a name="history-table"></a>Tabella di cronologia

La tabella **Cronologia** di Archiviazione di Azure contiene gli eventi di cronologia per tutte le istanza di orchestrazione in un hub attività. Il nome della tabella è nel formato *NomeHubAttività*History. Durante l'esecuzione di istanze vengono aggiunte nuove righe alla tabella. La chiave di partizione della tabella deriva dall'ID di istanza dell'orchestrazione. Nella maggior parte dei casi un ID di istanza è casuale, garantendo in tal modo la distribuzione ottimale delle partizioni interne in Archiviazione di Azure.

Quando è necessario eseguire un'istanza di orchestrazione, le righe appropriate della tabella Cronologia vengono caricate in memoria. Tali *eventi di cronologia* vengono riprodotti nel codice di funzione dell'agente di orchestrazione per riportarlo allo stato precedente all'ultimo checkpoint. L'utilizzo della cronologia di esecuzione per ricompilare lo stato in questo modo è influenzato dal [modello di origine evento](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabella delle istanze

La tabella **instances** è un'altra tabella di archiviazione di Azure che contiene gli Stati di tutte le istanze di orchestrazione e di entità all'interno di un hub attività. In seguito alla creazione di istanze, nuove righe vengono aggiunte alla tabella. La chiave di partizione di questa tabella è l'ID dell'istanza di orchestrazione o la chiave di entità e la chiave di riga è una costante fissa. È presente una riga per ogni orchestrazione o istanza di entità.

Questa tabella viene utilizzata per soddisfare le richieste di query di istanza dalle API `GetStatusAsync` (.NET) e `getStatus` (JavaScript), nonché dall' [API HTTP query di stato](durable-functions-http-api.md#get-instance-status). Il contenuto della tabella viene mantenuto coerente con quello della tabella **Cronologia** citata in precedenza. L'uso di una tabella di Archiviazione di Azure separata per soddisfare in modo efficiente le operazioni di query di istanza in questo modo è influenzata dal [modello di separazione e responsabilità per query e comandi (CQRS, Command and Query Responsibility Segregation)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Trigger di code interne

Le funzioni di orchestrazione e di attività vengono attivate da code interne nell'hub attività dell'app per le funzioni. L'uso delle code in base a questa modalità supporta la garanzia di recapito "At-Least-Once". In Funzioni permanenti esistono due tipi di code: la **coda di controllo** e la **coda di elementi di lavoro**.

### <a name="the-work-item-queue"></a>Coda di elementi di lavoro

Esiste una coda di elementi di lavoro per ogni hub attività in Funzioni permanenti. È una coda di base che funziona in modo analogo a qualsiasi altra coda `queueTrigger` in Funzioni di Azure. Tale coda consente di attivare *funzioni di attività* senza stato rimuovendo dalla coda un solo messaggio alla volta. Ognuno di questi messaggi contiene gli input della funzione di attività e metadati aggiuntivi, ad esempio la funzione da eseguire. Quando viene eseguita la scalabilità orizzontale di un'applicazione di Funzioni permanenti in più macchine virtuali, tutte queste macchine virtuali competono per acquisire lavoro dalla coda di elementi di lavoro.

### <a name="control-queues"></a>Code di controllo

In Funzioni permanenti sono presenti più *code di controllo* per hub attività. Una *coda di controllo* è più complessa della coda di elementi di lavoro. Le code di controllo vengono usate per attivare le funzioni dell'agente di orchestrazione e dell'entità con stato. Poiché le istanze dell'agente di orchestrazione e della funzione di entità sono singleton con stato, non è possibile usare un modello di consumer concorrente per distribuire il carico tra le macchine virtuali. Al contrario, l'agente di orchestrazione e i messaggi di entità sono bilanciati tra le code di controllo. Altre informazioni su questo comportamento sono disponibili nelle sezioni successive.

Le code di controllo contengono messaggi di diverso tipo relativi al ciclo di vita di orchestrazione. Gli esempi includono i [messaggi di controllo dell'agente di orchestrazione](durable-functions-instance-management.md), i messaggi di *risposta* delle funzioni di attività e i messaggi del timer. In una singola operazione di polling dalla coda di controllo verranno rimossi al massimo 32 messaggi. Tali messaggi contengono dati di payload, nonché altri metadati, ad esempio l'istanza di orchestrazione a cui sono destinati. Se più messaggi rimossi dalla coda sono destinati alla stessa istanza di orchestrazione, verranno elaborati in batch.

### <a name="queue-polling"></a>Polling della coda

L'estensione di attività durevole implementa un algoritmo di backup esponenziale casuale per ridurre l'effetto del polling delle code inattive sui costi delle transazioni di archiviazione. Quando viene trovato un messaggio, il runtime verifica immediatamente la presenza di un altro messaggio. Quando non viene trovato alcun messaggio, attende un certo periodo di tempo prima di riprovare. Dopo i tentativi successivi non riusciti di ottenere un messaggio in coda, il tempo di attesa continua ad aumentare fino a raggiungere il tempo di attesa massimo, che per impostazione predefinita è 30 secondi.

Il ritardo massimo di polling può essere configurato tramite la proprietà `maxQueuePollingInterval` nel [file host. JSON](../functions-host-json.md#durabletask). L'impostazione di questa proprietà su un valore superiore può comportare latenze di elaborazione dei messaggi più elevate. Le latenze più elevate sarebbero previste solo dopo periodi di inattività. L'impostazione di questa proprietà su un valore inferiore può comportare costi di archiviazione più elevati a causa di un aumento delle transazioni di archiviazione.

> [!NOTE]
> Quando viene eseguito nei piani di consumo e Premium di funzioni di Azure, il [controller di scalabilità di funzioni di Azure](../functions-scale.md#how-the-consumption-and-premium-plans-work) eseguirà il polling di ogni controllo e coda di elementi di lavoro ogni 10 secondi. Questo polling aggiuntivo è necessario per determinare quando attivare le istanze delle app per le funzioni e prendere decisioni di scalabilità. Al momento della stesura di questa operazione, questo intervallo di 10 secondi è costante e non può essere configurato.

## <a name="storage-account-selection"></a>Selezione dell'account di archiviazione

Le code, le tabelle e i BLOB usati da Durable Functions vengono creati in un account di archiviazione di Azure configurato. Per specificare l'account da usare, è possibile usare l'impostazione `durableTask/storageProvider/connectionStringName` (o `durableTask/azureStorageConnectionStringName` impostazione in Durable Functions 1. x) nel file **host. JSON** .

### <a name="durable-functions-2x"></a>Durable Functions 2. x

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

### <a name="durable-functions-1x"></a>Durable Functions 1. x

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

Le funzioni di attività sono senza stato e vengono scalate orizzontalmente in modo automatico tramite l'aggiunta di macchine virtuali. Le funzioni e le entità dell'agente di orchestrazione, invece, vengono *partizionate* in una o più code di controllo. Il numero di code di controllo viene definito nel file **host.json**. Il frammento di codice host. JSON di esempio seguente imposta la proprietà `durableTask/storageProvider/partitionCount` (o `durableTask/partitionCount` in Durable Functions 1. x) su `3`.

### <a name="durable-functions-2x"></a>Durable Functions 2. x

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

### <a name="durable-functions-1x"></a>Durable Functions 1. x

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

Quando si esegue la scalabilità orizzontale in più istanze dell'host di funzioni, in genere su diverse macchine virtuali, ogni istanza acquisisce un blocco su una delle code di controllo. Questi blocchi vengono implementati internamente come lease di archiviazione BLOB e assicurano che un'istanza di orchestrazione o un'entità venga eseguita solo su una singola istanza host alla volta. Se un hub attività è configurato con tre code di controllo, le istanze di orchestrazione e le entità possono essere sottoposte a bilanciamento del carico in un massimo di tre macchine virtuali. È possibile aggiungere altre macchine virtuali per aumentare la capacità per l'esecuzione della funzione di attività.

Il diagramma seguente illustra l'interazione tra l'host di Funzioni di Azure e le entità di archiviazione in un ambiente con scalabilità orizzontale.

![Diagramma di scalabilità](./media/durable-functions-perf-and-scale/scale-diagram.png)

Come illustrato nel diagramma precedente, tutte le macchine virtuali sono in conflitto per i messaggi presenti nella coda degli elementi di lavoro. Tuttavia, solo tre macchine virtuali possono acquisire i messaggi dalle code di controllo e ogni macchina virtuale blocca una singola coda di controllo.

Istanze ed entità dell'orchestrazione vengono distribuite in tutte le istanze della coda di controllo. La distribuzione viene eseguita mediante l'hashing dell'ID istanza dell'orchestrazione o il nome dell'entità e la coppia di chiavi. Per impostazione predefinita, gli ID istanza di orchestrazione sono GUID casuali, assicurando che le istanze siano equamente distribuite in tutte le code di controllo.

In generale, le funzioni dell'agente di orchestrazione devono essere semplici, senza richiedere potenza di calcolo in grande quantità. Non è quindi necessario creare un numero elevato di partizioni di code di controllo per ottenere una velocità effettiva elevata per le orchestrazioni. La maggior parte del lavoro più intenso deve essere eseguita nelle funzioni di attività senza stato, che possono essere scalate orizzontalmente all'infinito.

## <a name="auto-scale"></a>Scalabilità automatica

Come con tutte le funzioni di Azure in esecuzione nei piani di consumo e Premium elastico, Durable Functions supporta la scalabilità automatica tramite il [controller di scalabilità di funzioni di Azure](../functions-scale.md#runtime-scaling). Il controller di scalabilità consente di monitorare la latenza di tutte le code eseguendo periodicamente comandi _peek_. In base alle latenze dei messaggi sottoposti al comando peek, il controller di scalabilità stabilisce se aggiungere o rimuovere macchine virtuali.

Se il controller di scalabilità determina che le latenze dei messaggi della coda di controllo sono troppo elevate, aggiunge istanze di macchine virtuali fino a quando la latenza dei messaggi non diminuisce fino a un livello accettabile o non raggiunge il numero di partizioni della coda di controllo. In modo analogo, il controller di scalabilità aggiunge continuamente istanze di macchine virtuali se le latenze della coda di elementi di lavoro sono elevate, indipendentemente dal numero di partizioni.

> [!NOTE]
> A partire da Durable Functions 2,0, le app per le funzioni possono essere configurate per l'esecuzione in endpoint di servizio protetti con VNET nel piano Premium elastico. In questa configurazione, i trigger di Durable Functions avviano richieste di ridimensionamento anziché il controller di ridimensionamento.

## <a name="thread-usage"></a>Utilizzo di thread

Le funzioni dell'agente di orchestrazione vengono eseguite su un thread singolo per garantire che l'esecuzione sia deterministica tra più riproduzioni. A causa di questa esecuzione su thread singolo, è importante che i thread delle funzioni dell'agente di orchestrazione non esegua operazioni con uso intensivo della CPU né operazioni di I/O oppure di blocco per qualsiasi motivo. Tutto il lavoro che richiede thread di I/O oppure di blocco o più thread deve essere spostato nelle funzioni di attività.

Le funzioni di attività hanno gli stessi comportamenti delle normali funzioni attivate da coda. In tal modo è possibile eseguire in modo sicuro operazioni di I/O oppure con uso intensivo della CPU e usare più thread. Poiché i trigger di attività sono senza stato, è possibile eseguire la scalabilità orizzontale a un numero illimitato di macchine virtuali.

Anche le funzioni di entità vengono eseguite su un singolo thread e le operazioni vengono elaborate una alla volta. Tuttavia, le funzioni di entità non presentano alcuna restrizione sul tipo di codice che può essere eseguito.

## <a name="concurrency-throttles"></a>Limitazioni di concorrenza

Funzioni di Azure supporta l'esecuzione di più funzioni contemporaneamente in una singola istanza di app. Tale esecuzione simultanea consente di aumentare il parallelismo e riduce al minimo il numero di "avvii a freddo" che si verificano in genere per un'app tipica. Tuttavia, la concorrenza elevata può esaurire le risorse di sistema per macchina virtuale, ad esempio connessioni di rete o memoria disponibile. A seconda dei requisiti dell'app per le funzioni, potrebbe essere necessario limitare la concorrenza per ogni istanza per evitare il rischio di esaurimento della memoria in situazioni di carico elevato.

È possibile configurare i limiti di concorrenza di attività, agenti di orchestrazione e funzioni di entità nel file **host. JSON** . Le impostazioni rilevanti sono `durableTask/maxConcurrentActivityFunctions` per le funzioni di attività e `durableTask/maxConcurrentOrchestratorFunctions` per le funzioni dell'agente di orchestrazione e dell'entità.

### <a name="functions-20"></a>Funzioni 2,0

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

Nell'esempio precedente, un massimo di 10 funzioni dell'agente di orchestrazione o di entità e 10 funzioni di attività possono essere eseguite contemporaneamente su una singola macchina virtuale. Se non è specificato, il numero di esecuzioni di attività simultanee e di agente di orchestrazione o di funzione di entità viene limitato a 10 volte il numero di core nella macchina virtuale.

> [!NOTE]
> Tali impostazioni sono utili per semplificare la gestione della memoria e l'uso della CPU in una singola macchina virtuale. Tuttavia, in caso di scalabilità orizzontale tra più macchine virtuali, ogni macchina virtuale ha un proprio set di limiti. Queste impostazioni non possono essere usate per controllare la concorrenza a livello globale.

## <a name="extended-sessions"></a>Sessioni estese

Le sessioni estese sono un'impostazione che mantiene le orchestrazioni e le entità in memoria anche dopo che hanno terminato l'elaborazione dei messaggi. L'abilitazione delle sessioni estese provoca in genere la riduzione delle operazioni di I/O in relazione all'account di Archiviazione di Azure e un miglioramento complessivo della velocità effettiva.

È possibile abilitare le sessioni estese impostando `durableTask/extendedSessionsEnabled` su `true` nel file **host. JSON** . L'impostazione `durableTask/extendedSessionIdleTimeoutInSeconds` può essere utilizzata per controllare per quanto tempo una sessione inattiva verrà mantenuta in memoria:

**Funzioni 2,0**
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

**Funzioni 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

È possibile tenere presente due potenziali svantaggi di questa impostazione:

1. L'utilizzo della memoria dell'app per le funzioni è un aumento complessivo.
2. È possibile che si verifichi una riduzione complessiva della velocità effettiva in presenza di più esecuzioni di funzioni di agente di orchestrazione o di funzione dell'entità temporanee.

Se ad esempio `durableTask/extendedSessionIdleTimeoutInSeconds` è impostato su 30 secondi, un agente di orchestrazione di breve durata o un episodio di funzione di entità che viene eseguito in meno di 1 secondo occupa ancora memoria per 30 secondi. Viene inoltre conteggiato rispetto alla quota `durableTask/maxConcurrentOrchestratorFunctions` citata in precedenza, impedendo potenzialmente l'esecuzione di altre funzioni dell'agente di orchestrazione o di entità.

Le sezioni successive illustrano gli effetti specifici delle sessioni estese sulle funzioni dell'agente di orchestrazione e dell'entità.

### <a name="orchestrator-function-replay"></a>Riproduzione delle funzioni dell'agente di orchestrazione

Come accennato in precedenza, le funzioni dell'agente di orchestrazione vengono riprodotte tramite il contenuto della tabella **Cronologia**. Per impostazione predefinita, il codice della funzione dell'agente di orchestrazione viene riprodotto ogni volta che un batch di messaggi viene rimosso da un coda di controllo. Quando sono abilitate le sessioni estese, le istanze della funzione dell'agente di orchestrazione vengono mantenute in memoria più a lungo e i nuovi messaggi possono essere elaborati senza una riproduzione completa

Il miglioramento delle prestazioni delle sessioni estese è spesso osservato nelle situazioni seguenti:

* Quando è presente un numero limitato di istanze di orchestrazione in esecuzione simultanea.
* Quando le orchestrazioni hanno un numero elevato di azioni sequenziali, ad esempio centinaia di chiamate di funzioni di attività, che vengono completate rapidamente.
* Quando le orchestrazioni sono fan-out e fan-in un numero elevato di azioni che vengono completate intorno allo stesso tempo.
* Quando le funzioni dell'agente di orchestrazione devono elaborare messaggi di grandi dimensioni o eseguire un'elaborazione dei dati con utilizzo intensivo della CPU

In tutti gli altri casi, non è in genere disponibile un miglioramento delle prestazioni osservabile per le funzioni di orchestrazione.

> [!NOTE]
> Tali impostazioni devono essere usate solo dopo che una funzione dell'agente di orchestrazione è stata completamente sviluppata e testata. Il comportamento di riproduzione aggressivo predefinito può essere utile per rilevare le violazioni dei vincoli del codice della funzione dell'agente di [orchestrazione](durable-functions-code-constraints.md) in fase di sviluppo ed è quindi disabilitato per impostazione predefinita.

### <a name="entity-function-unloading"></a>Scaricamento di funzioni di entità

Le funzioni di entità elaborano fino a 20 operazioni in un singolo batch. Non appena un'entità termina l'elaborazione di un batch di operazioni, Salva in modo permanente lo stato e lo Scarica dalla memoria. È possibile ritardare lo scaricamento delle entità dalla memoria utilizzando l'impostazione sessioni estese. Le entità continuano a mantenere le modifiche dello stato come prima, ma rimangono in memoria per il periodo di tempo configurato per ridurre il numero di caricamenti dall'archiviazione di Azure. Questa riduzione dei carichi di archiviazione di Azure può migliorare la velocità effettiva complessiva delle entità a cui si accede di frequente.

## <a name="performance-targets"></a>Prestazioni richieste

Quando si intende usare Funzioni permanenti per un'applicazione di produzione, è importante prendere in considerazione i requisiti in termini di prestazioni nelle prime fasi del processo di pianificazione. Questa sezione descrive alcuni scenari di uso di base e i valori di velocità effettiva massima prevista.

* **Esecuzione di attività sequenziali**: questo scenario descrive una funzione dell'agente di orchestrazione che esegue una serie di funzioni di attività una dopo l'altra ed è analogo all'esempio descritto in [Concatenamento di funzioni](durable-functions-sequence.md).
* **Esecuzione di attività parallele**: questo scenario descrive una funzione dell'agente di orchestrazione che esegue molte funzioni di attività in parallelo tramite il modello [fan-out, fan-in](durable-functions-cloud-backup.md).
* **Elaborazione di risposte parallele**: questo scenario è la parte complementare del modello [fan-out, fan-in](durable-functions-cloud-backup.md) e si basa sulle prestazioni dello schema fan-in. È importante notare che, a differenza dello schema fan-out, lo schema fan-in viene realizzato da un'unica istanza delle funzioni dell'agente di orchestrazione e pertanto può essere eseguito solo su un'unica macchina virtuale.
* **Elaborazione di eventi esterni**: questo scenario rappresenta un'unica istanza di funzioni dell'agente di orchestrazione che rimane in attesa su [eventi esterni](durable-functions-external-events.md), uno alla volta.
* **Elaborazione**delle operazioni dell'entità: questo scenario verifica la velocità con cui una _singola_ [entità contatore](durable-functions-entities.md) può elaborare un flusso costante di operazioni.

> [!TIP]
> A differenza dello schema fan-out, le operazioni fan-in sono limitate a un'unica macchina virtuale. Se l'applicazione usa il modello fan-out, fan-in ed è necessario rispettare le prestazioni in ambito fan-in, è possibile applicare lo schema fan-out della funzione di attività tra più [orchestrazioni secondarie](durable-functions-sub-orchestrations.md).

La tabella seguente mostra i valori *massimi* di velocità effettiva per gli scenari descritti in precedenza. "Istanza" si riferisce a una singola istanza di una funzione dell'agente di orchestrazione in esecuzione in un'unica macchina virtuale di piccole dimensioni ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) in Servizio app di Azure. In tutti i casi si presuppone che le [sessioni estese](#orchestrator-function-replay) siano abilitate. I risultati effettivi possono variare a seconda delle operazioni della CPU o di I/O eseguite dal codice della funzione.

| Scenario | Velocità effettiva massima |
|-|-|
| Esecuzione di attività sequenziali | 5 attività al secondo, per istanza |
| Esecuzione di attività parallele (fan-out) | 100 attività al secondo, per istanza |
| Elaborazione di risposte parallele (fan-in) | 150 risposte al secondo, per istanza |
| Elaborazione di eventi esterni | 50 eventi al secondo, per istanza |
| Elaborazione delle operazioni dell'entità | 64 operazioni al secondo |

> [!NOTE]
> Questi numeri si riferiscono alla versione corrente v1.4.0 (GA) dell'estensione Funzioni permanenti e possono variare nel tempo in seguito alla crescita e alle ottimizzazioni delle funzionalità.

Se non si realizzano in valori di velocità effettiva previsti e l'uso della CPU e della memoria sembra tuttavia corretto, verificare se la causa è correlata allo [stato dell'account di archiviazione](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). L'estensione Funzioni permanenti può aggiungere carico significativo a un account di Archiviazione di Azure e carichi sufficientemente elevati possono comportare la limitazione delle richieste dell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sul ripristino di emergenza e sulla distribuzione geografica](durable-functions-disaster-recovery-geo-distribution.md)
