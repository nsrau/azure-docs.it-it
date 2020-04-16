---
title: Creazione, aggiornamento delle statistiche
description: Suggerimenti ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 798fec4dacb33a9f16de319062baf12adaffdbd0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428745"
---
# <a name="statistics-in-synapse-sql"></a>Statistiche in Sql SynapseStatistics in Synapse SQL

In questo articolo vengono forniti suggerimenti ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query usando le risorse SQL Synapse: pool SQL e SQL on-demand (anteprima).

## <a name="statistics-in-sql-pool"></a>Statistiche nel pool SQLStatistics in SQL pool

### <a name="why-use-statistics"></a>Perché utilizzare le statistiche

Maggiore è il numero di risorse del pool SQL che sono in grado di eseguire query nei dati. Dopo aver caricato i dati nel pool SQL, la raccolta di statistiche sui dati è una delle operazioni più importanti che è possibile eseguire per l'ottimizzazione delle query.  

Query Optimizer del pool SQL è un ottimizzatore basato sui costi. Confronta il costo dei vari piani di query e quindi sceglie il piano con il costo più basso. Nella maggior parte dei casi, sceglie il piano che verrà eseguito più velocemente.

Ad esempio, se l'ottimizzatore stima che la data in base alla quale viene filtrata la query verrà restituita una riga, verrà scelto un piano. Se stima che la data selezionata restituirà 1 milione di righe, restituirà un piano diverso.

### <a name="automatic-creation-of-statistics"></a>Creazione automatica di statistiche

Il pool SQL analizzerà le query degli utenti `ON`in ingresso per le statistiche mancanti quando l'opzione AUTO_CREATE_STATISTICS database è impostata su .  Se le statistiche sono mancanti, Query Optimizer crea statistiche su singole colonne nel predicato di query o nella condizione di join. Questa funzione viene utilizzata per migliorare le stime di cardinalità per il piano di query.

> [!IMPORTANT]
> Per impostazione predefinita, la creazione automatica di statistiche è attiva.

È possibile verificare se il data warehouse è AUTO_CREATE_STATISTICS configurato eseguendo il comando seguente:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se il data warehouse non dispone di AUTO_CREATE_STATISTICS abilitata, è consigliabile abilitare questa proprietà eseguendo il comando seguente:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Queste dichiarazioni attiveranno la creazione automatica di statistiche:

- SELECT
- INSERT SELECT
- CTAS
- UPDATE
- DELETE
- EXPLAIN quando viene rilevato un join o la presenza di un predicato

> [!NOTE]
> La creazione automatica di statistiche non viene generata su tabelle temporanee o esterne.

La creazione automatica delle statistiche viene eseguita in modo sincrono. Pertanto, è possibile che si verifichi un miglioramento leggermente delle prestazioni delle query se nelle colonne mancano statistiche. Il tempo necessario per creare statistiche per una singola colonna dipende dalle dimensioni della tabella.

Per evitare una riduzione delle prestazioni misurabile, è necessario assicurarsi che le statistiche siano state create prima eseguendo il carico di lavoro di benchmark prima di profilare il sistema.

> [!NOTE]
> La creazione delle statistiche viene registrata in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in un contesto utente diverso.

Quando vengono create le statistiche automatiche, assumeranno la forma: _WA_Sys_<ID colonna di 8 cifre in Hex>_<id di tabella a 8 cifre in> esadecimali. È possibile visualizzare le statistiche già create eseguendo il comando [DBCC SHOW_STATISTICS:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Il table_name è il nome della tabella che contiene le statistiche da visualizzare, che non può essere una tabella esterna. La destinazione è il nome dell'indice di destinazione, delle statistiche o della colonna per cui visualizzare le informazioni sulle statistiche.

### <a name="update-statistics"></a>Aggiornare le statistiche

Una procedura consigliata consiste nell'aggiornare le statistiche sulle colonne data ogni giorno quando vengono aggiunte nuove date. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. Queste aggiunte modificano la distribuzione dei dati e rendono le statistiche obsolete.

Le statistiche su una colonna del paese o dell'area geografica in una tabella cliente potrebbero non dover mai essere aggiornate perché la distribuzione dei valori in genere non cambia. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati.

Tuttavia, quando il data warehouse contiene solo un paese o un'area geografica e si includano dati da un nuovo paese o area geografica, è necessario aggiornare le statistiche nella colonna del paese o dell'area geografica.

Di seguito sono forniti alcuni elementi consigliati per l'aggiornamento delle statistiche:

|||
|-|-|
| **Frequenza degli aggiornamenti delle statistiche**  | Conservativa: giornaliera </br> Dopo il caricamento o la trasformazione dei dati |
| **campionamento** |  Meno di 1 miliardo di righe, utilizzare il campionamento predefinito (20%). </br> Con più di 1 miliardo di righe, utilizzare il campionamento del due%. |

### <a name="determine-last-statistics-update"></a>Determinare l'ultimo aggiornamento delle statistiche

Quando si risolvono i problemi di una query è essenziale verificare prima di tutto che **le statistiche siano aggiornate**.

Questa domanda non è uno che può essere risposto dall'età dei dati. Un oggetto statistiche aggiornato può essere vecchio se non sono state apportate modifiche sostanziali ai dati sottostanti. Quando il numero di righe è cambiato in modo sostanziale o si verifica una modifica sostanziale nella distribuzione dei valori per una *colonna,* è il momento di aggiornare le statistiche.

Non è disponibile una visualizzazione a gestione dinamica per determinare se i dati all'interno della tabella sono stati modificati dall'ultimo aggiornamento delle statistiche. Conoscere l'età delle statistiche può fornirti una parte dell'immagine. È possibile usare la query seguente per determinare l'ultimo aggiornamento delle statistiche di ogni tabella.

> [!NOTE]
> Se si verifica una modifica sostanziale nella distribuzione dei valori per una colonna, è necessario aggiornare le statistiche indipendentemente dall'ultima volta che sono state aggiornate.

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

**Le colonne data** in un data warehouse, ad esempio, richiedono in genere aggiornamenti di statistiche frequenti. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. Queste aggiunte modificano la distribuzione dei dati e rendono le statistiche obsolete.

Le statistiche su una colonna di genere in una tabella dei clienti potrebbero non dover mai essere aggiornate. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati.

Tuttavia, se il data warehouse contiene un solo sesso e un nuovo requisito genera più sessi, è necessario aggiornare le statistiche nella colonna gender. Per ulteriori informazioni, consultare l'articolo [Statistiche.](/sql/relational-databases/statistics/statistics)

### <a name="implementing-statistics-management"></a>Implementazione della gestione delle statistiche

È spesso consigliabile estendere il processo di caricamento dei dati per garantire che le statistiche vengano aggiornate alla fine del caricamento. Il caricamento dei dati si verifica quando le tabelle modificano più frequentemente le dimensioni, la distribuzione dei valori o entrambe. Di conseguenza, il processo di caricamento è una posizione logica per implementare alcuni processi di gestione.

Di seguito sono disponibili i principi guida per l'aggiornamento delle statistiche durante il processo di caricamento:

- Assicurarsi che ogni tabella caricata includa almeno un oggetto statistiche aggiornato. Questo processo aggiorna le informazioni sulle dimensioni della tabella (conteggio delle righe e numero di pagine) come parte dell'aggiornamento delle statistiche.
- Concentrarsi sulle colonne incluse nelle clausole JOIN, GROUP BY, ORDER BY e DISTINCT.
- Valutare la possibilità di aggiornare le colonne "chiave crescente", ad esempio le date delle transazioni, in modo più frequente perché questi valori non verranno inclusi nell'istogramma delle statistiche.
- Prendere in considerazione una minore frequenza per l'aggiornamento delle colonne relative alla distribuzione statica.
- Occorre ricordare che ogni oggetto statistiche viene aggiornato in sequenza. La semplice implementazione di `UPDATE STATISTICS <TABLE_NAME>` non è sempre ottimale, in particolare per tabelle di grandi dimensioni con molti oggetti statistiche.

Per ulteriori informazioni, vedere [Stima della cardinalità](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Esempi: Creare le statistiche

Questi esempi illustrano come usare diverse opzioni per la creazione delle statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

#### <a name="create-single-column-statistics-with-default-options"></a>Creare statistiche a colonna singola con opzioni predefinite

Per creare statistiche su una colonna, specificare un nome per l'oggetto statistiche e il nome della colonna.
Questa sintassi usa tutte le opzioni predefinite. Per impostazione predefinita, il pool SQL campiona il **20%** della tabella quando crea statistiche.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Ad esempio:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga

La frequenza di campionamento del 20% è sufficiente per la maggior parte delle situazioni. È tuttavia possibile modificare la frequenza di campionamento. Per eseguire il campionamento dell'intera tabella, usare la sintassi seguente:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Ad esempio:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Creare statistiche a colonna singola specificando le dimensioni del campione

Un'altra opzione disponibile consiste nello specificare la dimensione del campione come percentuale:Another option you have is to specify the sample size as a percent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Creare statistiche a colonna singola solo su alcune righe

È inoltre possibile creare statistiche su una parte delle righe della tabella, denominata statistica filtrata.

Ad esempio, è possibile usare le statistiche filtrate quando si pianifica di eseguire una query in una partizione specifica di una tabella partizionata di grandi dimensioni. Creando statistiche solo sui valori di partizione, l'accuratezza delle statistiche migliorerà. Si verificherà anche un miglioramento delle prestazioni delle query.

Questo esempio crea statistiche su un intervallo di valori. È possibile definire con facilità i valori in modo che corrispondano all'intervallo di valori in una partizione.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Per fare in modo che Query Optimizer prenda in considerazione l'uso delle statistiche filtrate quando sceglie il piano di query distribuite, è necessario che la query rientri nella definizione dell'oggetto statistiche. Usando l'esempio precedente, la clausola WHERE della query deve specificare i valori col1 compresi tra 2000101 e 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Creare statistiche a colonna singola con tutte le opzioni

È anche possibile combinare le varie opzioni. L'esempio seguente crea un oggetto statistiche filtrato con una dimensione del campione personalizzata:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Per i riferimenti completi, vedere [CREAZIONE DELLE  STATISTICHE](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="create-multi-column-statistics"></a>Creare statistiche a più colonne

Per creare un oggetto statistiche a più colonne, utilizzare gli esempi precedenti, ma specificare più colonne.

> [!NOTE]
> L'istogramma, che viene usato per stimare il numero di righe nei risultato della query, è disponibile solo per la prima colonna elencata nella definizione dell'oggetto statistiche.

In questo esempio l'istogramma è disponibile su *product\_category*. Le statistiche trasversali sono calcolate in base alla *categoria di prodotto\_* e al sub_category del *\_prodotto:*

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Poiché esiste una correlazione tra *la categoria di prodotto\_* e la *sottocategoria\_\_del prodotto,* un oggetto statistiche a più colonne può essere utile se si accede a queste colonne contemporaneamente.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Creare statistiche su tutte le colonne in una tabella

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Usare una stored procedure per creare statistiche su tutte le colonne in un database

SQL pool doesn't have a system stored procedure equivalent to sp_create_stats in SQL Server. Questa stored procedure crea un oggetto statistiche a colonna singola su ogni colonna del database che non include già statistiche.
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

Per creare statistiche su tutte le colonne della tabella utilizzando i valori predefiniti, eseguire la stored procedure.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Per creare statistiche su tutte le colonne della tabella eseguendo un'analisi completa, chiamare questa stored procedure:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Per creare statistiche campionate su tutte le colonne della tabella, immettere 3 e la percentuale di campionamento. La procedura seguente utilizza una frequenza di campionamento del 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Esempi: aggiornare le statistiche

Per aggiornare le statistiche, è possibile eseguire le operazioni seguenti:

- Aggiornare un oggetto statistiche. Specificare il nome dell'oggetto statistiche che si desidera aggiornare.
- Aggiornare tutti gli oggetti statistiche in una tabella. Specificare il nome della tabella invece di un oggetto statistiche specifico.

#### <a name="update-one-specific-statistics-object"></a>Aggiornare un oggetto statistiche specifico

Usare la sintassi seguente per aggiornare un oggetto statistiche specifico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Ad esempio:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

L'aggiornamento di oggetti statistiche specifici permette di ridurre al minimo il tempo e le risorse necessari per gestire le statistiche. Questa azione richiede una certa riflessione per selezionare gli oggetti statistiche migliori da aggiornare.

#### <a name="update-all-statistics-on-a-table"></a>Aggiornamento di tutte le statistiche di una tabella

Un metodo semplice per aggiornare tutti gli oggetti statistiche in una tabella è:A simple method for updating all the statistics objects on a table is:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Ad esempio:

```sql
UPDATE STATISTICS dbo.table1;
```

L'istruzione UPDATE STATISTICS è facile da usare. Basta ricordare che aggiorna *tutte* le statistiche sulla tabella, richiedendo più lavoro del necessario. Se le prestazioni non sono un problema, questo metodo è il modo più semplice e completo per garantire che le statistiche siano aggiornate.

> [!NOTE]
> Quando si aggiornano tutte le statistiche in una tabella, il pool SQL esegue un'analisi per campionare la tabella per ogni oggetto statistiche. Se si tratta di una tabella di grandi dimensioni, che include molte colonne e molte statistiche, potrebbe risultare più efficiente aggiornare le singole statistiche in base alle necessità.

Per un'implementazione `UPDATE STATISTICS` di una routine, vedere [Tabelle temporanee](develop-tables-temporary.md). Il metodo di implementazione è leggermente diverso rispetto alla procedura `CREATE STATISTICS` precedente, ma il risultato è lo stesso.
Per la sintassi completa, consultate [Aggiornare le statistiche.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="statistics-metadata"></a>Metadati delle statistiche

Esistono diverse visualizzazioni e funzioni di sistema che consentono di trovare informazioni sulle statistiche. Ad esempio, è possibile verificare se un oggetto statistiche potrebbe non essere aggiornato utilizzando la funzione STATS_DATE(). STATS_DATE() consente di vedere quando le statistiche sono state create o aggiornate per l'ultima volta.

#### <a name="catalog-views-for-statistics"></a>Viste del catalogo per le statistiche

Queste visualizzazioni di sistema forniscono informazioni sulle statistiche:

| Vista del catalogo | Descrizione |
|:--- |:--- |
| [sys.columns (colonne)](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Una riga per ogni colonna. |
| [Sys](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Una riga per ogni oggetto del database. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Una riga per ogni schema del database. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Una riga per ogni oggetto statistiche. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Una riga per ogni colonna nell'oggetto statistiche. Si collega a sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Una riga per ogni tabella (include le tabelle esterne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Una riga per ogni tipo di dati. |

#### <a name="system-functions-for-statistics"></a>Funzioni di sistema per le statistiche

Queste funzioni di sistema sono utili per usare le statistiche:

| Funzioni di sistema | Descrizione |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Data dell'ultimo aggiornamento dell'oggetto statistiche. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Riepilogo e informazioni dettagliate sulla distribuzione di valori riconosciuti dall'oggetto statistiche. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinare le colonne delle statistiche e le funzioni in un'unica visualizzazione

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

### <a name="dbcc-show_statistics-examples"></a>Esempi di DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mostra i dati inclusi in un oggetto statistiche. Questi dati sono costituiti da tre parti:

- Intestazione
- Vettore di densità
- Istogramma

L'intestazione è i metadati relativi alle statistiche. L'istogramma mostra la distribuzione dei valori nella prima colonna chiave dell'oggetto statistiche. Il vettore di densità misura la correlazione tra le colonne. Il pool SQL calcola le stime di cardinalità con qualsiasi dato nell'oggetto statistiche.

#### <a name="show-header-density-and-histogram"></a>Mostrare l'intestazione, la densità e l'istogramma

Questo semplice esempio mostra tutte e tre le parti di un oggetto statistiche:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Ad esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrare una o più parti di DBCC SHOW_STATISTICS()

Se si è interessati a visualizzare solo parti specifiche, usare la clausola `WITH` e specificare le parti da visualizzare:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Ad esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Differenze di DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() è implementato in modo più rigoroso nel pool SQL rispetto a SQL Server:

- Le funzionalità non documentate non sono supportate.
- Non posso usare Stats_stream.
- Impossibile unire i risultati per sottoinsiemi specifici di dati statistici. Ad esempio, STAT_HEADER DENSITY_VECTOR JOIN.
- NO_INFOMSGS non può essere impostato per l'eliminazione dei messaggi.
- Non è possibile utilizzare le parentesi quadre intorno ai nomi delle statistiche.
- Impossibile utilizzare i nomi di colonna per identificare gli oggetti statistiche.
- L'errore personalizzato 2767 non è supportato.

### <a name="next-steps"></a>Passaggi successivi

Per ottimizzare ulteriormente le prestazioni delle query, vedere [Monitorare il carico di lavoro](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>Statistiche in SQL su richiesta (anteprima)Statistics in SQL on-demand (preview)

Le statistiche vengono create per una determinata colonna per un set di dati specifico (percorso di archiviazione).

### <a name="why-use-statistics"></a>Perché utilizzare le statistiche

Maggiore è il numero di informazioni su richiesta SQL (anteprima) dei dati, più veloce sarà possibile eseguire query su di esso. La raccolta di statistiche sui dati è una delle operazioni più importanti che è possibile eseguire per ottimizzare le query. SQL on-demand query optimizer è un ottimizzatore basato sui costi. Confronta il costo dei vari piani di query e quindi sceglie il piano con il costo più basso. Nella maggior parte dei casi, sceglie il piano che verrà eseguito più velocemente. Ad esempio, se l'ottimizzatore stima che la data in base alla quale viene filtrata la query verrà restituita una riga, verrà scelto un piano. Se stima che la data selezionata restituirà 1 milione di righe, restituirà un piano diverso.

### <a name="automatic-creation-of-statistics"></a>Creazione automatica di statistiche

SQL su richiesta analizza le query utente in ingresso per le statistiche mancanti. Se le statistiche sono mancanti, Query Optimizer crea statistiche su singole colonne nel predicato di query o nella condizione di join per migliorare le stime di cardinalità per il piano di query.

L'istruzione SELECT attiverà la creazione automatica delle statistiche.

> [!NOTE]
> La creazione automatica delle statistiche è attivata per i file Parquet. Per i file CSV, è necessario creare statistiche manualmente fino a quando non è supportata la creazione automatica di statistiche di file CSV.

La creazione automatica delle statistiche viene eseguita in modo sincrono, pertanto è possibile che si verifichi un peggioramento delle prestazioni delle query leggermente degradate se nelle colonne mancano statistiche. Il tempo necessario per creare statistiche per una singola colonna dipende dalle dimensioni dei file di destinazione.

### <a name="manual-creation-of-statistics"></a>Creazione manuale di statistiche

SQL su richiesta consente di creare statistiche manualmente. Per i file CSV, è necessario creare le statistiche manualmente perché la creazione automatica di statistiche non è attivata per i file CSV. Vedere gli esempi seguenti per istruzioni su come creare manualmente le statistiche.

### <a name="updating-statistics"></a>Aggiornamento delle statistiche

Le modifiche apportate ai dati nei file, l'eliminazione e l'aggiunta di file comportano modifiche alla distribuzione dei dati e rendono le statistiche non aggiornate. In tal caso, le statistiche devono essere aggiornate.

SQL su richiesta ricrea automaticamente le statistiche se i dati vengono modificati in modo significativo. Ogni volta che le statistiche vengono create automaticamente, viene salvato anche lo stato corrente del set di dati: percorsi di file, dimensioni, date dell'ultima modifica.

Quando le statistiche sono obsolete, ne verranno create di nuove. L'algoritmo passa attraverso i dati e li confronta con lo stato corrente del set di dati. Se le dimensioni delle modifiche sono superiori alla soglia specifica, le statistiche precedenti vengono eliminate e verranno ricreate nel nuovo set di dati.

Le statistiche manuali non vengono mai dichiarate non aggiornate.

> [!NOTE]
> La ricreazione automatica delle statistiche è attivata per i file Parquet. Per i file CSV, è necessario eliminare e creare statistiche manualmente fino a quando non è supportata la creazione automatica di statistiche di file CSV. Controlla gli esempi qui sotto su come eliminare e creare statistiche.

Quando si risolvono i problemi di una query è essenziale verificare prima di tutto che **le statistiche siano aggiornate**.

Quando il numero di righe è cambiato in modo sostanziale o c'è una modifica sostanziale nella distribuzione dei valori per una colonna, *allora* è il momento di aggiornare le statistiche.

> [!NOTE]
> Se si verifica una modifica sostanziale nella distribuzione dei valori per una colonna, è necessario aggiornare le statistiche indipendentemente dall'ultima volta che sono state aggiornate.

### <a name="implementing-statistics-management"></a>Implementazione della gestione delle statistiche

È possibile estendere la pipeline di dati per garantire che le statistiche vengano aggiornate quando i dati vengono modificati in modo significativo tramite l'aggiunta, l'eliminazione o la modifica di file.

Per l'aggiornamento delle statistiche sono previsti i seguenti principi guida:

- Assicurarsi che nel set di dati sia aggiornato almeno un oggetto statistiche. Questo aggiorna le informazioni sulle dimensioni (conteggio delle righe e conteggio delle pagine) come parte dell'aggiornamento delle statistiche.
- Concentrarsi sulle colonne incluse nelle clausole JOIN, GROUP BY, ORDER BY e DISTINCT.
- Aggiornare le colonne "chiave crescente", ad esempio le date delle transazioni più frequentemente perché questi valori non verranno inclusi nell'istogramma delle statistiche.
- Aggiornare le colonne di distribuzione statiche con minore frequenza.

Per ulteriori informazioni, vedere [Stima della cardinalità](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Esempi: creare statistiche per la colonna nel percorso OPENROWSETExamples: Create statistics for column in OPENROWSET path

Negli esempi seguenti viene illustrato come utilizzare varie opzioni per la creazione di statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

> [!NOTE]
> È possibile creare statistiche a colonna singola solo in questo momento.

La stored procedure seguente viene utilizzata per creare statistiche:The following stored procedure is used to create statistics:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Argomenti: @stmt [ ] N'statement_text' - Specifica un'istruzione Transact-SQLTransact-SQL che restituirà valori di colonna da utilizzare per le statistiche. È possibile utilizzare TABLESAMPLE per specificare campioni di dati da utilizzare. Se TABLESAMPLE non è specificato, verrà utilizzato FULLSCAN.

```sql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Il campionamento CSV non funziona in questo momento, solo FULLSCAN è supportato per CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga

Per creare statistiche su una colonna, fornire una query che restituisce la colonna per la quale sono necessarie statistiche.

Per impostazione predefinita, se non si specifica il contrario, SQL on-demand utilizza il 100% dei dati forniti nel set di dati quando crea statistiche.

Ad esempio, per creare statistiche con opzioni predefinite (FULLSCAN) per una colonna di anni del set di dati in base al file population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Creare statistiche a colonna singola specificando le dimensioni del campione

È possibile specificare la dimensione del campione come percentuale:You can specify the sample size as a percent:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Esempi: aggiornare le statistiche

Per aggiornare le statistiche, è necessario eliminare e creare le statistiche. La stored procedure seguente viene utilizzata per eliminare le statistiche:The following stored procedure is used to drop statistics:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

Argomenti: @stmt [ ] N'statement_text' - Specifica la stessa istruzione Transact-SQL utilizzata al momento della creazione delle statistiche.

Per aggiornare le statistiche per la colonna year nel set di dati, basata sul file population.csv, è necessario eliminare e creare statistiche:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Esempi: creare statistiche per la colonna della tabella esternaExamples: Create statistics for external table column

Negli esempi seguenti viene illustrato come utilizzare varie opzioni per la creazione di statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

> [!NOTE]
> È possibile creare statistiche a colonna singola solo in questo momento.

Per creare statistiche su una colonna, specificare un nome per l'oggetto statistiche e il nome della colonna.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argomenti: external_table Specifica la tabella esterna in cui devono essere create le statistiche.

FULLSCAN Calcola le statistiche analizzando tutte le righe. FULLSCAN e SAMPLE 100 PERCENT generano gli stessi risultati. Non è possibile utilizzare FULLSCAN con l'opzione SAMPLE.

Numero SAMPLE PERCENT Specifica la percentuale approssimativa o il numero di righe nella tabella o nella vista indicizzata per Query Optimizer da utilizzare quando crea statistiche. Il numero può essere compreso tra 0 e 100.

Questa opzione non può essere utilizzata quando viene specificata l'opzione FULLSCAN.

> [!NOTE]
> Il campionamento CSV non funziona in questo momento, solo FULLSCAN è supportato per CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Creare statistiche a colonna singola specificando le dimensioni del campione

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState on census_external_table (STATENAME) WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Esempi: aggiornare le statistiche

Per aggiornare le statistiche, è necessario eliminare e creare le statistiche. Prima le statistiche di rilascio:

```sql
DROP STATISTICS census_external_table.sState
```

E creare statistiche:

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori miglioramenti delle prestazioni delle query, vedere Procedure consigliate per il [pool SQL.](best-practices-sql-pool.md#maintain-statistics)
