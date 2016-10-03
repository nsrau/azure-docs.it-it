<properties
   pageTitle="Indicizzazione di tabelle in SQL Data Warehouse | Microsoft Azure"
   description="Introduzione all'indicizzazione delle tabelle in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Indicizzazione di tabelle in SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica][]
- [Tipi di dati][]
- [Distribuzione][]
- [Index][]
- [Partition][]
- [Statistiche][]
- [Temporanee][]

SQL Data Warehouse offre diverse opzioni di indicizzazione inclusi [indici columnstore cluster][], [indici cluster e indici non cluster][]. Offre anche un'opzione senza indice nota come [heap][]. Questo articolo illustra i vantaggi dei singoli tipi di indice e fornisce suggerimenti su come ottimizzare le prestazioni degli indici. Per altre informazioni su come creare una tabella in SQL Data Warehouse, vedere la [sintassi CREATE TABLE][].

## Indici columnstore cluster

Per impostazione predefinita, SQL Data Warehouse crea un indice columnstore cluster quando non vengono specificate opzioni di indice in una tabella. Le tabelle columnstore cluster offrono sia il livello massimo di compressione dei dati che le migliori prestazioni query generali. Le tabelle columnstore cluster garantiscono in genere prestazioni migliori rispetto alle tabelle heap o con indice cluster e rappresentano la scelta migliore in caso di tabelle di grandi dimensioni. Per questi motivi, l'indice columnstore cluster è il modo migliore per iniziare quando non si è certi di come indicizzare una tabella.

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

- Le tabelle columnstore non supportano gli indici non cluster secondari. È consigliabile usare tabelle heap o di indici cluster.
- Le tabelle columnstore non supportano varchar(max), nvarchar(max) e varbinary(max). È consigliabile usare tabelle heap o di indici cluster.
- Le tabelle columnstore potrebbero risultare meno efficiente per i dati temporanei. È consigliabile usare tabelle heap oppure tabelle temporanee.
- Tabelle di piccole dimensioni con meno di 100 milioni di righe. È consigliabile usare tabelle heap.

## Tabelle heap

Quando si inseriscono temporaneamente i dati in SQL Data Warehouse, una tabella heap potrebbe rendere più veloce il processo complessivo. Questo perché il caricamento negli heap è più veloce rispetto alle tabelle degli indici e in alcuni casi è possibile eseguire la lettura successiva dalla cache. Se si caricano i dati solo per inserirli temporaneamente prima di eseguire altre trasformazioni, il caricamento della tabella in una tabella heap sarà molto più rapido del caricamento dei dati in una tabella columnstore cluster. Anche il caricamento dei dati in una [tabella temporanea][Temporary] risulta molto più veloce del caricamento di una tabella in un archivio permanente.

Per le tabelle di ricerca di piccole dimensioni, inferiori a 100 milioni di righe, è spesso consigliabile scegliere le tabelle heap. Le tabelle columnstore cluster iniziano a raggiungere la compressione ottimale oltre i 100 milioni di righe.

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

## Indici cluster e non cluster

Gli indici cluster possono offrire prestazioni migliori rispetto alle tabelle columnstore cluster quando è necessario recuperare rapidamente una singola riga. Per le query in cui è necessario eseguire la ricerca in una sola riga o comunque in un numero di righe molto ridotto a velocità elevata, è consigliabile usare indici cluster o indici non cluster secondari. Gli indici cluster hanno lo svantaggio di risultare utili solo per le query con un filtro molto selettivo nella colonna dell'indice cluster. Per migliorare i filtri nelle altre colonne è possibile aggiungere un indice non cluster a tali colonne. Tuttavia, ogni indice che viene aggiunto a una tabella richiederà spazio e tempo di elaborazione aggiuntivi.

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

Per aggiungere un indice non cluster a una tabella, è sufficiente utilizzare la sintassi seguente:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Quando si usa CREATE INDEX, per impostazione predefinita viene creato un indice non cluster. Inoltre, un indice non cluster è consentito solo in una tabella di archiviazione riga (HEAP o CLUSTERED INDEX). Non è possibile usare indici non cluster su CLUSTERED COLUMNSTORE INDEX.


## Ottimizzazione degli indici columnstore cluster

I dati delle tabelle columnstore cluster sono organizzati in segmenti. Una qualità elevata dei segmenti è fondamentale per ottenere prestazioni ottimali delle query in una tabella columnstore. La qualità dei segmenti si può misurare in base al numero di righe in un gruppo di righe compresso. Per una qualità ottimale dei segmenti devono essere presenti almeno 100.000 righe per ogni gruppo di righe compresso. Le prestazioni migliorano quanto più il numero di righe si avvicina a 1.048.576, ovvero il numero massimo di righe che un gruppo di righe può contenere.

La vista seguente può essere creata e usata nel sistema per calcolare il numero medio di righe per ogni gruppo di righe e per identificare eventuali indici columnstore cluster non ottimali. L'ultima colonna di questa vista viene generata come istruzione SQL che può essere usata per la ricompilazione degli indici.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,	COUNT(DISTINCT rg.[partition_number])					AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,	CEILING	((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
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
SELECT	*
FROM	[dbo].[vColumnstoreDensity]
WHERE	COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Dopo avere eseguito la query, è possibile iniziare a esaminare i dati e analizzare i risultati. Questa tabella illustra gli elementi da cercare nell'analisi dei gruppi di righe.


| Colonna | Come usare questi dati |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Se la tabella è partizionata, è prevedibile che sia presente un numero maggiore di gruppi di righe aperti. Teoricamente, a ogni partizione nella distribuzione può essere associato un gruppo di righe aperto. Includere questo fattore nell'analisi. Una tabella di piccole dimensioni partizionata può essere ottimizzata rimuovendo completamente il partizionamento e migliorando così la compressione. |
| [row\_count\_total] | Numero totale di righe per la tabella. Ad esempio, è possibile usare questo valore per calcolare la percentuale di righe nello stato compresso. |
| [row\_count\_per\_distribution\_MAX] | Se tutte le righe sono distribuite uniformemente, questo valore sarà il numero di righe per ogni distribuzione. Confrontare questo valore con compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Numero totale di righe nel formato columnstore per la tabella. |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Se il numero medio di righe è notevolmente inferiore al numero massimo di righe per un gruppo di righe, è consigliabile usare le istruzioni CTAS o ALTER INDEX REBUILD per comprimere nuovamente i dati. |
| [COMPRESSED\_rowgroup\_count] | Numero di gruppi di righe nel formato columnstore. Se questo numero è molto elevato in relazione la tabella, è un indicatore che la densità del columnstore è bassa. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Le righe vengono eliminate in modo logico nel formato columnstore. Se il numero è elevato rispetto alle dimensioni della tabella, provare a ricreare la partizione o a ricompilare l'indice, perché in questo modo vengono eliminate fisicamente. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Usare questa colonna insieme alle colonne AVG e MAX per comprendere l'intervallo di valori per i gruppi di righe nel columnstore. Un numero basso oltre la soglia di caricamento, ad esempio 102.400 per ogni distribuzione di partizioni allineate, suggerisce che è possibile ottimizzare il caricamento dei dati. |
| [COMPRESSED\_rowgroup\_rows\_MAX] | Come sopra. |
| [OPEN\_rowgroup\_count] | I gruppi di righe aperti sono normali. È ragionevole prevedere un gruppo di righe aperto (OPEN) per ogni distribuzione di tabella (60). Un numero eccessivo suggerisce il caricamento di dati tra le partizioni. Verificare la strategia di partizionamento per assicurarsi che sia valida. |
| [OPEN\_rowgroup\_rows] | Ogni gruppo di righe può contenere al massimo 1.048.576 righe. Usare questo valore per controllare l'attuale livello di riempimento dei gruppi di righe aperti. |
| [OPEN\_rowgroup\_rows\_MIN] | I gruppi aperti indicano che i dati vengono caricati gradualmente nella tabella o che il carico precedente ha distribuito le righe rimanenti in questo gruppo di righe. Usare le colonne MIN, MAX e AVG per visualizzare la quantità di dati rimasta nei gruppi di righe OPEN. Per le tabelle di piccole dimensioni potrebbe essere il 100% di tutti i dati. In tal caso, usare ALTER INDEX REBUILD per forzare i dati nel columnstore. |
| [OPEN\_rowgroup\_rows\_MAX] | Come sopra. |
| [OPEN\_rowgroup\_rows\_AVG] | Come sopra. |
| [CLOSED\_rowgroup\_rows] | Esaminare le righe dei gruppi di righe chiusi come controllo di integrità. |
| [CLOSED\_rowgroup\_count] | Il numero di gruppi di righe chiusi deve essere basso o pari a zero. I gruppi di righe chiusi possono essere convertiti in gruppi di righe compressi usando l'istruzione ALTER INDEX... REORGANISE. In genere questa operazione non è tuttavia richiesta. I gruppi chiusi vengono convertiti automaticamente in gruppi di righe columnstore dal processo in background del motore di tuple. |
| [CLOSED\_rowgroup\_rows\_MIN] | I gruppi di righe chiusi devono avere una velocità di riempimento molto elevata. Se la velocità di riempimento per un gruppo di righe chiuso è bassa, è necessario analizzare ulteriormente il columnstore. |
| [CLOSED\_rowgroup\_rows\_MAX] | Come sopra. |
| [CLOSED\_rowgroup\_rows\_AVG] | Come sopra. |
| [Rebuild\_Index\_SQL] | Codice SQL per la ricompilazione dell'indice columnstore per una tabella |

## Possibili cause di una qualità scadente dell'indice columnstore

Se sono state identificate tabelle di qualità scadente è consigliabile identificarne anche la causa radice. Di seguito sono riportate altre cause comuni della qualità scadente dei segmenti:

1. Utilizzo elevato di memoria durante la compilazione dell'indice
2. Volume elevato di operazioni DML
3. Operazioni di caricamento di piccole dimensioni o con un flusso irregolare
4. Troppe partizioni

I fattori seguenti possono far sì che un indice columnstore abbia un numero di righe notevolmente inferiore alla cifra ottimale di 1 milione per ogni gruppo di righe. E possono anche far sì che le righe vengano inserite nel gruppo di righe differenziale anziché nel gruppo di righe compresso.

### Utilizzo elevato di memoria durante la compilazione dell'indice

Il numero di righe per ogni gruppo di righe compresso è direttamente correlato alla larghezza della riga e alla quantità di memoria disponibile per l'elaborazione del gruppo di righe. Quando le righe vengono scritte nelle tabelle columnstore in condizioni di utilizzo elevato di memoria, la qualità dei segmenti columnstore potrebbe risentirne. La procedura consigliata consiste quindi nel fare in modo che la sessione che sta scrivendo nelle tabelle di indice columnstore abbia accesso alla maggiore quantità di memoria possibile. Visto il compromesso necessario tra memoria e concorrenza, la giusta quantità di memoria da allocare dipende dalla quantità di dati in ogni riga della tabella, dalla quantità di DWU assegnate al sistema e dalla quantità di slot di concorrenza che è possibile assegnare alla sessione che sta scrivendo i dati nella tabella. È consigliabile iniziare con xlargerc se si usa un valore DW300 o inferiore, largerc se si usa un valore DW400 o DW600 o mediumrc se si usa un valore DW1000 o superiore.

### Volume elevato di operazioni DML

Un volume elevato di operazioni DML pesanti per l'aggiornamento e l'eliminazione di righe può causare l'inefficienza del columnstore. Ciò vale soprattutto quando viene modificata la maggior parte delle righe di un gruppo di righe.

- L'eliminazione di una riga da un gruppo di righe compresso si limita a contrassegnare in modo logico la riga come eliminata. La riga rimane nel gruppo di righe compresso finché la partizione o la tabella non viene ricompilata.
- Quando si inserisce una riga, questa viene aggiunta a una tabella rowstore interna detta gruppo di righe differenziale. La riga inserita non viene convertita in columnstore fino a quando il gruppo di righe differenziale non viene riempito e contrassegnato come chiuso. I gruppi di righe vengono chiusi quando raggiungono la capacità massima di 1.048.576 righe.
- L'aggiornamento di una riga nel formato columnstore viene elaborato come un'eliminazione logica e poi come operazione di inserimento. La riga inserita può essere archiviata nell'archivio differenziale.

Le operazioni di aggiornamento e inserimento in batch che superano la soglia in blocco di 102.400 righe per distribuzione allineata a partizione, vengono scritte direttamente nel formato columnstore. Tuttavia, presupponendo una distribuzione uniforme, perché ciò si verifichi si dovranno modificare più di 6.144.000 righe in una singola operazione. Se il numero di righe per una determinata distribuzione allineata alle partizioni è inferiore a 102.400, le righe vengono inviate all'archivio differenziale e lì rimangono fino a quando non viene inserito o modificato un numero di righe sufficiente per chiudere il gruppo di righe o fino a quando l'indice non viene ricompilato.

### Operazioni di caricamento di piccole dimensioni o con un flusso irregolare

I caricamenti di piccole dimensioni in SQL Data Warehouse sono anche definiti flussi irregolari. In genere rappresentano un flusso quasi costante di dati inseriti nel sistema. Quando questo flusso è quasi continuo, tuttavia, il volume di righe non è particolarmente elevato. Molto spesso i dati sono notevolmente inferiori alla soglia necessaria per un caricamento diretto nel formato columnstore.

In queste situazioni è spesso preferibile inserire prima i dati nell'archivio BLOB di Azure e lasciarli accumulare prima di caricarli. Questa tecnica viene spesso definita come *micro invio in batch*.

### Troppe partizioni

Un altro fattore da considerare è l'impatto del partizionamento sulle tabelle columnstore cluster. Prima di eseguire il partizionamento, SQL Data Warehouse divide già i dati in 60 database. Il partizionamento, quindi, suddivide ulteriormente i dati. Se si partizionano i dati, tenere presente che per poter sfruttare i vantaggi di un indice columnstore cluster **ogni** partizione deve contenere almeno 1 milione di righe. Se una tabella è divisa in 100 partizioni, per poter sfruttare i vantaggi di un indice columnstore cluster deve contenere almeno 6 miliardi di righe, ovvero 60 distribuzioni * 100 partizioni * 1 milione di righe. Se la tabella da 100 partizioni non contiene 6 miliardi di righe, occorre ridurre il numero di partizioni o prendere in considerazione l'uso di una tabella heap.

Dopo aver caricato alcuni dati nelle tabelle, seguire questa procedura per identificare e ricompilare le tabelle con indici columnstore cluster non ottimali.

## Ricompilazione degli indici per migliorare la qualità dei segmenti

### Passaggio 1: Identificare o creare l'utente per la classe di risorse appropriata

Un metodo rapido per migliorare immediatamente la qualità dei segmenti consiste nella ricompilazione dell'indice. Il codice SQL restituito dalla vista precedente restituisce un'istruzione ALTER INDEX REBUILD che può essere usata per ricompilare gli indici. Durante la ricompilazione degli indici, assicurarsi di allocare memoria sufficiente per la sessione di ricompilazione dell'indice. A tale scopo, incrementare la classe di risorse di un utente con autorizzazioni per la ricompilazione dell'indice in questa tabella al livello minimo consigliato. Non è possibile modificare la classe di risorse dell'utente proprietario del database. Se non è ancora stato creato un utente nel sistema, quindi, sarà necessario eseguire prima di tutto questa operazione. È consigliabile usare almeno xlargerc con un valore DW300 o inferiore, largerc con un valore DW400 o DW600 o mediumrc con un valore DW1000 o superiore.

Di seguito è riportato un esempio di come allocare altra memoria per un utente, aumentando la relativa classe di risorse. Per altre informazioni sulle classi di risorse e su come creare un nuovo utente, vedere [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse][Concurrency].

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### Passaggio 2: Ricompilare gli indici columnstore cluster con un utente che usa una classe di risorse superiore
Accedere con le credenziali dell'utente indicato nel passaggio 1, ad esempio LoadUser, che ora usa una classe di risorse superiore e quindi eseguire le istruzioni ALTER INDEX. Assicurarsi che l'utente abbia l'autorizzazione ALTER per le tabelle in cui viene ricompilato l'indice. Questi esempi illustrano come ricompilare l'intero indice columnstore o una singola partizione. Nelle tabelle di grandi dimensioni, è consigliabile ricompilare gli indici procedendo una partizione alla volta.

In alternativa, invece di ricompilare l'indice è possibile copiare la tabella in una nuova tabella con [CTAS][]. Qual è il modo migliore? Per grandi volumi di dati [CTAS][] è in genere più veloce di [ALTER INDEX][]. Per volumi di dati più piccoli, [ALTER INDEX][] è più facile da usare e non richiede la sostituzione della tabella. Per altre informazioni su come ricompilare gli indici con CTAS, vedere **Ricompilazione degli indici con CTAS e cambio della partizione** più avanti.

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

La ricompilazione di un indice in SQL Data Warehouse è un'operazione offline. Per altre informazioni sulla ricompilazione degli indici, vedere la sezione relativa ad ALTER INDEX REBUILD in [Columnstore Indexes Defragmentation][] \(Deframmentazione degli indici columnstore) e l'argomento relativo alla sintassi [ALTER INDEX][].
 
### Passaggio 3: Verificare che la qualità dei segmenti columnstore cluster sia migliorata
Eseguire nuovamente la query che ha identificato la tabella con una qualità scadente dei segmenti e verificare che la qualità sia migliorata. Se la qualità dei segmenti non è migliorata, le righe della tabella potrebbero essere troppo larghe. È consigliabile usare una classe di risorse superiore o una DWU durante la ricompilazione degli indici.

 
## Ricompilazione degli indici con CTAS e cambio della partizione

Questo esempio fa uso di [CTAS][] e del cambio della partizione per ricompilare una partizione di tabella.

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

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Per altre informazioni sulla ricompilazione di partizioni con `CTAS`, vedere l'articolo relativo alle [partizioni][].

## Passaggi successivi

Per altre informazioni, vedere gli articoli [Table Overview][Overview] \(Panoramica sulle tabelle), [Table Data Types][Data Types] \(Tipi di dati per le tabelle), [Distributing a Table][Distribute] \(Distribuzione di tabelle ), [Partitioning a table][Partition] \(Partizionamento di una tabella), [Maintaining table statistics][Statistics] \(Gestione delle statistiche nelle tabelle) e [Temporary tables][Temporary] \(Tabelle temporanee). Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per Azure SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Panoramica]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Tipi di dati]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuzione]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[partizioni]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporanee]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Procedure consigliate per Azure SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[indici cluster e indici non cluster]: https://msdn.microsoft.com/library/ms190457.aspx
[sintassi CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[indici columnstore cluster]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0810_2016-->