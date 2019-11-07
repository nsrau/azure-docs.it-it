---
title: Creazione e aggiornamento delle statistiche
description: Suggerimenti ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query nelle tabelle in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c995358fc0135a1f9b504b57b23ecb3f6b41d6da
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692403"
---
# <a name="table-statistics-in-azure-sql-data-warehouse"></a>Statistiche tabella in Azure SQL Data Warehouse

Suggerimenti ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query nelle tabelle in Azure SQL Data Warehouse.

## <a name="why-use-statistics"></a>Perché usare le statistiche

Più informazioni sui dati sono a disposizione di Azure SQL Data Warehouse, più rapidamente può eseguire query. Dopo il caricamento dei dati in SQL Data Warehouse, la raccolta delle statistiche sui dati è una delle operazioni più importanti che è possibile eseguire per ottimizzare le query. Query Optimizer di SQL Data Warehouse si basa sul costo. Confronta il costo dei diversi piani di query e quindi sceglie il piano con il costo più basso. Nella maggior parte dei casi, viene scelto il piano che verrà eseguito più velocemente. Se, ad esempio, Query Optimizer stima che la data in cui si sta filtrando la query restituirà una riga, verrà scelto un piano. Se stima che la data selezionata restituirà 1 milione righe, verrà restituito un piano diverso.

## <a name="automatic-creation-of-statistic"></a>Creazione automatica di statistiche

Quando l'opzione database AUTO_CREATE_STATISTICS è on, SQL Data Warehouse analizza le query utente in ingresso per le statistiche mancanti. Se le statistiche risultano mancanti, il Query Optimizer crea statistiche sulle singole colonne nel predicato di query o nella condizione di join per migliorare le stime della cardinalità per il piano di query. Per impostazione predefinita, la creazione automatica di statistiche è attiva.

È possibile verificare se la data warehouse è configurata con AUTO_CREATE_STATISTICS eseguendo il comando seguente:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se il data warehouse non dispone di AUTO_CREATE_STATISTICS configurato, è consigliabile abilitare questa proprietà eseguendo il comando seguente:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Queste istruzioni attiveranno la creazione automatica delle statistiche:

- SELECT
- INSERT SELECT
- CTAS
- AGGIORNAMENTO
- DELETE
- Viene illustrato quando viene rilevato un join o viene rilevata la presenza di un predicato

> [!NOTE]
> L'opzione di creazione automatica di statistiche non crea statistiche in tabelle temporanee o esterne.

La creazione automatica delle statistiche viene eseguita in modo sincrono, quindi è possibile che si verifichi un peggioramento delle prestazioni delle query se nelle colonne mancano statistiche. Il tempo necessario per creare le statistiche per una singola colonna dipende dalle dimensioni della tabella. Per evitare un calo delle prestazioni misurabile, soprattutto nel benchmark delle prestazioni, è necessario assicurarsi che le statistiche siano state create prima eseguendo il carico di lavoro benchmark prima di profilare il sistema.

> [!NOTE]
> La creazione di statistiche verrà registrata in [sys. dm _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) in un contesto utente diverso.

Le statistiche automatiche create sono nel formato: _WA_Sys_<id colonna a 8 cifre in hex>_<id tabella a 8 cifre in hex>. È possibile visualizzare le statistiche che sono già state create eseguendo il comando [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=azure-sqldw-latest) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name è il nome della tabella che contiene le statistiche da visualizzare. Non può essere una tabella esterna. La destinazione è il nome dell'indice, delle statistiche o della colonna di destinazione per cui visualizzare le informazioni statistiche.

## <a name="updating-statistics"></a>Aggiornamento delle statistiche

Una procedura consigliata consiste nell'aggiornare le statistiche sulle colonne data ogni giorno quando vengono aggiunte nuove date. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. In questo modo si modifica la distribuzione dei dati e le statistiche non si aggiornano. Viceversa, potrebbe non essere mai necessario aggiornare le statistiche relative a una colonna Country/Region in una tabella Customer, perché la distribuzione dei valori in genere non cambia. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati. Tuttavia, se il data warehouse contiene solo un paese e si importano i dati da un nuovo paese/area geografica, causando l'archiviazione di dati da più paesi o aree geografiche, è necessario aggiornare le statistiche nella colonna paese/area geografica.

Di seguito sono forniti alcuni elementi consigliati per l'aggiornamento delle statistiche:

|||
|-|-|
| **Frequenza degli aggiornamenti delle statistiche**  | Conservativa: giornaliera </br> Dopo il caricamento o la trasformazione dei dati |
| **Campionamento** |  Meno di 1 miliardo righe, usare il campionamento predefinito (20%). </br> Con più di 1 miliardo righe, usare il campionamento del due%. |

Quando si risolvono i problemi di una query è essenziale verificare prima di tutto che **le statistiche siano aggiornate**.

Questa verifica non può essere basata sulla data di creazione dei dati. Un oggetto statistiche aggiornato può essere vecchio se non sono state apportate modifiche sostanziali ai dati sottostanti. È necessario aggiornare le statistiche *quando* vengono apportate modifiche importanti al numero di righe o modifiche sostanziali alla distribuzione dei valori per una colonna specifica.

Non esiste alcuna vista a gestione dinamica per determinare se i dati all'interno della tabella sono cambiati dall'ultimo aggiornamento delle statistiche. Conoscere l'età delle statistiche può fornire una parte dell'immagine. È possibile usare la query seguente per determinare l'ultimo aggiornamento delle statistiche di ogni tabella.

> [!NOTE]
> Se si verifica una modifica sostanziale nella distribuzione dei valori per una colonna, è necessario aggiornare le statistiche indipendentemente dall'ultima volta in cui sono state aggiornate.

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

Le **colonne data** in un data warehouse, ad esempio, richiedono solitamente aggiornamenti frequenti delle statistiche. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. In questo modo si modifica la distribuzione dei dati e le statistiche non si aggiornano. Al contrario, è possibile che non sia mai necessario aggiornare le statistiche relative alla colonna del sesso in una tabella clienti. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati. Se tuttavia il data warehouse contiene solo un sesso e un nuovo requisito ha come risultato più sessi, allora sarà necessario aggiornare le statistiche relative alla colonna del sesso.

Per ulteriori informazioni, vedere indicazioni su [Statistiche](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Implementazione della gestione delle statistiche

È spesso consigliabile estendere il processo di caricamento dei dati per assicurare che le statistiche vengano aggiornate al termine del caricamento. Il caricamento dei dati è la fase in cui si verifica con maggiore frequenza una modifica delle dimensioni e/o della distribuzione dei valori delle tabelle. Questa è quindi una posizione logica per implementare alcuni processi di gestione.

Di seguito sono disponibili i principi guida per l'aggiornamento delle statistiche durante il processo di caricamento:

* Assicurarsi che ogni tabella caricata includa almeno un oggetto statistiche aggiornato. Ciò permette di aggiornare le informazioni sulle dimensioni delle tabelle (numero di righe e pagine) come parte dell'aggiornamento delle statistiche.
* Concentrarsi sulle colonne incluse nelle clausole JOIN, GROUP BY, ORDER BY e DISTINCT.
* Prendere in considerazione una maggiore frequenza per l'aggiornamento delle colonne "chiave crescente", ad esempio le date delle transazioni, poiché questi valori non verranno inclusi nell'istogramma delle statistiche.
* Prendere in considerazione una minore frequenza per l'aggiornamento delle colonne relative alla distribuzione statica.
* Occorre ricordare che ogni oggetto statistiche viene aggiornato in sequenza. La semplice implementazione di `UPDATE STATISTICS <TABLE_NAME>` non è sempre ottimale, in particolare per tabelle di grandi dimensioni con molti oggetti statistiche.

Per ulteriori informazioni, vedere [Stima della cardinalità](/sql/relational-databases/performance/cardinality-estimation-sql-server).

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

### <a name="create-single-column-statistics-with-all-the-options"></a>Creare statistiche a colonna singola con tutte le opzioni

È anche possibile combinare le varie opzioni. L'esempio seguente crea un oggetto statistiche filtrato con una dimensione del campione personalizzata:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Per i riferimenti completi, vedere [CREAZIONE DELLE  STATISTICHE](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Creare statistiche a più colonne

Per creare un oggetto statistiche a più colonne, è sufficiente usare gli esempi precedenti, specificando però più colonne.

> [!NOTE]
> L'istogramma, che viene usato per stimare il numero di righe nei risultato della query, è disponibile solo per la prima colonna elencata nella definizione dell'oggetto statistiche.

In questo esempio l'istogramma è disponibile su *product\_category*. Le statistiche tra le colonne vengono calcolate su *product\_category* e *product\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Poiché esiste una correlazione tra *product\_category* e *product\_sub\_category*, un oggetto statistiche a più colonne può essere utile se si accede contemporaneamente a queste colonne.

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

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

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
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
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

Per creare statistiche su tutte le colonne della tabella utilizzando le impostazioni predefinite, eseguire il stored procedure.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Per creare statistiche su tutte le colonne della tabella eseguendo un'analisi completa, chiamare questa stored procedure:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Per creare statistiche campionate su tutte le colonne della tabella, immettere 3 e la percentuale di campionamento. Questa stored procedure usa una frequenza di campionamento pari al 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

Per creare statistiche campionate su tutte le colonne

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

Un metodo semplice per l'aggiornamento di tutti gli oggetti statistiche in una tabella è il seguente:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Ad esempio:

```sql
UPDATE STATISTICS dbo.table1;
```

L'istruzione UPDATE STATISTICs è facile da utilizzare. Occorre ricordare che aggiorna *tutte* le statistiche nella tabella e che quindi potrebbe eseguire più lavoro del necessario. Se le prestazioni non sono un problema, questo è il modo più semplice e più completo per garantire che le statistiche siano aggiornate.

> [!NOTE]
> Quando si aggiornano tutte le statistiche in una tabella, SQL Data Warehouse esegue un'analisi per campionare la tabella per ogni oggetto statistiche. Se si tratta di una tabella di grandi dimensioni, che include molte colonne e molte statistiche, potrebbe risultare più efficiente aggiornare le singole statistiche in base alle necessità.

Per l'implementazione della procedura `UPDATE STATISTICS`, vedere [Tabelle temporanee](sql-data-warehouse-tables-temporary.md). Il metodo di implementazione è leggermente diverso rispetto alla procedura `CREATE STATISTICS` precedente, ma il risultato è lo stesso.

Per la sintassi completa, vedere [Aggiornamento delle statistiche](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Metadati delle statistiche

Esistono diverse visualizzazioni e funzioni di sistema che consentono di trovare informazioni sulle statistiche. Ad esempio, è possibile verificare se un oggetto statistiche non è aggiornato usando la funzione stats-date per vedere la data di creazione o dell'ultimo aggiornamento delle statistiche.

### <a name="catalog-views-for-statistics"></a>Viste del catalogo per le statistiche

Queste visualizzazioni di sistema forniscono informazioni sulle statistiche:

| Vista del catalogo | Descrizione |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Una riga per ogni colonna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Una riga per ogni oggetto del database. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Una riga per ogni schema del database. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Una riga per ogni oggetto statistiche. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Una riga per ogni colonna nell'oggetto statistiche. Si collega a sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Una riga per ogni tabella (include le tabelle esterne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Una riga per ogni tipo di dati. |

### <a name="system-functions-for-statistics"></a>Funzioni di sistema per le statistiche

Queste funzioni di sistema sono utili per usare le statistiche:

| Funzioni di sistema | Descrizione |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |Data dell'ultimo aggiornamento dell'oggetto statistiche. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Riepilogo e informazioni dettagliate sulla distribuzione di valori riconosciuti dall'oggetto statistiche. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinare le colonne delle statistiche e le funzioni in un'unica visualizzazione

Questa visualizzazione riunisce le colonne relative alle statistiche e ai risultati della funzione STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
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

## <a name="dbcc-show_statistics-examples"></a>Esempi di DBCC SHOW_STATISTICS()

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

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrare una o più parti di DBCC SHOW_STATISTICS()

Se si è interessati a visualizzare solo parti specifiche, usare la clausola `WITH` e specificare le parti da visualizzare:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Ad esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Differenze di DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() viene implementato in modo più rigoroso in SQL Data Warehouse rispetto a SQL Server:

- Le funzionalità non documentate non sono supportate.
- Non è possibile usare Stats_stream.
- Non è possibile unire i risultati per sottoinsiemi specifici di dati statistici. Ad esempio, STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS non può essere impostato per l'eliminazione del messaggio.
- Non è possibile usare le parentesi quadre per i nomi delle statistiche.
- Non è possibile usare i nomi di colonna per identificare gli oggetti statistiche.
- L'errore personalizzato 2767 non è supportato.

## <a name="next-steps"></a>Passaggi successivi

Per ottimizzare ulteriormente le prestazioni delle query, vedere [Monitorare il carico di lavoro](sql-data-warehouse-manage-monitor.md)
