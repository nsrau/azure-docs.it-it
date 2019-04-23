---
title: Indicizzazione di tabelle in Azure SQL Data Warehouse | Microsoft Azure
description: Raccomandazioni ed esempi per l'indicizzazione di tabelle in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/18/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: eab64d9494ef2d2838e16c55eed6ecf0db9736e9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797942"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indicizzazione di tabelle in SQL Data Warehouse

Raccomandazioni ed esempi per l'indicizzazione di tabelle in Azure SQL Data Warehouse.

## <a name="index-types"></a>Tipi di indice

SQL Data Warehouse offre diverse opzioni di indicizzazione, inclusi [indici columnstore cluster](/sql/relational-databases/indexes/columnstore-indexes-overview), [indici cluster e indici non cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) e un'opzione non di indice nota anche come [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Per creare una tabella con un indice, vedere la documentazione su [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse).

## <a name="clustered-columnstore-indexes"></a>Indici columnstore cluster

Per impostazione predefinita, SQL Data Warehouse crea un indice columnstore cluster quando non vengono specificate opzioni di indice in una tabella. Le tabelle columnstore cluster offrono sia il livello massimo di compressione dei dati che le migliori prestazioni query generali.  Le tabelle columnstore cluster garantiscono in genere prestazioni migliori rispetto alle tabelle heap o con indice cluster e rappresentano la scelta migliore in caso di tabelle di grandi dimensioni.  Per questi motivi, l'indice columnstore cluster è il modo migliore per iniziare quando non si è certi di come indicizzare una tabella.  

Per creare una tabella columnstore cluster è sufficiente specificare CLUSTERED COLUMNSTORE INDEX nella clausola WITH o lasciare la clausola WITH disabilitata:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

In alcuni scenari l'indice columnstore cluster potrebbe non essere la scelta ideale:

- Le tabelle columnstore non supportano varchar(max), nvarchar(max) e varbinary(max). È consigliabile usare tabelle heap o di indici cluster.
- Le tabelle columnstore potrebbero risultare meno efficiente per i dati temporanei. È consigliabile usare tabelle heap oppure tabelle temporanee.
- Tabelle di piccole dimensioni con meno di 60 milioni di righe. È consigliabile usare tabelle heap.

## <a name="heap-tables"></a>Tabelle heap

Quando si inseriscono temporaneamente i dati in SQL Data Warehouse, è probabile che una tabella heap rende più veloce il processo complessivo. Questo perché il caricamento negli heap è più veloce rispetto alle tabelle degli indici e in alcuni casi è possibile eseguire la lettura successiva dalla cache.  Se si caricano i dati solo per inserirli temporaneamente prima di eseguire altre trasformazioni, il caricamento della tabella in una tabella heap è molto più rapido del caricamento dei dati in una tabella columnstore cluster. Anche il caricamento dei dati in una [tabella temporanea](sql-data-warehouse-tables-temporary.md) risulta più veloce del caricamento di una tabella in un archivio permanente.  

Per le tabelle di ricerca di piccole dimensioni, minore di 60 milioni di righe, spesso le tabelle heap sono significativi.  Le tabelle columnstore cluster iniziano a raggiungere la compressione ottimale quando è presente più di 60 milioni di righe.

Per creare una tabella heap è sufficiente specificare HEAP nella clausola WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Indici cluster e non cluster

Gli indici cluster possono offrire prestazioni migliori rispetto alle tabelle columnstore cluster quando è necessario recuperare rapidamente una singola riga. Per le query in cui è necessario eseguire la ricerca in una sola riga o comunque in un numero di righe molto ridotto a velocità elevata, è consigliabile usare indici cluster o indici non cluster secondari. Gli indici cluster hanno lo svantaggio di risultare utili solo per le query con un filtro molto selettivo nella colonna dell'indice cluster. Per migliorare i filtri nelle altre colonne è possibile aggiungere un indice non cluster a tali colonne. Tuttavia, ogni indice che viene aggiunto a una tabella richiede spazio e tempo di elaborazione aggiuntivi.

Per creare una tabella di indici cluster è sufficiente specificare CLUSTERED INDEX nella clausola WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Per aggiungere un indice non cluster in una tabella, usare la sintassi seguente:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Ottimizzazione degli indici columnstore cluster

I dati delle tabelle columnstore cluster sono organizzati in segmenti.  Una qualità elevata dei segmenti è fondamentale per ottenere prestazioni ottimali delle query in una tabella columnstore.  La qualità dei segmenti si può misurare in base al numero di righe in un gruppo di righe compresso.  Per una qualità ottimale dei segmenti devono essere presenti almeno 100.000 righe per ogni gruppo di righe compresso. Le prestazioni migliorano quanto più il numero di righe si avvicina a 1.048.576, ovvero il numero massimo di righe che un gruppo di righe può contenere.

La vista seguente può essere creata e usata nel sistema per calcolare il numero medio di righe per ogni gruppo di righe e per identificare eventuali indici columnstore cluster non ottimali.  L'ultima colonna di questa vista viene generata come istruzione SQL che può essere usata per la ricompilazione degli indici.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Dopo aver creato la vista, eseguire la query per identificare le tabelle con gruppi di righe con meno di 100.000 righe. Per una qualità ancora superiore dei segmenti, è consigliabile aumentare la soglia oltre le 100.000 righe.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Dopo avere eseguito la query, è possibile iniziare a esaminare i dati e analizzare i risultati. Questa tabella illustra gli elementi da cercare nell'analisi dei gruppi di righe.

| Colonna | Come usare questi dati |
| --- | --- |
| [table_partition_count] |Se la tabella è partizionata, è prevedibile che sia presente un numero maggiore di gruppi di righe aperti. Teoricamente, a ogni partizione nella distribuzione può essere associato un gruppo di righe aperto. Includere questo fattore nell'analisi. Una tabella di piccole dimensioni partizionata può essere ottimizzata rimuovendo completamente il partizionamento e migliorando così la compressione. |
| [row_count_total] |Numero totale di righe per la tabella. Ad esempio, è possibile usare questo valore per calcolare la percentuale di righe nello stato compresso. |
| [row_count_per_distribution_MAX] |Se tutte le righe sono distribuite uniformemente, questo valore sarà il numero di righe per ogni distribuzione. Confrontare questo valore con compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Numero totale di righe nel formato columnstore per la tabella. |
| [COMPRESSED_rowgroup_rows_AVG] |Se il numero medio di righe è notevolmente inferiore al numero massimo di righe per un gruppo di righe, è consigliabile usare le istruzioni CTAS o ALTER INDEX REBUILD per comprimere nuovamente i dati. |
| [COMPRESSED_rowgroup_count] |Numero di gruppi di righe nel formato columnstore. Se questo numero è molto elevato in relazione la tabella, è un indicatore che la densità del columnstore è bassa. |
| [COMPRESSED_rowgroup_rows_DELETED] |Le righe vengono eliminate in modo logico nel formato columnstore. Se il numero è elevato rispetto alle dimensioni della tabella, provare a ricreare la partizione o a ricompilare l'indice, perché in questo modo vengono eliminate fisicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Usare questa colonna insieme alle colonne AVG e MAX per comprendere l'intervallo di valori per i gruppi di righe nel columnstore. Un numero basso oltre la soglia di caricamento, ad esempio 102.400 per ogni distribuzione di partizioni allineate, suggerisce che è possibile ottimizzare il caricamento dei dati. |
| [COMPRESSED_rowgroup_rows_MAX] |Come sopra. |
| [OPEN_rowgroup_count] |I gruppi di righe aperti sono normali. È ragionevole prevedere un gruppo di righe aperto (OPEN) per ogni distribuzione di tabella (60). Un numero eccessivo suggerisce il caricamento di dati tra le partizioni. Verificare la strategia di partizionamento per assicurarsi che sia valida. |
| [OPEN_rowgroup_rows] |Ogni gruppo di righe può contenere al massimo 1.048.576 righe. Usare questo valore per controllare l'attuale livello di riempimento dei gruppi di righe aperti. |
| [OPEN_rowgroup_rows_MIN] |I gruppi aperti indicano che i dati vengono caricati gradualmente nella tabella o che il carico precedente ha distribuito le righe rimanenti in questo gruppo di righe. Usare le colonne MIN, MAX e AVG per visualizzare la quantità di dati rimasta nei gruppi di righe OPEN. Per le tabelle di piccole dimensioni potrebbe essere il 100% di tutti i dati. In tal caso, usare ALTER INDEX REBUILD per forzare i dati nel columnstore. |
| [OPEN_rowgroup_rows_MAX] |Come sopra. |
| [OPEN_rowgroup_rows_AVG] |Come sopra. |
| [CLOSED_rowgroup_rows] |Esaminare le righe dei gruppi di righe chiusi come controllo di integrità. |
| [CLOSED_rowgroup_count] |Il numero di gruppi di righe chiusi deve essere basso o pari a zero. I gruppi di righe chiusi possono essere convertiti in gruppi di righe compressi usando l'istruzione ALTER INDEX... Comando REORGANIZE. In genere questa operazione non è tuttavia richiesta. I gruppi chiusi vengono convertiti automaticamente in gruppi di righe columnstore dal processo in background del motore di tuple. |
| [CLOSED_rowgroup_rows_MIN] |I gruppi di righe chiusi devono avere una velocità di riempimento molto elevata. Se la velocità di riempimento per un gruppo di righe chiuso è bassa, è necessario analizzare ulteriormente il columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Come sopra. |
| [CLOSED_rowgroup_rows_AVG] |Come sopra. |
| [Rebuild_Index_SQL] |Codice SQL per la ricompilazione dell'indice columnstore per una tabella |

## <a name="causes-of-poor-columnstore-index-quality"></a>Possibili cause di una qualità scadente dell'indice columnstore

Se sono state identificate tabelle di qualità scadente, è consigliabile identificarne la causa radice.  Di seguito sono riportate altre cause comuni della qualità scadente dei segmenti:

1. Utilizzo elevato di memoria durante la compilazione dell'indice
2. Volume elevato di operazioni DML
3. Operazioni di caricamento di piccole dimensioni o con un flusso irregolare 
4. Troppe partizioni

I fattori seguenti possono far sì che un indice columnstore abbia un numero di righe notevolmente inferiore alla cifra ottimale di 1 milione per ogni gruppo di righe. E possono anche far sì che le righe vengano inserite nel gruppo di righe differenziale anziché nel gruppo di righe compresso.

### <a name="memory-pressure-when-index-was-built"></a>Utilizzo elevato di memoria durante la compilazione dell'indice

Il numero di righe per ogni gruppo di righe compresso è direttamente correlato alla larghezza della riga e alla quantità di memoria disponibile per l'elaborazione del gruppo di righe.  Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne.  La procedura consigliata consiste quindi nel fare in modo che la sessione che sta scrivendo nelle tabelle di indice columnstore abbia accesso alla maggiore quantità di memoria possibile.  Visto il compromesso necessario tra memoria e concorrenza, la giusta quantità di memoria da allocare dipende dalla quantità di dati in ogni riga della tabella, dalle unità di data warehouse assegnate al sistema e dal numero di slot di concorrenza che è possibile assegnare alla sessione che sta scrivendo i dati nella tabella.

### <a name="high-volume-of-dml-operations"></a>Volume elevato di operazioni DML

Un volume elevato di operazioni DML pesanti per l'aggiornamento e l'eliminazione di righe può causare l'inefficienza del columnstore. Ciò vale soprattutto quando viene modificata la maggior parte delle righe di un gruppo di righe.

- L'eliminazione di una riga da un gruppo di righe compresso si limita a contrassegnare in modo logico la riga come eliminata. La riga rimane nel gruppo di righe compresso finché la partizione o la tabella non viene ricompilata.
- Quando si inserisce una riga, questa viene aggiunta a una tabella rowstore interna detta gruppo di righe differenziale. La riga inserita non viene convertita in columnstore fino a quando il gruppo di righe differenziale non viene riempito e contrassegnato come chiuso. I gruppi di righe vengono chiusi quando raggiungono la capacità massima di 1.048.576 righe.
- L'aggiornamento di una riga nel formato columnstore viene elaborato come un'eliminazione logica e poi come operazione di inserimento. La riga inserita può essere archiviata nell'archivio differenziale.

Le operazioni di aggiornamento e inserimento in batch che superano la soglia in blocco di 102.400 righe per distribuzione allineata a partizione passano direttamente al formato columnstore. Tuttavia, presupponendo una distribuzione uniforme, perché ciò si verifichi si dovranno modificare più di 6.144.000 righe in una singola operazione. Se il numero di righe per una determinata distribuzione allineata alle partizioni è inferiore a 102.400 le righe inviate all'archivio differenziale e lì rimangono fino a quando non viene inserite o modificate per chiudere il gruppo di righe sufficiente righe o l'indice è stato ricompilato.

### <a name="small-or-trickle-load-operations"></a>Operazioni di caricamento di piccole dimensioni o con un flusso irregolare 

I caricamenti di piccole dimensioni in SQL Data Warehouse sono anche definiti flussi irregolari. In genere rappresentano un flusso quasi costante di dati inseriti nel sistema. Quando questo flusso è quasi continuo, tuttavia, il volume di righe non è particolarmente elevato. Molto spesso i dati sono notevolmente inferiori alla soglia necessaria per un caricamento diretto nel formato columnstore.

In queste situazioni è spesso preferibile inserire prima i dati nell'archivio BLOB di Azure e lasciarli accumulare prima di caricarli. Questa tecnica viene spesso definita come *micro invio in batch*.

### <a name="too-many-partitions"></a>Troppe partizioni

Un altro fattore da considerare è l'impatto del partizionamento sulle tabelle columnstore cluster.  Prima di eseguire il partizionamento, SQL Data Warehouse divide già i dati in 60 database.  Il partizionamento, quindi, suddivide ulteriormente i dati.  Se si partizionano i dati, tenere presente che per poter sfruttare i vantaggi di un indice columnstore cluster **ogni** partizione deve contenere almeno 1 milione di righe.  Se si partizionare la tabella in 100 partizioni, quindi la tabella deve almeno 6 miliardi di righe per trarre vantaggio da un indice columnstore cluster (60 distribuzioni *100 partizioni* 1 milione di righe). Se la tabella da 100 partizioni non contiene 6 miliardi di righe, occorre ridurre il numero di partizioni o prendere in considerazione l'uso di una tabella heap.

Dopo aver caricato alcuni dati nelle tabelle, seguire questa procedura per identificare e ricompilare le tabelle con indici columnstore cluster non ottimali.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Ricompilazione degli indici per migliorare la qualità dei segmenti

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Passaggio 1: Identificare o creare l'utente per la classe di risorse appropriata

Un metodo rapido per migliorare immediatamente la qualità dei segmenti consiste nella ricompilazione dell'indice.  Il codice SQL restituito dalla vista precedente restituisce un'istruzione ALTER INDEX REBUILD che può essere usata per ricompilare gli indici. Durante la ricompilazione degli indici, assicurarsi di allocare memoria sufficiente per la sessione di ricompilazione dell'indice.  A tale scopo, incrementare la classe di risorse di un utente con autorizzazioni per la ricompilazione dell'indice in questa tabella al livello minimo consigliato.

Di seguito è riportato un esempio di come allocare altra memoria per un utente, aumentando la relativa classe di risorse. Per informazioni sull'uso delle classi di risorse, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Passaggio 2: Ricompilare gli indici columnstore cluster con un utente che usa una classe di risorse superiore

Accedere come l'utente indicato nel passaggio 1, ad esempio LoadUser, che ora Usa una classe di risorse superiore, ed eseguire le istruzioni ALTER INDEX. Assicurarsi che l'utente abbia l'autorizzazione ALTER per le tabelle in cui viene ricompilato l'indice. Questi esempi illustrano come ricompilare l'intero indice columnstore o una singola partizione. Nelle tabelle di grandi dimensioni, è consigliabile ricompilare gli indici procedendo una partizione alla volta.

In alternativa, invece di ricompilare l'indice è possibile copiare la tabella in una nuova tabella con [CTAS](sql-data-warehouse-develop-ctas.md). Qual è il modo migliore? Per grandi volumi di dati CTAS è in genere più veloce di [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Per volumi di dati più piccoli, ALTER INDEX è più facile da usare e non richiede la sostituzione della tabella.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

La ricompilazione di un indice in SQL Data Warehouse è un'operazione offline.  Per altre informazioni sulla ricompilazione di indici, vedere la sezione ALTER INDEX REBUILD in [Deframmentazione degli indici columnstore](/sql/relational-databases/indexes/columnstore-indexes-defragmentation) e [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Passaggio 3: Verificare che la qualità dei segmenti columnstore cluster sia migliorata

Eseguire nuovamente la query che ha identificato la tabella con una qualità scadente dei segmenti e verificare che la qualità sia migliorata.  Se la qualità dei segmenti non è migliorata, le righe della tabella potrebbero essere troppo larghe.  È consigliabile usare una classe di risorse superiore o una DWU durante la ricompilazione degli indici.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Ricompilazione degli indici con CTAS e cambio della partizione

In questo esempio viene usata l'istruzione [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) e il cambio di partizione per ricompilare una partizione di tabella.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Per informazioni dettagliate sulla ricreazione di partizioni tramite CTAS, vedere [Using partitions in SQL Data Warehouse](sql-data-warehouse-tables-partition.md) (Uso di partizioni in SQL Data Warehouse).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di tabelle, vedere [Developing tables](sql-data-warehouse-tables-overview.md) (Sviluppo di tabelle).
