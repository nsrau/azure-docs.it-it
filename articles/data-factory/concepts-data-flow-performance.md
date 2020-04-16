---
title: Guida all'ottimizzazione e prestazioni del flusso di dati
description: Informazioni sui fattori chiave che influiscono sulle prestazioni del mapping dei flussi di dati in Azure Data Factory.Learn about key factors that affect the performance of mapping data flows in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: c09a035c8994118b0fb116f357485766e05883ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418440"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guida all'ottimizzazione e alle prestazioni dei flussi di dati di mapping

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Il mapping dei flussi di dati in Azure Data Factory offre un'interfaccia senza codice per progettare, distribuire e orchestrare le trasformazioni dei dati su larga scala. Se non si ha familiarità con il mapping dei flussi di dati, vedere [Panoramica](concepts-data-flow-overview.md)del flusso di dati di mapping .

Quando si progettano e si testano i flussi di dati dall'esperienza utente ADF, assicurarsi di attivare la modalità di debug per eseguire i flussi di dati in tempo reale senza attendere il riscaldamento di un cluster. Per ulteriori informazioni, vedere [Modalità debug](concepts-data-flow-debug-mode.md).

Questo video mostra alcuni intervalli di esempio che trasformano i dati con i flussi di dati:This video shows some sample timings transforming data with data flows:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitoraggio delle prestazioni del flusso di datiMonitoring data flow performance

Durante la progettazione dei flussi di dati di mapping, è possibile unit testare ogni trasformazione facendo clic sulla scheda Anteprima dati nel pannello di configurazione. Dopo aver verificato la logica, testare il flusso di dati end-to-end come attività in una pipeline. Aggiungere un'attività Esegui flusso di dati e usare il pulsante Debug per testare le prestazioni del flusso di dati. Per aprire il piano di esecuzione e il profilo delle prestazioni del flusso di dati, fare clic sull'icona degli occhiali in "Azioni" nella scheda di output della pipeline.

![Monitoraggio flusso di dati](media/data-flow/mon002.png "Monitoraggio flusso di dati 2")

 È possibile usare queste informazioni per stimare le prestazioni del flusso di dati su origini dati di dimensioni diverse. Per ulteriori informazioni, vedere [Monitoraggio dei flussi](concepts-data-flow-monitoring.md)di dati di mapping .

![Monitoraggio del flusso di dati](media/data-flow/mon003.png "Monitoraggio flusso di dati 3")

 Per le esecuzioni di debug della pipeline, per un cluster a caldo è necessario circa un minuto di tempo di configurazione del cluster. Se si sta inizializzando il runtime di integrazione di Azure predefinito, il tempo di rotazione potrebbe richiedere circa 5 minuti.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumento delle dimensioni di calcolo in Azure Integration Runtime

Un runtime di integrazione con più core aumenta il numero di nodi negli ambienti di calcolo Spark e fornisce maggiore potenza di elaborazione per leggere, scrivere e trasformare i dati. Flussi di dati ADF utilizza Spark per il motore di calcolo. L'ambiente Spark funziona molto bene sulle risorse ottimizzate per la memoria.
* Provare un cluster **ottimizzato per** il calcolo se si desidera che la velocità di elaborazione sia superiore alla velocità di input.
* Provare un cluster **ottimizzato per** la memoria se si desidera memorizzare nella cache più dati in memoria. La memoria ottimizzata ha un prezzo-punto più alto per core rispetto a Compute Optimized, ma probabilmente si tradurrà in velocità di trasformazione più elevate.

![Nuovo IR](media/data-flow/ir-new.png "Nuovo IR")

Per altre informazioni su come creare un runtime di integrazione, vedere [Runtime di integrazione in Azure Data Factory.](concepts-integration-runtime.md)

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumentare le dimensioni del cluster di debug

Per impostazione predefinita, l'attivazione del debug usa il runtime di integrazione di Azure predefinito creato automaticamente per ogni data factory. Questo livello di messaggistica di archiviazione predefinito è impostato per otto core, quattro per un nodo driver e quattro per un nodo di lavoro, usando le proprietà di calcolo generali. Durante il test con dati di dimensioni maggiori, è possibile aumentare le dimensioni del cluster di debug creando un catalogo di termini di Azure con configurazioni più grandi e scegliere questo nuovo catalogo dei messaggi di ricambio di Azure quando si attiva il debug. Ciò indicherà ad ADF di usare questo sistema di controllo visivo di Azure per l'anteprima dei dati e il debug della pipeline con i flussi di dati.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Ridurre il tempo di avvio del calcolo del cluster con TTL

In Proprietà flusso di dati è presente una proprietà nel ricorso di Azure che consente di gestire un pool di risorse di elaborazione del cluster per la factory. Con questo pool è possibile inviare in sequenza le attività del flusso di dati per l'esecuzione. Una volta stabilito il pool, ogni processo successivo richiederà 1-2 minuti affinché il cluster Spark su richiesta esegua il processo. La configurazione iniziale del pool di risorse richiederà circa 6 minuti. Specificare la quantità di tempo per cui si desidera gestire il pool di risorse nell'impostazione TTL (Time-To-Live).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Ottimizzazione per il database SQL di Azure e Azure SQL Data Warehouse SynapseOptimizing for Azure SQL Database and Azure SQL Data Warehouse Synapse

### <a name="partitioning-on-source"></a>Partizionamento nell'origine

1. Vai alla scheda **Ottimizza** e seleziona **Imposta partizionamento**
1. Selezionare **Origine**.
1. In Numero di partizioni impostare il numero massimo di connessioni al database SQL di Azure.Under **Number of partitions**, set the maximum number of connections to your Azure SQL DB. È possibile provare un'impostazione superiore per ottenere connessioni parallele al database. Tuttavia, alcuni casi possono comportare prestazioni più veloci con un numero limitato di connessioni.
1. Selezionare se eseguire la partizione in base a una colonna di tabella specifica o a una query.
1. Se è stata selezionata l'opzione **Colonna**, selezionare la colonna della partizione.
1. Se è stata selezionata l'opzione **Query**, immettere una query che corrisponda allo schema di partizionamento della tabella di database. Questa query consente al motore di database di origine di sfruttare l'eliminazione delle partizioni. Le tabelle del database di origine non devono essere partizionate. Se l'origine non è già partizionata, ADF continuerà a usare il partizionamento dei dati nell'ambiente di trasformazione Spark in base alla chiave selezionata nella trasformazione Origine.

![Parte di origine](media/data-flow/sourcepart3.png "Parte di origine")

> [!NOTE]
> Una buona guida per scegliere il numero di partizioni per l'origine si basa sul numero di core impostati per il runtime di integrazione di Azure e moltiplicare tale numero per cinque. Ad esempio, se si sta trasformando una serie di file nelle cartelle ADLS e si intende utilizzare un prodotto a/ di classi 32 core di Azure, il numero di partizioni di destinazione è 32 x 5 e 160 partizioni.

### <a name="source-batch-size-input-and-isolation-level"></a>Dimensioni del batch di origine, input e livello di isolamentoSource batch size, input, and isolation level

In **Opzioni di origine** nella trasformazione di origine, le impostazioni seguenti possono influire sulle prestazioni:

* Dimensione batch indica ad ADF di archiviare i dati in set nella memoria Spark anziché riga per riga. Le dimensioni del batch sono un'impostazione facoltativa e le risorse potrebbero essere esaurite nei nodi di calcolo se non sono dimensionate correttamente. Se non si imposta questa proprietà verranno utilizzate le impostazioni predefinite del batch di memorizzazione nella cache di Spark.
* L'impostazione di una query consente di filtrare le righe nell'origine prima che arrivino in Flusso di dati per l'elaborazione. Questo può rendere più veloce l'acquisizione dei dati iniziali. Se si usa una query, è possibile aggiungere hint di query facoltativi per il database SQL di Azure, ad esempio READ UNCOMMITTED.
* Read uncommitted fornirà risultati di query più rapidi nella trasformazione OrigineRead uncommitted will provide faster query results on Source transformation

![origine](media/data-flow/source4.png "Source (Sorgente)")

### <a name="sink-batch-size"></a>Dimensione batch sink

Per evitare l'elaborazione riga per riga dei flussi di dati, impostare Dimensioni batch nella scheda Impostazioni per il database SQL di Azure e i sink di Azure SQL DW.To avoid row-by-row processing of your data flows, set **Batch size** in the Settings tab for Azure SQL DB and Azure SQL DW sinks. Se è impostata la dimensione del batch, ADF elabora le scritture del database in batch in base alle dimensioni fornite. Se non si imposta questa proprietà verranno utilizzate le impostazioni predefinite del batch di memorizzazione nella cache di Spark.

![Sink](media/data-flow/sink4.png "Sink")

### <a name="partitioning-on-sink"></a>Partizionamento sul sink

Anche se i dati non sono partizionati nelle tabelle di destinazione, è consigliabile partizionare i dati nella trasformazione sink. I dati partizionati spesso comportano un caricamento molto più veloce forzando l'utilizzo di tutte le connessioni con un singolo nodo/partizione. Passare alla scheda Ottimizza del sink e selezionare *Round Robin* partitioning per selezionare il numero ideale di partizioni da scrivere nel sink.

### <a name="disable-indexes-on-write"></a>Disabilitare gli indici in scritturaDisable indexes on write

Nella pipeline aggiungere [un'attività Stored procedure](transform-data-using-stored-procedure.md) prima dell'attività Flusso di dati che disabilita gli indici nelle tabelle di destinazione scritte dal sink. Dopo l'attività Flusso di dati, aggiungere un'altra attività di Stored procedure che abilita tali indici. In alternativa, è possibile utilizzare gli script di pre-elaborazione e post-elaborazione in un sink di database.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumentare le dimensioni del database SQL di Azure e DW

Pianificare un ridimensionamento dell'origine e eseguire il sink del database e dW SQL di Azure prima dell'esecuzione della pipeline per aumentare la velocità effettiva e ridurre al minimo la limitazione delle richieste di Azure quando si raggiungono i limiti DTU. Al termine dell'esecuzione della pipeline, ridimensionare i database alla frequenza di esecuzione normale.

* Tabella di origine del database SQL con 887k righe e 74 colonne in una tabella di database SQL con una singola trasformazione di colonna derivata richiede circa 3 min end-to-end utilizzando la memoria ottimizzata 80-core debug IRs di Azure.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Solo Azure Synapse SQL DW] Usare la gestione temporanea per caricare i dati in blocco tramite PolybaseUse staging to load data in bulk via Polybase

Per evitare inserimenti riga per riga nel DW, **selezionare Abilita gestione temporanea** nelle impostazioni sink in modo che ADF possa utilizzare [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase consente ad ADF di caricare i dati in blocco.
* Quando si esegue l'attività del flusso di dati da una pipeline, è necessario selezionare una posizione di archiviazione BLOB o ADLS Gen2 per eseguire temporaneamente i dati durante il caricamento bulk.

* L'origine file di 421MB file con 74 colonne in una tabella Synapse e una singola trasformazione di colonna derivata richiede circa 4 min end-to-end utilizzando la memoria 80-core debug IR di Azure.

## <a name="optimizing-for-files"></a>Ottimizzazione per i file

Ad ogni trasformazione, è possibile impostare lo schema di partizionamento che si desidera utilizzare per la data factory nella scheda Ottimizza. È consigliabile testare prima i sink basati su file mantenendo il partizionamento e le ottimizzazioni predefinite.

* Per i file più piccoli, è possibile che la scelta di un numero inferiore di partizioni a volte può funzionare meglio e più velocemente rispetto a chiedere a Spark di partizionare i file di piccole dimensioni.
* Se non si dispone di informazioni sufficienti sui dati di origine, scegliere *Round Robin* partitioning e impostare il numero di partizioni.
* Se i dati conto di colonne che possono essere chiavi hash buone, scegliere *Partizionamento hash*.

* Origine file con sink di file di un file di 421MB con 74 colonne e una singola trasformazione di colonna derivata richiede circa 2 min end-to-end utilizzando la memoria ottimizzata 80-core debug IR di Azure.

Quando si esegue il debug nell'anteprima dei dati e nel debug della pipeline, il limite e le dimensioni di campionamento per i set di dati di origine basati su file si applicano solo al numero di righe restituite, non al numero di righe lette. Ciò può influire sulle prestazioni delle esecuzioni di debug e, eventualmente, causare l'esito negativo del flusso.
* I cluster di debug sono cluster a nodo singolo di piccole dimensioni per impostazione predefinita e si consiglia di usare file di piccole dimensioni di esempio per il debug. Passare a Impostazioni di debug e puntare a un piccolo sottoinsieme di dati utilizzando un file temporaneo.

    ![Impostazioni di debug](media/data-flow/debugsettings3.png "Impostazioni di debug")

### <a name="file-naming-options"></a>Opzioni di denominazione dei file

Il modo più comune per scrivere dati trasformati nel mapping dei flussi di dati che scrivono BLOB o archivio file ADLS. Nel sink è necessario selezionare un set di dati che punti a un contenitore o a una cartella, non a un file denominato. Poiché il mapping del flusso di dati usa Spark per l'esecuzione, l'output viene suddiviso su più file in base allo schema di partizionamento.

Uno schema di partizionamento comune consiste nel scegliere _Output su singolo file_, che unisce tutti i file PART di output in un unico file nel sink. Questa operazione richiede che l'output si riduce a una singola partizione in un singolo nodo del cluster. È possibile esaurire le risorse del nodo del cluster se si combinano molti file di origine di grandi dimensioni in un singolo file di output.

Per evitare di esaurire le risorse del nodo di calcolo, mantenere lo schema predefinito e ottimizzato nel flusso di dati e aggiungere un'attività di copia nella pipeline che unisce tutti i file PART dalla cartella di output in un nuovo singolo file. Questa tecnica separa l'azione di trasformazione dall'unione dei file e ottiene lo stesso risultato dell'impostazione _di Output su un singolo file_.

### <a name="looping-through-file-lists"></a>Scorrere in ciclo gli elenchi di file

Un flusso di dati di mapping verrà eseguito meglio quando la trasformazione Origine scorre più file anziché eseguire un ciclo tramite l'attività For Each. È consigliabile usare caratteri jolly o elenchi di file nella trasformazione di origine. Il processo flusso di dati verrà eseguito più velocemente consentendo il ciclo si verifichi all'interno del cluster Spark. Per ulteriori informazioni, vedere [Carattere jolly nella trasformazione Origine](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Ad esempio, se si dispone di un elenco di file di dati di luglio 2019 che si desidera elaborare in una cartella nell'archivio BLOB, di seguito è riportato un carattere jolly che è possibile usare nella trasformazione Origine.For example, if you have a list of data files from July 2019 that you wish to process in a folder in Blob Storage, below is a wildcard you can use in your Source transformation.

```DateFiles/*_201907*.txt```

Utilizzando i caratteri jolly, la pipeline conterrà solo un'attività Flusso di dati. Questa funzione sarà migliore rispetto a una ricerca nell'archivio BLOB che quindi scorre tutti i file corrispondenti usando un ForEach con un'attività Esegui flusso di dati all'interno.

### <a name="optimizing-for-cosmosdb"></a>Ottimizzazione per CosmosDB

L'impostazione della velocità effettiva e delle proprietà batch nei sink CosmosDB ha effetto solo durante l'esecuzione di tale flusso di dati da un'attività del flusso di dati della pipeline. Le impostazioni di raccolta originali verranno rispettate da CosmosDB dopo l'esecuzione del flusso di dati.

* Dimensioni batch: calcolare la dimensione approssimativa della riga dei dati e assicurarsi che rowSize e dimensioni batch siano inferiori a due milioni. In caso affermativo, aumentare le dimensioni del batch per ottenere una velocità effettiva migliore
* Velocità effettiva: impostare un'impostazione di velocità effettiva più elevata per consentire ai documenti di scrivere più velocemente in CosmosDB. Tenere presente i costi RU più elevati in base a un'impostazione di velocità effettiva elevata.
*   Budget di velocità effettiva di scrittura: usare un valore inferiore a quello delle RU totali al minuto. Se si dispone di un flusso di dati con un numero elevato di partizioni Spark, l'impostazione di una velocità effettiva di budget consentirà un maggiore equilibrio tra tali partizioni.

## <a name="join-performance"></a>Prestazioni di join

La gestione delle prestazioni dei join nel flusso di dati è un'operazione molto comune che verrà eseguita durante l'intero ciclo di vita delle trasformazioni dei dati. In ADF, data flows do not require data to be sorted prior to joins as these operations are performed as hash joins in Spark. Tuttavia, è possibile trarre vantaggio da un miglioramento delle prestazioni con l'ottimizzazione di join "Broadcast". In questo modo si eviteranno gli elementi casuali spingendo verso il basso il contenuto di entrambi i lati della relazione di join nel nodo Spark. Questo funziona bene per le tabelle più piccole che vengono utilizzate per le ricerche di riferimento. Le tabelle più grandi che potrebbero non rientrare nella memoria del nodo non sono buoni candidati per l'ottimizzazione broadcast.

Un'altra ottimizzazione del join consiste nel creare i join in modo da evitare la tendenza di Spark a implementare i cross join. Ad esempio, quando si includono valori letterali nelle condizioni di join, Spark potrebbe considerarlo un requisito per eseguire prima un prodotto cartesiano completo, quindi escludere i valori uniti. Tuttavia, se si garantisce di disporre di valori di colonna su entrambi i lati della condizione di join, è possibile evitare questo prodotto cartesiano indotto da Spark e migliorare le prestazioni dei join e dei flussi di dati.

## <a name="next-steps"></a>Passaggi successivi

Vedere altri articoli del flusso di dati relativi alle prestazioni:See other Data Flow articles related to performance:

- [Scheda Ottimizza flusso di dati](concepts-data-flow-overview.md#optimize)
- [Attività flusso di datiData Flow activity](control-flow-execute-data-flow-activity.md)
- [Monitorare le prestazioni del flusso di datiMonitor Data Flow performance](concepts-data-flow-monitoring.md)
