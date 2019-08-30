---
title: Output di Analisi di flusso di Azure in Database SQL di Azure
description: Maggiori informazioni sull'output dei dati in SQL Azure da Analisi di flusso di Azure e su come ottenere una elevata velocità effettiva di scrittura.
services: stream-analytics
author: chetanmsft
ms.author: chetang
manager: katiiceva
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 7845833a0269514c8fdbd093e18d4516ff9567d9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173008"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Output di Analisi di flusso di Azure in Database SQL di Azure

Questo articolo fornisce suggerimenti per ottenere migliori prestazioni di velocità effettiva di scrittura quando si caricano dati nel Database SQL di Azure mediante l'Analisi di flusso di Azure.

L'output di SQL nell'Analisi di flusso di Azure supporta la scrittura in parallelo come opzione. Questa opzione consente di avere topologie [perfettamente parallele](stream-analytics-parallelization.md#embarrassingly-parallel-jobs), dove più partizioni di output vengono scritte in parallelo sulla tabella di destinazione. L'abilitazione di questa opzione in Analisi di flusso di Azure, tuttavia, potrebbe non essere sufficiente per ottenere una maggiore velocità effettiva, in quanto dipende in modo significativo dalla configurazione del database SQL di Azure e dallo schema tabella. La scelta degli indici, la chiave di clustering, il fattore di riempimento indice e la compressione possono influire sui tempi di caricamento delle tabelle. Per altre informazioni su come ottimizzare il database SQL di Azure per migliorare la query e caricare le prestazioni in base ai benchmark interni, vedere le [linee guida sulle prestazioni del database SQL](../sql-database/sql-database-performance-guidance.md). L'ordinamento delle operazioni di scrittura non è garantito quando si scrive in parallelo sul Database SQL di Azure.

Ecco alcune configurazioni all'interno di ogni servizio che consentono di migliorare la velocità effettiva complessiva della soluzione.

## <a name="azure-stream-analytics"></a>Analisi di flusso di Azure

- **Ereditare il partizionamento** – Questa opzione di configurazione dell'output di SQL consente di ereditare lo schema di partizione del passaggio precedente della query o dell'input. Con questa impostazione attivata, la scrittura in una tabella basata su disco e con una topologia [perfettamente parallela](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) per il processo, ci si aspetta di vedere migliori velocità effettive. Questo partizionamento avviene automaticamente per molti altri [output](stream-analytics-parallelization.md#partitions-in-sources-and-sinks). Il blocco di tabella (TABLOCK) viene inoltre disabilitato per gli inserimenti bulk eseguiti con questa opzione.

> [!NOTE] 
> Quando sono presenti più di 8 partizioni di input, ereditare l'input dello schema di partizione potrebbe non essere una scelta appropriata. Il limite massimo è stato rilevato in una tabella con una colonna di identità singola e un indice cluster. In questo caso, è consigliabile usare [into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 nella query per specificare in modo esplicito il numero di writer di output. In base allo schema e alla scelta degli indici, le osservazioni possono variare.

- **Dimensione batch** - la configurazione di output SQL consente di specificare le dimensioni massime del batch in un output Analisi di flusso SQL di Azure, in base alla natura del carico di lavoro/tabella di destinazione. La dimensione del batch indica il numero massimo di record inviati con ogni transazione di inserimento di massa. Negli indici columnstore cluster, le dimensioni del batch intorno ai [100K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) consentono una maggiore parallelizzazione, una registrazione minima e le ottimizzazioni dei blocchi. Nelle tabelle basate su disco, uguale o inferiore a 10K (impostazione predefinita), può essere ottimale per la soluzione, in quanto le dimensioni di batch maggiori possono attivare l'escalation blocchi durante gli inserimenti di massa.

- **Sintonizzazione del messaggio di Input** – se è stato ottimizzato tramite l'ereditare il partizionamento e la dimensione del batch, l'aumento del numero di eventi di input per ogni messaggio e partizione consente di aumentare ulteriormente la velocità effettiva di scrittura. La sintonizzazione del messaggio di input consente alle dimensioni del batch all'interno delle Analisi di flusso di Azure di arrivare alla dimensione del batch specificata, migliorando così la velocità effettiva. Questa operazione può essere eseguita usando la [compressione](stream-analytics-define-inputs.md) o aumentando le dimensioni dei messaggi di input in EventHub o BLOB.

## <a name="sql-azure"></a>SQL Azure

- **Tabelle e indici partizionati** – Usare una tabella SQL [partizionata](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) e gli indici partizionati sulla tabella con la stessa colonna come chiave di partizione (ad esempio PartitionId) può ridurre notevolmente le contese tra partizioni durante la scrittura. Per una tabella partizionata, è necessario creare una [funzione di partizione](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) e uno [schema di partizione](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) nel filegroup primario. Questo aumenterà anche la disponibilità dei dati esistenti mentre vengono caricati i dati nuovi. Il limite del log IO potrebbe raggiungere il valore basato sul numero di partizioni che può essere aumentato eseguendo l'aggiornamento dello SKU.

- **Evitare le violazioni della chiave univoca** – Se si verificano [più messaggi di avviso di violazione della chiave](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) nel Log di Analisi di flusso di Azure, assicurarsi che il processo non è interessato da violazioni di vincolo uniche che possono verificarsi durante i casi di ripristino. Questo può essere evitato se si imposta sugli indici l'opzione [IGNORE\_CHIAVE\_DUP](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output).

## <a name="azure-data-factory-and-in-memory-tables"></a>Data Factory di Azure e tabelle in memoria

- **Tabella in memoria come tabella temporanea** : le [tabelle in memoria](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) consentono carichi di dati molto veloci, ma i dati devono rientrare nella memoria. I benchmark mostrano che il caricamento di massa da una tabella in memoria in una tabella basata su disco è circa 10 volte più veloce rispetto all'inserimento di massa diretto tramite un unico scrittore nella tabella basata su disco con una colonna identity e un indice cluster. Per sfruttare queste prestazioni di inserimento di massa, configurare un [processo di copia usando Azure Data Factory](../data-factory/connector-azure-sql-database.md) che copia i dati dalla tabella in memoria alla tabella basata su disco.

## <a name="avoiding-performance-pitfalls"></a>Evitare i problemi di prestazioni
L'inserimento bulk dei dati è molto più veloce del caricamento dei dati con singoli inserimenti, poiché il sovraccarico ripetuto del trasferimento dei dati, l'analisi dell'istruzione INSERT, l'esecuzione dell'istruzione e l'emissione di un record di transazione vengono evitati. Viene invece usato un percorso più efficiente nel motore di archiviazione per trasmettere i dati. Il costo di installazione di questo percorso è tuttavia molto più elevato rispetto a una singola istruzione INSERT in una tabella basata su disco. Il punto di pareggio è in genere di circa 100 righe, oltre il quale il caricamento bulk è quasi sempre più efficiente. 

Se la frequenza degli eventi in ingresso è bassa, è possibile creare facilmente dimensioni batch inferiori a 100 righe, rendendo inefficiente l'inserimento bulk e utilizzando una quantità eccessiva di spazio su disco. Per ovviare a questa limitazione, è possibile eseguire una di queste operazioni:
* Creare un [trigger](/sql/t-sql/statements/create-trigger-transact-sql) instead of per utilizzare l'inserimento semplice per ogni riga.
* Usare una tabella temporanea in memoria, come descritto nella sezione precedente.

Un altro scenario di questo tipo si verifica quando si scrive in un indice columnstore non cluster (NCCI), in cui gli inserimenti bulk più piccoli possono creare troppi segmenti, che possono causare l'arresto anomalo dell'indice. In questo caso, è consigliabile usare un indice columnstore cluster.

## <a name="summary"></a>Riepilogo

In riepilogo, con la funzionalità di output partizionato in Analisi di flusso di Azure per l'output SQL, l'allineamento del processo di parallelizzazione con una tabella partizionata in SQL Azure dovrebbe fornire miglioramenti significativi della velocità effettiva. Sfruttando Azure Data Factory per orchestrare lo spostamento dei dati da una tabella in memoria in tabelle basate su disco, è possibile ottenere guadagni di velocità effettiva in ordine di grandezza. Se fattibile, il miglioramento della densità dei messaggi può essere un fattore determinante per migliorare la velocità effettiva complessiva.
