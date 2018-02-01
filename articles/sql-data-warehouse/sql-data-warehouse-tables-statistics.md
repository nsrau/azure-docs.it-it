---
title: Gestione delle statistiche nelle tabelle in SQL Data Warehouse | Documentazione Microsoft
description: Introduzione alle statistiche nelle tabelle di SQL Data Warehouse di Azure.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 11/06/2017
ms.author: barbkess
ms.openlocfilehash: b007e1894f163d50dbf31e3c09b4b5ff329adb59
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gestione delle statistiche nelle tabelle in SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Panoramica][Overview]
> * [Tipi di dati][Data Types]
> * [Distribuzione][Distribute]
> * [Indice][Index]
> * [Partizione][Partition]
> * [Statistiche][Statistics]
> * [Temporanee][Temporary]
> 
> 

Più informazioni sui dati sono a disposizione di Azure SQL Data Warehouse, più rapidamente può eseguire query. La raccolta le statistiche sui dati e il relativo caricamento in SQL Data Warehouse sono fra le operazioni più importanti per ottimizzare le query. Questo è dovuto al fatto che Query Optimizer di SQL Data Warehouse si basa sul costo. Esegue un confronto fra i costi dei vari piani di query e poi sceglie quello che costa meno, che in molti casi è anche il piano eseguito più velocemente. Ad esempio, se Query Optimizer stima che il filtro per una particolare data nella query restituirà una riga, può scegliere un piano diverso rispetto a quando stima che la data selezionata restituirà 1 milione di righe.

Il processo di creazione e aggiornamento delle statistiche è attualmente manuale, ma si tratta di un processo semplice.  A breve l'utente sarà in grado di creare e aggiornare automaticamente le statistiche per i singoli indici e le singole colonne.  Sfruttando le informazioni seguenti, è possibile automatizzare notevolmente la gestione delle statistiche sui dati. 

## <a name="getting-started-with-statistics"></a>Introduzione alle statistiche
La creazione di statistiche campionate per ogni colonna è un modo semplice per iniziare. La presenza di statistiche non aggiornate comporta prestazioni di query non ottimali. Tuttavia l'aggiornamento delle statistiche per tutte le colonne può occupare molta memoria man mano che i dati aumentano. 

Di seguito sono riportati i consigli per i diversi scenari:
| **Scenario** | Raccomandazione |
|:--- |:--- |
| **Attività iniziali** | Aggiorna tutte le colonne dopo aver eseguito la migrazione a SQL Data Warehouse |
| **Colonna più importante per le statistiche** | Chiave di distribuzione hash |
| **Seconda colonna più importante per le statistiche** | Chiave di partizione |
| **Altre colonne importanti per le statistiche** | Data, JOIN, GROUP BY, HAVING e WHERE frequenti |
| **Frequenza degli aggiornamenti delle statistiche**  | Conservativa: giornaliera <br></br> Dopo il caricamento o la trasformazione dei dati |
| **Campionamento** |  Inferiore a 1 miliardo di righe, usare il campionamento predefinito (20%) <br></br> Con più di 1 miliardo di righe, sono considerate ottimali le statistiche in un intervallo del 2% |

## <a name="updating-statistics"></a>Aggiornamento delle statistiche

Una procedura consigliata consiste nell'aggiornare le statistiche sulle colonne data ogni giorno quando vengono aggiunte nuove date. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. In questo modo si modifica la distribuzione dei dati e le statistiche non si aggiornano. Al contrario le statistiche sulla colonna del paese nella tabella di un cliente potrebbero non richiedere mai un aggiornamento, poiché la distribuzione dei valori in genere non cambia. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati. Tuttavia se il data warehouse contiene solo un determinato paese e si importano dati da un paese diverso, archiviando in questo modo dati da più paesi, sarà sicuramente necessario aggiornare le statistiche sulla colonna del paese.

Quando si risolvono i problemi di una query è essenziale verificare prima di tutto che **le statistiche siano aggiornate**.

Questa verifica non può essere basata sulla data di creazione dei dati. Un oggetto statistiche aggiornato può essere vecchio se non sono state apportate modifiche sostanziali ai dati sottostanti. È necessario aggiornare le statistiche *quando* vengono apportate modifiche importanti al numero di righe o modifiche sostanziali alla distribuzione dei valori per una colonna specifica.

Poiché non esiste alcuna vista a gestione dinamica per determinare se i dati all'interno della tabella sono cambiati dall'ultimo aggiornamento delle statistiche, sapere a quando risalgono le statistiche può consentire di fare un quadro della situazione.  È possibile usare la query seguente per determinare l'ultimo aggiornamento delle statistiche di ogni tabella.  

> [!NOTE]
> Si tenga presente che se vi è una modifica sostanziale nella distribuzione dei valori per una colonna, è necessario aggiornare le statistiche a prescindere dalla data dell'ultimo aggiornamento.  
> 
> 

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Le **colonne data** in un data warehouse, ad esempio, richiedono solitamente aggiornamenti frequenti delle statistiche. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. In questo modo si modifica la distribuzione dei dati e le statistiche non si aggiornano.  Al contrario, è possibile che non sia mai necessario aggiornare le statistiche relative alla colonna del sesso in una tabella clienti. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati. Se tuttavia il data warehouse contiene solo un sesso e un nuovo requisito ha come risultato più sessi, allora sarà necessario aggiornare le statistiche relative alla colonna del sesso.

Per altre informazioni, vedere [Statistiche][Statistics] in MSDN.

## <a name="implementing-statistics-management"></a>Implementazione della gestione delle statistiche
È spesso consigliabile estendere il processo di caricamento dei dati per assicurare che le statistiche vengano aggiornate al termine del caricamento. Il caricamento dei dati è la fase in cui si verifica con maggiore frequenza una modifica delle dimensioni e/o della distribuzione dei valori delle tabelle. Questa è quindi una posizione logica per implementare alcuni processi di gestione.

Di seguito sono disponibili i principi guida per l'aggiornamento delle statistiche durante il processo di caricamento:

* Assicurarsi che ogni tabella caricata includa almeno un oggetto statistiche aggiornato. Ciò permette di aggiornare le informazioni sulle dimensioni delle tabelle (numero di righe e pagine) come parte dell'aggiornamento delle statistiche.
* Concentrarsi sulle colonne incluse nelle clausole JOIN, GROUP BY, ORDER BY e DISTINCT.
* Prendere in considerazione una maggiore frequenza per l'aggiornamento delle colonne "chiave crescente", ad esempio le date delle transazioni, poiché questi valori non verranno inclusi nell'istogramma delle statistiche.
* Prendere in considerazione una minore frequenza per l'aggiornamento delle colonne relative alla distribuzione statica.
* Occorre ricordare che ogni oggetto statistiche viene aggiornato in sequenza. La semplice implementazione di `UPDATE STATISTICS <TABLE_NAME>` non è sempre ottimale, in particolare per tabelle di grandi dimensioni con molti oggetti statistiche.

Per altre informazioni, vedere [Stima della cardinalità][Cardinality Estimation] in MSDN.

## <a name="examples-create-statistics"></a>Esempi: Creare le statistiche
Questi esempi illustrano come usare diverse opzioni per la creazione delle statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

### <a name="create-single-column-statistics-with-default-options"></a>Creare statistiche a colonna singola con opzioni predefinite
Per creare statistiche su una colonna, è sufficiente fornire un nome per l'oggetto statistiche e il nome della colonna.

Questa sintassi usa tutte le opzioni predefinite. Per impostazione predefinita, SQL Data Warehouse esegue il campionamento del **20%** della tabella quando crea le statistiche.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Ad esempio: 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga
La frequenza di campionamento del 20% è sufficiente per la maggior parte delle situazioni. È tuttavia possibile modificare la frequenza di campionamento.

Per eseguire il campionamento dell'intera tabella, usare la sintassi seguente:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Ad esempio: 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Creare statistiche a colonna singola specificando le dimensioni del campione
In alternativa, è possibile specificare le dimensioni del campione sotto forma di percentuale:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Creare statistiche a colonna singola solo su alcune righe
È anche possibile creare statistiche su una parte delle righe della tabella. Questa opzione è definita statistica filtrata.

Ad esempio, è possibile usare le statistiche filtrate quando si pianifica di eseguire una query in una partizione specifica di una tabella partizionata di grandi dimensioni. Se si creano statistiche solo sui valori della partizione, la precisione delle statistiche migliorerà e miglioreranno quindi le prestazioni delle query.

Questo esempio crea statistiche su un intervallo di valori. È possibile definire con facilità i valori in modo che corrispondano all'intervallo di valori in una partizione.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Per fare in modo che Query Optimizer prenda in considerazione l'uso delle statistiche filtrate quando sceglie il piano di query distribuite, è necessario che la query rientri nella definizione dell'oggetto statistiche. Usando l'esempio precedente, la clausola WHERE della query deve specificare i valori col1 compresi tra 2000101 e 20001231.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Creare statistiche a colonna singola con tutte le opzioni
È anche possibile combinare le varie opzioni. L'esempio seguente crea un oggetto statistiche filtrato con una dimensione del campione personalizzata:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Per i riferimenti completi, vedere [CREATE STATISTICS][CREATE STATISTICS] in MSDN.

### <a name="create-multi-column-statistics"></a>Creare statistiche a più colonne
Per creare un oggetto statistiche a più colonne, è sufficiente usare gli esempi precedenti, specificando però più colonne.

> [!NOTE]
> L'istogramma, che viene usato per stimare il numero di righe nei risultati della query, è disponibile solo per la prima colonna elencata nella definizione dell'oggetto statistiche.
> 
> 

In questo esempio l'istogramma è disponibile su *product\_category*. Le statistiche tra le colonne vengono calcolate su *product\_category* e *product\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Poiché esiste una correlazione tra *product\_category* e *product\_sub\_category*, un oggetto statistica a più colonne può essere utile se si accede contemporaneamente a queste colonne.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Creare statistiche su tutte le colonne in una tabella
Un modo per creare le statistiche consiste nell'emettere comandi CREATE STATISTICS dopo la creazione della tabella:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Usare una stored procedure per creare statistiche su tutte le colonne in un database
SQL Data Warehouse non prevede una stored procedure di sistema equivalente a sp_create_stats in SQL Server. Questa stored procedure crea un oggetto statistiche a colonna singola su ogni colonna del database che non include già statistiche.

L'esempio seguente consente di iniziare a progettare il database. È possibile adattarlo alle proprie esigenze:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Per creare statistiche su tutte le colonne della tabella con questa procedura, è sufficiente chiamarla.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Esempi: aggiornare le statistiche
Per aggiornare le statistiche, è possibile eseguire le operazioni seguenti:

- Aggiornare un oggetto statistiche. Specificare il nome dell'oggetto statistiche che si desidera aggiornare.
- Aggiornare tutti gli oggetti statistiche in una tabella. Specificare il nome della tabella invece di un oggetto statistiche specifico.

### <a name="update-one-specific-statistics-object"></a>Aggiornare un oggetto statistiche specifico
Usare la sintassi seguente per aggiornare un oggetto statistiche specifico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Ad esempio: 

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

L'aggiornamento di oggetti statistiche specifici permette di ridurre al minimo il tempo e le risorse necessari per gestire le statistiche. È necessario scegliere con attenzione gli oggetti statistiche migliori da aggiornare.

### <a name="update-all-statistics-on-a-table"></a>Aggiornare tutte le statistiche in una tabella
Illustra un semplice metodo di aggiornamento di tutti gli oggetti statistiche in una tabella:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Ad esempio: 

```sql
UPDATE STATISTICS dbo.table1;
```

Questa istruzione è facile da usare. Occorre ricordare che aggiorna *tutte* le statistiche nella tabella e che quindi potrebbe eseguire più lavoro del necessario. Se le prestazioni non sono un problema, questo è il modo più semplice e più completo per garantire l'aggiornamento delle statistiche.

> [!NOTE]
> Quando si aggiornano tutte le statistiche in una tabella, SQL Data Warehouse esegue un'analisi per campionare la tabella per ogni oggetto statistica. Se si tratta di una tabella di grandi dimensioni, che include molte colonne e molte statistiche, potrebbe risultare più efficiente aggiornare le singole statistiche in base alle necessità.
> 
> 

Per l'implementazione della procedura `UPDATE STATISTICS`, vedere [Tabelle temporanee][Temporary]. Il metodo di implementazione è leggermente diverso rispetto alla procedura `CREATE STATISTICS` precedente, ma il risultato finale è lo stesso.

Per la sintassi completa, vedere [UPDATE STATISTICS][Update Statistics] in MSDN.

## <a name="statistics-metadata"></a>Metadati delle statistiche
Esistono diverse visualizzazioni e funzioni di sistema che consentono di trovare informazioni sulle statistiche. Ad esempio, è possibile verificare se un oggetto statistiche non è aggiornato usando la funzione stats-date per vedere la data di creazione o dell'ultimo aggiornamento delle statistiche.

### <a name="catalog-views-for-statistics"></a>Viste del catalogo per le statistiche
Queste visualizzazioni di sistema forniscono informazioni sulle statistiche:

| Vista del catalogo | DESCRIZIONE |
|:--- |:--- |
| [sys.columns][sys.columns] |Una riga per ogni colonna. |
| [sys.objects][sys.objects] |Una riga per ogni oggetto del database. |
| [sys.schemas][sys.schemas] |Una riga per ogni schema del database. |
| [sys.stats][sys.stats] |Una riga per ogni oggetto statistiche. |
| [sys.stats_columns][sys.stats_columns] |Una riga per ogni colonna nell'oggetto statistiche. Si collega a sys.columns. |
| [sys.tables][sys.tables] |Una riga per ogni tabella (include le tabelle esterne). |
| [sys.table_types][sys.table_types] |Una riga per ogni tipo di dati. |

### <a name="system-functions-for-statistics"></a>Funzioni di sistema per le statistiche
Queste funzioni di sistema sono utili per usare le statistiche:

| Funzioni di sistema | DESCRIZIONE |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Data dell'ultimo aggiornamento dell'oggetto statistiche. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Riepilogo e informazioni dettagliate sulla distribuzione di valori riconosciuti dall'oggetto statistiche. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinare le colonne delle statistiche e le funzioni in un'unica visualizzazione
Questa visualizzazione riunisce le colonne relative alle statistiche e ai risultati della funzione STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Esempi di DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() mostra i dati inclusi in un oggetto statistiche. Questi dati sono costituiti da tre parti:

- Intestazione
- Vettore di densità
- Istogramma

Metadati di intestazione sulle statistiche. L'istogramma mostra la distribuzione dei valori nella prima colonna chiave dell'oggetto statistiche. Il vettore di densità misura la correlazione tra le colonne. SQL Data Warehouse calcola le stime di cardinalità con tutti i dati nell'oggetto statistiche.

### <a name="show-header-density-and-histogram"></a>Mostrare l'intestazione, la densità e l'istogramma
Questo semplice esempio mostra tutte e tre le parti di un oggetto statistiche:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Ad esempio: 

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Mostrare una o più parti di DBCC SHOW_STATISTICS()
Se si è interessati a visualizzare solo parti specifiche, usare la clausola `WITH` e specificare le parti da visualizzare:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Ad esempio: 

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Differenze di DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() viene implementato in modo più rigoroso in SQL Data Warehouse rispetto a SQL Server:

- Le funzionalità non documentate non sono supportate.
- Non è possibile usare Stats_stream.
- Non è possibile unire i risultati per sottoinsiemi specifici di dati statistici. Ad esempio (STAT_HEADER JOIN DENSITY_VECTOR).
- NO_INFOMSGS non può essere impostato per l'eliminazione del messaggio.
- Non è possibile usare le parentesi quadre per i nomi delle statistiche.
- Non è possibile usare i nomi di colonna per identificare gli oggetti statistiche.
- L'errore personalizzato 2767 non è supportato.

## <a name="next-steps"></a>Passaggi successivi
Per altri dettagli, vedere [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] in MSDN.

  Per altre informazioni, vedere gli articoli sulla [panoramica delle tabelle][Overview], i [tipi di dati delle tabelle][Data Types], la [distribuzione di una tabella][Distribute], l'[indicizzazione di una tabella][Index], il [partizionamento di una tabella][Partition] e le [tabelle temporanee][Temporary].
  
   Per altre informazioni sulle procedure consigliate, vedere [Procedure consigliate per SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
