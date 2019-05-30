---
title: Guida per il mapping di flusso di dati delle prestazioni e ottimizzazione in Azure Data Factory | Microsoft Docs
description: Informazioni sui fattori chiave che influiscono sulle prestazioni dei flussi di dati in Azure Data Factory quando si usa il Mapping di flussi di dati.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 90c7e4653b879c2432f08506cea08646e84bb69a
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297711"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapping delle prestazioni di flussi di dati e l'ottimizzazione manuale

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory il Mapping di flusso dei dati forniscono un'interfaccia browser senza codice per progettare, distribuire e orchestrare le trasformazioni dei dati su larga scala.

> [!NOTE]
> Se non si ha familiarità con Azure Data factory di Mapping di flusso dei dati in generale, vedere [Cenni preliminari sui flussi di dati](concepts-data-flow-overview.md) prima di leggere questo articolo.
>

> [!NOTE]
> Durante la progettazione e test di flussi di dati dalla UI ADF, assicurarsi di attivare l'opzione di Debug in modo che sia possibile eseguire i flussi di dati in tempo reale senza tempi di attesa per un cluster per il riscaldamento.
>

![Eseguire il debug sul pulsante](media/data-flow/debugb1.png "eseguire il Debug")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Ottimizzazione per il Database SQL di Azure e Azure SQL Data Warehouse

![Parte di origine](media/data-flow/sourcepart2.png "parte dell'origine")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>È possibile far corrispondere il partizionamento dei dati per il partizionamento del database di origine basato su una chiave di colonna nella tabella di database nella trasformazione origine Spark

* Passare a "Optimize" e selezionare "Origine". Impostare una colonna di tabella specifica o un tipo in una query.
* Se si sceglie "column", quindi selezionare la colonna di partizione.
* Inoltre, impostare il numero massimo di connessioni per database SQL di Azure. È possibile provare un'impostazione più alta per ottenere connessioni parallele ai database. Tuttavia, alcuni casi possono comportare prestazioni più veloci con un numero limitato di connessioni.

### <a name="set-batch-size-and-query-on-source"></a>Impostare le dimensioni del batch ed eseguire una query sull'origine

![Source](media/data-flow/source4.png "Source")

* Impostazione delle dimensioni batch indicherà a Azure Data factory per archiviare i dati nei set nella memoria anziché row-by-row. È un'impostazione facoltativa e esauriscano le risorse nei nodi di calcolo se non sono configurati correttamente.
* L'impostazione di una query può consentono di filtrare il diritto di righe nell'origine prima di raggiungere anche per il flusso di dati per l'elaborazione, ciò può complicare l'acquisizione dei dati iniziale più rapido.
* Se si usa una query, è possibile aggiungere hint per la query facoltativo per il database SQL di Azure, ad esempio READ UNCOMMITTED

### <a name="set-sink-batch-size"></a>Impostare le dimensioni del batch sink

![Sink](media/data-flow/sink4.png "Sink")

* Per evitare un'elaborazione row-by-row di floes i dati, impostare le "dimensioni del Batch" nelle impostazioni del sink per il database SQL di Azure. In questo modo che Azure Data factory al database di processo scrive in batch in base alla dimensione fornita.

### <a name="set-partitioning-options-on-your-sink"></a>Set di opzioni sul sink di partizionamento

* Anche se non si dispone dei dati partizionati in tabelle di database SQL di Azure di destinazione, passare alla scheda Ottimizza e partizionamento del set.
* Molto spesso, è sufficiente indica a Azure Data factory per usare il partizionamento in cluster Spark esecuzione i risultati in caricamento anziché forzare tutte le connessioni da una partizione o un nodo singola di dati molto più veloci di Round Robin.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Aumentare le dimensioni del motore di calcolo nel Runtime di integrazione di Azure

![Nuovo runtime di integrazione](media/data-flow/ir-new.png "nuovo runtime di integrazione")

* Aumentare il numero di memorie centrali, che aumenterà il numero di nodi e offrono maggiore potenza di calcolo per eseguire query e scrittura al database SQL di Azure.
* Provare le opzioni di "Calcolo ottimizzato" e "Ottimizzate per la memoria" per applicare più risorse ai nodi di calcolo.

### <a name="unit-test-and-performance-test-with-debug"></a>Unit test e test delle prestazioni con il debug

* Quando i flussi di dati testing unità, impostare il pulsante "Dati flusso di Debug" su ON.
* All'interno di progettazione del flusso di dati, utilizzare la scheda di anteprima dei dati sulle trasformazioni per visualizzare i risultati della logica di trasformazione.
* Unit test, i dati di fluiscono dalla finestra di progettazione della pipeline per l'inserimento di un'attività flusso di dati in Progettazione pipeline canvas e usare il pulsante "Debug" per eseguire il test.
* Test in modalità di debug funzionerà con un ambiente cluster preparata in tempo reale senza la necessità di attendere per un cluster-in-time spin-up.

### <a name="disable-indexes-on-write"></a>Disabilitazione degli indici durante la scrittura
* Usare un'attività di routine Azure Data factory pipeline archiviata prima dell'attività flusso di dati che disabilita gli indici nelle tabelle di destinazione che vengono scritti dal Sink.
* Dopo l'attività flusso di dati, aggiungere un'altra attività di stored procedure che abilitato tali indici.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Aumentare le dimensioni del database SQL di Azure
* Pianificare un ridimensionamento dell'origine e sink Azure SQL DB prima dell'esecuzione di pipeline per aumentare la velocità effettiva e ridurre al minimo la limitazione Azure dopo aver raggiunto DTU limita.
* Al termine dell'esecuzione della pipeline, è possibile ridimensionare i database al loro frequenza di esecuzione normale.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Ottimizzazione per Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Utilizzare Gestione temporanea per caricare dati in blocco tramite Polybase

* Per evitare un'elaborazione row-by-row di floes i dati, impostare l'opzione di "Staging" nelle impostazioni del Sink in modo che Azure Data factory è possibile usare Polybase per evitare gli inserimenti row-by-row in data Warehouse. Ciò indicherà a Azure Data factory per usare Polybase in modo che i dati possono essere caricati in bulk.
* Quando si esegue l'attività del flusso di dati da una pipeline, tramite la gestione temporanea è attivato, sarà necessario selezionare il percorso dell'archivio Blob di staging dati per il caricamento bulk.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Aumentare le dimensioni di Azure SQL DW

* Pianificare un ridimensionamento dell'origine e sink Azure SQL Data Warehouse prima di eseguire la pipeline per aumentare la velocità effettiva e ridurre al minimo la limitazione Azure dopo aver raggiunto i limiti delle DWU.

* Al termine dell'esecuzione della pipeline, è possibile ridimensionare i database al loro frequenza di esecuzione normale.

## <a name="optimize-for-files"></a>Ottimizzare per i file

* È possibile controllare il numero di partizioni che verrà usato Azure Data factory. In ogni trasformazione origine e Sink, nonché ogni trasformazione singoli, è possibile impostare uno schema di partizionamento. Per i file più piccoli, può risultare utile selezione "Una partizione singola" possono talvolta funziona meglio e più veloci di chiedere di Spark per partizionare i file di piccole dimensioni.
* Se non si dispone di sufficienti informazioni sui dati di origine, è possibile scegliere "Round Robin" partizionamento e impostare il numero di partizioni.
* Se si Esplora i dati e che si disponga di colonne che possono essere chiavi hash valida, usare l'opzione di partizionamento Hash.

### <a name="file-naming-options"></a>Opzioni di denominazione di file

* La natura predefinito della scrittura di dati trasformati in Azure Data factory di Mapping di flusso dei dati consiste nello scrivere un set di dati che dispone di un Blob o un servizio collegato Azure Data Lake Store. È consigliabile impostare tale set di dati in modo che punti a una cartella o contenitore, non è un file denominato.
* Uso di flussi di dati in Azure Databricks Spark per l'esecuzione, il che significa che l'output viene suddiviso su più file di base predefinito sia Spark partizionamento o il partizionamento dello schema che si è scelto in modo esplicito.
* Un'operazione molto comune nei flussi di dati di Azure Data factory consiste nello scegliere "In un singolo file di Output" in modo che tutti i file di parte di output vengono uniti in un singolo file di output.
* Tuttavia, questa operazione richiede che consente di ridurre l'output a una singola partizione su un singolo nodo del cluster.
* Tenere a mente quando si sceglie questa opzione più diffusi. Se si combinano molti file di grandi dimensioni di origine in una partizione del file di output singolo, è possibile eseguire esaurisce le risorse di nodo del cluster.
* Per evitare l'esaurimento delle risorse di nodo di calcolo, è possibile mantenere il valore predefinito o esplicito schema di partizionamento in Azure Data factory, in modo da ottimizzare per le prestazioni, e quindi aggiungere un'attività di copia successive della pipeline che unisce tutta la parte dei file dalla cartella dell'output di un singolo nuovo file. In pratica, questa tecnica separa l'azione di trasformazione dall'unione di file e consente di ottenere lo stesso risultato dell'impostazione di "output in un singolo file".

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli del flusso di dati:

- [Cenni preliminari sul flusso di dati](concepts-data-flow-overview.md)
- [Attività del flusso di dati](control-flow-execute-data-flow-activity.md)

