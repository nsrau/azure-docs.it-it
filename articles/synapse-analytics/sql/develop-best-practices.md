---
title: Procedure consigliate di sviluppo per sinapsi SQL
description: Raccomandazioni e procedure consigliate che è opportuno tenere presente durante lo sviluppo di sinapsi SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086352"
---
# <a name="development-best-practices-for-synapse-sql"></a>Procedure consigliate di sviluppo per sinapsi SQL
Questo articolo descrive le procedure consigliate e linee guida quando si sviluppa una soluzione di data warehouse. 

## <a name="sql-pool-development-best-practices"></a>Procedure consigliate per lo sviluppo di pool SQL

### <a name="reduce-cost-with-pause-and-scale"></a>Ridurre i costi con sospensione e scalabilità

Per altre informazioni sulla riduzione dei costi tramite sospensione e scalabilità, vedere [Gestire la potenza di calcolo](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Gestire le statistiche

Assicurarsi di aggiornare le statistiche ogni giorno o dopo ogni caricamento.  Sono sempre necessari compromessi tra le prestazioni e il costo di creazione e aggiornamento delle statistiche. Se si ritiene che la gestione di tutte le statistiche richieda troppo tempo, è più selettivo le colonne con le statistiche o le colonne che richiedono un aggiornamento frequente.  

Ad esempio, potrebbe essere necessario aggiornare le colonne date, in cui è possibile aggiungere nuovi valori su base giornaliera. 

> [!NOTE]
> Per ottenere il massimo vantaggio, è necessario disporre di statistiche sulle colonne incluse nei join, nelle colonne utilizzate nella clausola WHERE e nelle colonne presenti in GROUP BY.

Vedere anche [gestire le statistiche delle tabelle](develop-tables-statistics.md), [Create Statistics](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="hash-distribute-large-tables"></a>Utilizzare la distribuzione hash per le tabelle di grandi dimensioni

Per impostazione predefinita, per le tabelle viene usata la distribuzione round robin.  Questo consente agli utenti di iniziare a creare tabelle senza dover decidere come distribuire le tabelle.  Le tabelle Round Robin possono essere sufficientemente efficienti per alcuni carichi di lavoro. Tuttavia, nella maggior parte dei casi, la selezione di una colonna di distribuzione sarà molto migliore.  

L'unione in join di due tabelle dei fatti di grandi dimensioni è l'esempio più comune di come una tabella distribuita in base a una colonna possa offrire prestazioni migliori rispetto a una tabella round robin.  

Se, ad esempio, si dispone di una tabella Orders distribuita da order_id e di una tabella Transactions, anch ' essa distribuita da order_id, quando si aggiunge la tabella Orders alla tabella Transactions in order_id, questa query diventa una query pass-through. 

Ciò significa che si eliminano le operazioni di spostamento dei dati.  Un numero inferiore di passaggi consente una maggiore velocità di esecuzione delle query.  Anche un numero inferiore di operazioni di spostamento consente query più veloci.

> [!TIP]
> Durante il caricamento di una tabella distribuita, assicurarsi che i dati in entrata non siano ordinati nella chiave di distribuzione, perché questo rallenterebbe il caricamento.  

Per ulteriori informazioni su come selezionare una colonna di distribuzione, vedere i collegamenti seguenti per ottenere un miglioramento delle prestazioni e la definizione di una tabella distribuita nella clausola WITH dell'istruzione CREATE Tables.

Vedere anche [Cenni preliminari sulle tabelle](develop-tables-overview.md), [distribuzione delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [selezione della distribuzione della tabella](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [create table come SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="do-not-over-partition"></a>Non creare un numero eccessivo di partizioni
Sebbene i dati di partizionamento possano essere efficaci per gestire i dati tramite il cambio della partizione o l'ottimizzazione delle analisi tramite l'eliminazione della partizione, la presenza di un numero eccessivo di partizioni può rallentare le query.  Spesso una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente nel pool SQL.  

> [!NOTE]
> Spesso una strategia di partizionamento a granularità elevata che può funzionare correttamente in SQL Server potrebbe non funzionare correttamente nel pool SQL.  

Un numero eccessivo di partizioni può anche ridurre l'efficacia degli indici columnstore cluster se ogni partizione contiene meno di 1 milione di righe. Il pool SQL esegue il partizionamento dei dati in 60 database. 

Quindi, se si crea una tabella con partizioni 100, il risultato sarà 6000 partizioni.  Ogni carico di lavoro è diverso, quindi la cosa migliore da fare è sperimentare diversi tipi di partizionamento per capire qual è la soluzione migliore per il carico di lavoro specifico.  

Una delle opzioni da considerare è l'uso di una granularità inferiore rispetto a quella che potrebbe avere funzionato in SQL Server.  Prendere ad esempio in considerazione l'uso di partizioni settimanali o mensili invece che giornaliere.

Vedere anche [partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Ridurre al minimo le dimensioni delle transazioni

Le istruzioni INSERT, UPDATE e DELETE vengono eseguite in una transazione e quando si verifica un errore è necessario eseguire il rollback.  Per ridurre al minimo il rischio di un rollback lungo, ridurre al minimo le dimensioni delle transazioni, quando possibile.  A tale scopo, è possibile suddividere in parti le istruzioni INSERT, UPDATE e DELETE.  

Se, ad esempio, si dispone di un inserimento che prevede un'ora, è possibile suddividere l'inserimento in quattro parti, riducendo quindi ogni esecuzione a 15 minuti.

> [!TIP]
> Sfruttare i casi speciali di registrazione minima, ad esempio CTAS, TRUNCATE, DROP TABLE o INSERT in tabelle vuote, in modo da ridurre il rischio di rollback.  

Un altro modo per eliminare i rollback consiste nell'usare operazioni solo sui metadati come il cambio di partizione per la gestione dati.  

Ad esempio, invece di eseguire un'istruzione DELETE per eliminare tutte le righe in una tabella in cui data_ordine corrisponde a ottobre 2001, è possibile creare una partizione mensile dei dati e quindi scambiare la partizione con i dati con una partizione vuota di un'altra tabella (vedere gli esempi relativi ad ALTER TABLE).  

Per le tabelle non partizionate, prendere in considerazione l'uso di CTAS per scrivere i dati da mantenere in una tabella, invece di usare DELETE.  Se un'operazione CTAS richiede la stessa quantità di tempo, è molto più sicura da eseguire perché prevede una registrazione delle transazioni minima e può essere annullata rapidamente, se necessario.

Vedere anche [informazioni sulle transazioni](develop-transactions.md), [ottimizzazione delle transazioni](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [partizionamento delle tabelle](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [create table As Select (CTAs)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Utilizzare colonne con dimensioni il più piccole possibili

Quando si definisce il codice DDL, per migliorare le prestazioni di query usare il più piccolo tipo di dati in grado di supportare i dati.  Questo aspetto è particolarmente importante per le colonne CHAR e VARCHAR.  

Se il valore più lungo in una colonna è di 25 caratteri, definire la colonna come VARCHAR(25).  Evitare di definire tutte le colonne di tipo carattere impostando una lunghezza predefinita elevata.  Definire inoltre le colonne come VARCHAR quando è sufficiente, invece di usare NVARCHAR.

Vedere anche [Cenni preliminari sulle tabelle](develop-tables-overview.md), [tipi di dati della tabella](develop-tables-data-types.md)e [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="optimize-clustered-columnstore-tables"></a>Ottimizzare le tabelle columnstore cluster

Gli indici columnstore cluster rappresentano uno dei modi più efficienti per archiviare i dati nel pool SQL.  Per impostazione predefinita, le tabelle nel pool SQL vengono create come cluster columnstore.  

Per ottenere prestazioni ottimali per le query sulle tabelle columnstore è importante che la qualità dei segmenti sia buona.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  

La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso.  Per istruzioni dettagliate su come rilevare e migliorare la qualità dei segmenti per le tabelle columnstore cluster, vedere l'articolo relativo alle cause degli [indici](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [columnstore e alla qualità degli indici columnstore scarso](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) .  

Poiché l'elevata qualità dei segmenti columnstore è importante, è consigliabile usare ID di utenti della classe di risorse media o grande per caricare i dati. L'uso di [unità di data warehouse](resource-consumption-models.md) inferiori implica l'intenzione di assegnare una classe di risorse di dimensioni superiori all'utente incaricato del caricamento.

Poiché le tabelle columnstore in genere non eseguono il push dei dati in un segmento columnstore compresso fino a quando non sono presenti più di 1 milione righe per tabella e ogni tabella del pool SQL viene partizionata in 60 tabelle, le tabelle columnstore non trarranno vantaggio da una query a meno che la tabella non contenga più di 60 milioni righe.  

> [!TIP]
> Per le tabelle con meno di 60 milioni righe, la presenza di un indice eliminando potrebbe non essere la soluzione ottimale.  

Inoltre, se si partizionano i dati, tenere presente che ogni partizione dovrà contenere 1 milione di righe per trarre vantaggio da un indice columnstore cluster.  Se una tabella contiene 100 partizioni, sarà necessario disporre di almeno 6 miliardi righe per trarre vantaggio da un archivio columnstore cluster (60 distribuzioni *100 partizioni* 1 milione righe).  

Se la tabella non contiene 6 miliardi righe, ridurre il numero di partizioni o prendere in considerazione l'uso di una tabella heap.  Potrebbe anche essere utile provare a verificare se è possibile ottenere prestazioni migliori usando una tabella heap con indici secondari anziché una tabella columnstore.

Quando si esegue una query su una tabella columnstore, l'esecuzione sarà più rapida se si selezionano solo le colonne necessarie.  

Vedere anche [indici di tabella](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Guida sugli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)e [ricompilazione degli indici columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="sql-on-demand-development-best-practices"></a>Procedure consigliate per lo sviluppo su richiesta SQL

### <a name="general-considerations"></a>Considerazioni generali

SQL su richiesta consente di eseguire query sui file negli account di archiviazione di Azure. Non dispone di funzionalità di archiviazione locale o di inserimento, ovvero tutti i file di destinazione della query sono esterni a SQL su richiesta. Pertanto, tutti gli elementi correlati alla lettura di file dall'archiviazione potrebbero avere un effetto sulle prestazioni di esecuzione delle query.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Trovare un account di archiviazione di Azure e SQL su richiesta

Per ridurre al minimo la latenza, colocare l'account di archiviazione di Azure e l'endpoint SQL su richiesta. Gli account di archiviazione e gli endpoint sottoposti a provisioning durante la creazione dell'area di lavoro si trovano nella stessa area.

Per ottenere prestazioni ottimali, se si accede ad altri account di archiviazione con SQL su richiesta, assicurarsi che si trovino nella stessa area. In caso contrario, si verifica un aumento della latenza per il trasferimento di rete dei dati dall'area remota all'area dell'endpoint.

### <a name="azure-storage-throttling"></a>Limitazione di archiviazione di Azure

Più applicazioni e servizi possono accedere all'account di archiviazione. Quando le operazioni di i/o al secondo combinate generate da applicazioni, servizi e carichi di lavoro SQL su richiesta superano i limiti dell'account di archiviazione, viene applicata la limitazione delle richieste di archiviazione. Quando si verifica la limitazione dell'archiviazione, esiste un effetto negativo significativo sulle prestazioni delle query.

Una volta rilevata la limitazione, SQL su richiesta dispone di una gestione incorporata di questo scenario. SQL su richiesta effettuerà richieste di archiviazione a un ritmo più lento fino a quando non viene risolta la limitazione delle richieste. 

Tuttavia, per un'esecuzione ottimale delle query, è consigliabile non sottoporre a stress l'account di archiviazione con altri carichi di lavoro durante l'esecuzione della query.

### <a name="prepare-files-for-querying"></a>Preparare i file per l'esecuzione di query

Se possibile, è possibile preparare i file per ottenere prestazioni migliori:

- Convertire CSV in parquet: parquet è un formato a colonne. Poiché è compresso, ha dimensioni di file inferiori rispetto ai file CSV con gli stessi dati e SQL su richiesta richiede meno tempo e richieste di archiviazione per leggerlo.
- Se una query è destinata a un singolo file di grandi dimensioni, sarà possibile suddividerla in più file più piccoli.
- Provare a mantenere le dimensioni del file CSV al di sotto di 10 GB.
- È preferibile disporre di file di dimensioni equivalenti per un singolo percorso OPENROWSET o una posizione di tabella esterna.
- Partizionare i dati archiviando le partizioni in cartelle o nomi di file diversi: selezionare [Usa funzioni filename e FilePath per individuare partizioni specifiche](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Usare le funzioni FileInfo e FilePath per individuare partizioni specifiche

I dati sono spesso organizzati in partizioni. È possibile richiedere a SQL su richiesta di eseguire query su file e cartelle specifiche. In questo modo verrà ridotto il numero di file e la quantità di dati che la query deve leggere ed elaborare. 

Di conseguenza, si otterranno prestazioni migliori. Per altre informazioni, vedere funzioni [filename](develop-storage-files-overview.md#filename-function) e [filePath](develop-storage-files-overview.md#filepath-function) ed esempi su come [eseguire una query su file specifici](query-specific-files.md).

Se i dati nell'archiviazione non sono partizionati, provare a partizionarlo per poter usare queste funzioni per ottimizzare le query destinate a tali file.

Quando si [eseguono query su tabelle Spark partizionate](develop-storage-files-spark-tables.md) da SQL su richiesta, la query verrà destinata automaticamente solo ai file necessari.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Usare CETAS per migliorare le prestazioni di esecuzione delle query e i join

[CETAS](develop-tables-cetas.md) è una delle funzionalità più importanti disponibili in SQL su richiesta. CETAS è un'operazione parallela che crea metadati della tabella esterna ed esporta il risultato della query SELECT in un set di file nell'account di archiviazione.

È possibile utilizzare CETAS per archiviare una parte di query spesso utilizzata, come le tabelle di riferimento Unite in join, in un nuovo set di file. In un secondo momento, è possibile aggiungere join a questa singola tabella esterna anziché ripetere i join comuni in più query. 

Quando CETAS genera file parquet, le statistiche vengono create automaticamente quando la prima query è destinata a questa tabella esterna e si ottengono prestazioni migliori.

### <a name="next-steps"></a>Passaggi successivi

Se sono necessarie informazioni non fornite in questo articolo, usare "Cerca documenti" sul lato sinistro della pagina per cercare tutti i documenti del pool SQL.  Il [Forum sul pool SQL](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) è una posizione in cui è possibile porre domande ad altri utenti e al gruppo di prodotti del pool SQL.  

Questo forum viene monitorato attivamente per garantire che venga fornita una risposta a tutte le domande, da un altro utente o da Microsoft.  Se si preferisce porre domande in Stack Overflow, è anche presente un [pool SQL di Azure stack overflow Forum](https://stackoverflow.com/questions/tagged/azure-sqldw).
 