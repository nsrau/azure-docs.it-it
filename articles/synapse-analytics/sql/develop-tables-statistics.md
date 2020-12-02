---
title: Creare e aggiornare le statistiche usando le risorse SQL di sinapsi di Azure
description: Suggerimenti ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 52e3ea3e07a81495f64f70f72686154a02a654af
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451801"
---
# <a name="statistics-in-synapse-sql"></a>Statistiche in Synapse SQL

In questo articolo vengono forniti consigli ed esempi per la creazione e l'aggiornamento delle statistiche di ottimizzazione delle query tramite le risorse SQL di sinapsi: pool SQL dedicato e pool SQL senza server.

## <a name="statistics-in-dedicated-sql-pool"></a>Statistiche nel pool SQL dedicato

### <a name="why-use-statistics"></a>Perché usare le statistiche?

Il pool SQL più dedicato conosce i dati, più rapidamente può eseguire query. Dopo il caricamento dei dati in un pool SQL dedicato, la raccolta delle statistiche sui dati è una delle operazioni più importanti che è possibile eseguire per l'ottimizzazione delle query.  

Il Query Optimizer del pool SQL dedicato è un ottimizzatore basato sui costi. Esegue un confronto fra i costi dei vari piani di query e poi sceglie quello che costa meno, che in molti casi è il piano eseguito più velocemente.

Se, ad esempio, l'ottimizzatore stima che la data in base alla quale si sta filtrando la query restituirà una riga, verrà scelto un piano. Se invece stima che la data selezionata restituirà un milione righe, verrà restituito un piano diverso.

### <a name="automatic-creation-of-statistics"></a>Creazione automatica di statistiche

Il motore del pool SQL dedicato analizzerà le query utente in entrata per le statistiche mancanti quando l'opzione database AUTO_CREATE_STATISTICS è impostata su `ON` .  Se non sono presenti, Query Optimizer creerà statistiche su singole colonne nel predicato della query o nella condizione di join, 

in modo da migliorare le stime di cardinalità del piano di query.

> [!IMPORTANT]
> Per impostazione predefinita, la creazione automatica di statistiche è attiva.

È possibile controllare se l'opzione AUTO_CREATE_STATISTICS è attiva nel data warehouse in uso con il comando seguente:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Nel caso in cui la proprietà AUTO_CREATE_STATISTICS non sia abilitata, è consigliabile attivarla con il comando seguente:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Queste istruzioni attiveranno la creazione automatica di statistiche:

- SELECT
- INSERT SELECT
- CTAS
- UPDATE
- DELETE
- EXPLAIN quando contengono un join o viene rilevata la presenza di un predicato

> [!NOTE]
> La creazione automatica di statistiche non viene generata in tabelle esterne o temporanee.

La creazione automatica di statistiche viene eseguita in modo sincrono. Se nelle colonne non sono presenti tutte le statistiche, quindi, è possibile che si verifichi un leggero peggioramento delle prestazioni delle query. Il tempo necessario per creare le statistiche relative a una colonna dipende dalle dimensioni della tabella.

Per evitare una riduzione delle prestazioni, verificare che le statistiche siano già state create eseguendo il carico di lavoro di benchmark prima della profilatura del sistema.

> [!NOTE]
> La creazione di statistiche viene registrata in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in un contesto utente diverso.

Le statistiche automatiche create sono nel formato: _WA_Sys_<id colonna a 8 cifre in hex>_<id tabella a 8 cifre in hex>. È possibile visualizzare le statistiche già create con il comando [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true):

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name è il nome della tabella contenente le statistiche da visualizzare, che non può essere una tabella esterna. La destinazione è il nome dell'indice di destinazione, delle statistiche o della colonna per cui visualizzare le informazioni statistiche.

### <a name="update-statistics"></a>Aggiornare le statistiche

Una procedura consigliata consiste nell'aggiornare le statistiche sulle colonne data ogni giorno quando vengono aggiunte nuove date. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. Queste aggiunte modificano la distribuzione dei dati e rendono le statistiche obsolete.

Le statistiche sulla colonna del paese o dell'area geografica nella tabella di un cliente potrebbero non richiedere mai un aggiornamento, poiché la distribuzione dei valori in genere non cambia. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati.

Tuttavia se il data warehouse contiene solo un paese e si importano i dati da un nuovo paese o una nuova area geografica, sarà necessario aggiornare le statistiche nella colonna del paese o dell'area geografica.

Di seguito sono forniti alcuni elementi consigliati per l'aggiornamento delle statistiche:

|||
|-|-|
| **Frequenza degli aggiornamenti delle statistiche**  | Conservativa: Giornaliera </br> Dopo il caricamento o la trasformazione dei dati |
| **Campionamento** |  Se inferiore a 1 miliardo di righe, usare il campionamento predefinito (20%). </br> Se superiore a 1 miliardo righe, usare il campionamento del 2%. |

### <a name="determine-last-statistics-update"></a>Determinare l'ultimo aggiornamento delle statistiche

Quando si risolvono i problemi di una query è essenziale verificare prima di tutto che **le statistiche siano aggiornate**.

Questa verifica non può essere basata sulla data di creazione dei dati. Un oggetto statistiche aggiornato può essere vecchio se non sono state apportate modifiche sostanziali ai dati sottostanti. È necessario aggiornare le statistiche *quando* vengono apportate modifiche importanti al numero di righe o modifiche sostanziali alla distribuzione dei valori per una colonna specifica.

Non esiste alcuna vista a gestione dinamica per determinare se i dati all'interno della tabella sono cambiati dall'ultimo aggiornamento delle statistiche. Sapere a quando risalgono le statistiche può quindi fornire un quadro della situazione. 

È possibile usare la query seguente per determinare l'ultimo aggiornamento delle statistiche di ogni tabella.

> [!NOTE]
> Se è stata apportata una modifica sostanziale nella distribuzione dei valori per una colonna, è necessario aggiornare le statistiche a prescindere dalla data dell'ultimo aggiornamento.

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

Le **colonne data** in un data warehouse, ad esempio, richiedono solitamente aggiornamenti frequenti delle statistiche. Ogni volta che vengono caricate nuove righe nel data warehouse, vengono aggiunte nuove date di caricamento o date di transazione. Queste aggiunte modificano la distribuzione dei dati e rendono le statistiche obsolete.

È possibile che in una tabella clienti non sia mai necessario aggiornare le statistiche relative alla colonna del sesso. Supponendo che la distribuzione sia costante tra i clienti, l'aggiunta di nuove righe alla variazione di tabella non modificherà la distribuzione dei dati.

Se tuttavia il data warehouse contiene solo un sesso e un nuovo requisito ha come risultato più sessi, allora sarà necessario aggiornare le statistiche relative alla colonna del sesso. 

Per altre informazioni, rivedere l'articolo [Statistiche](/sql/relational-databases/statistics/statistics).

### <a name="implement-statistics-management"></a>Implementare la gestione delle statistiche

È spesso consigliabile estendere il processo di caricamento dei dati per assicurare che le statistiche vengano aggiornate al termine del caricamento. Il caricamento dei dati è la fase in cui si verifica con maggiore frequenza una modifica delle dimensioni e/o della distribuzione dei valori delle tabelle. Il processo di caricamento costituisce quindi una posizione logica per implementare alcuni processi di gestione.

Di seguito sono disponibili i principi guida per l'aggiornamento delle statistiche durante il processo di caricamento:

- Assicurarsi che ogni tabella caricata includa almeno un oggetto statistiche aggiornato. Questo processo consente di aggiornare le informazioni sulle dimensioni delle tabelle (numero di righe e pagine) come parte dell'aggiornamento delle statistiche.
- Concentrarsi sulle colonne incluse nelle clausole JOIN, GROUP BY, ORDER BY e DISTINCT.
- Prendere in considerazione una maggiore frequenza per l'aggiornamento delle colonne "chiave crescente", ad esempio le date delle transazioni, poiché questi valori non verranno inclusi nell'istogramma delle statistiche.
- Prendere in considerazione una minore frequenza per l'aggiornamento delle colonne relative alla distribuzione statica.
- Occorre ricordare che ogni oggetto statistiche viene aggiornato in sequenza. La semplice implementazione di `UPDATE STATISTICS <TABLE_NAME>` non è sempre ottimale, in particolare per tabelle di grandi dimensioni con molti oggetti statistiche.

Per ulteriori informazioni, vedere [Stima della cardinalità](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Esempi: Creare le statistiche

Questi esempi illustrano come usare diverse opzioni per la creazione delle statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

#### <a name="create-single-column-statistics-with-default-options"></a>Creare statistiche a colonna singola con opzioni predefinite

Per creare statistiche su una colonna, fornire un nome per l'oggetto statistiche e il nome della colonna.
Questa sintassi usa tutte le opzioni predefinite. Per impostazione predefinita, il pool SQL dedicato campiona il **20%** della tabella durante la creazione delle statistiche.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Ad esempio:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga

La frequenza di campionamento del 20% è sufficiente per la maggior parte delle situazioni. È tuttavia possibile modificare la frequenza di campionamento. Per eseguire il campionamento dell'intera tabella, usare la sintassi seguente:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Ad esempio:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Creare statistiche a colonna singola specificando le dimensioni del campione

In alternativa, è possibile specificare le dimensioni del campione sotto forma di percentuale:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Creare statistiche a colonna singola solo su alcune righe

È anche possibile creare statistiche su una parte delle righe della tabella. Questa opzione prende il nome di statistica filtrata.

Ad esempio, è possibile usare le statistiche filtrate quando si pianifica di eseguire una query in una partizione specifica di una tabella partizionata di grandi dimensioni. Se si creano statistiche solo sui valori della partizione, la precisione delle statistiche migliorerà e, quindi, miglioreranno anche le prestazioni delle query.

Questo esempio crea statistiche su un intervallo di valori. È possibile definire con facilità i valori in modo che corrispondano all'intervallo di valori in una partizione.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Per fare in modo che Query Optimizer prenda in considerazione l'uso delle statistiche filtrate quando sceglie il piano di query distribuite, è necessario che la query rientri nella definizione dell'oggetto statistiche. Usando l'esempio precedente, la clausola WHERE della query deve specificare i valori col1 compresi tra 2000101 e 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Creare statistiche a colonna singola con tutte le opzioni

È anche possibile combinare le varie opzioni. L'esempio seguente crea un oggetto statistiche filtrato con una dimensione del campione personalizzata:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Per i riferimenti completi, vedere [CREAZIONE DELLE  STATISTICHE](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Creare statistiche a più colonne

Per creare un oggetto statistiche a più colonne, usare gli esempi precedenti, specificando però più colonne.

> [!NOTE]
> L'istogramma, che viene usato per stimare il numero di righe nei risultato della query, è disponibile solo per la prima colonna elencata nella definizione dell'oggetto statistiche.

In questo esempio l'istogramma è disponibile su *product\_category*. Le statistiche sulle colonne vengono calcolate su *product\_category* e *product\_sub_category*:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Poiché esiste una correlazione tra *product\_category* e *product\_sub\_category*, un oggetto statistiche a più colonne può essere utile se si accede contemporaneamente a queste colonne.

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

Il pool SQL non prevede una stored procedure di sistema equivalente a sp_create_stats in SQL Server. Questa stored procedure crea un oggetto statistiche a colonna singola su ogni colonna del database che non include già statistiche.

L'esempio seguente consente di iniziare a progettare il database. È possibile adattarlo alle proprie esigenze:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

Per creare statistiche su tutte le colonne della tabella usando le impostazioni predefinite, chiamare la stored procedure.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Per creare statistiche su tutte le colonne della tabella eseguendo un'analisi completa, chiamare questa stored procedure:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Per creare statistiche campionate su tutte le colonne della tabella, immettere 3 e la percentuale di campionamento. La procedura seguente usa una frequenza di campionamento pari al 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Esempi: Aggiornare le statistiche

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

L'aggiornamento di oggetti statistiche specifici permette di ridurre al minimo il tempo e le risorse necessari per gestire le statistiche. Questa azione richiede di scegliere con attenzione i migliori oggetti statistiche da aggiornare.

#### <a name="update-all-statistics-on-a-table"></a>Aggiornamento di tutte le statistiche di una tabella

Di seguito è illustrato un semplice metodo di aggiornamento di tutti gli oggetti statistiche in una tabella.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Ad esempio:

```sql
UPDATE STATISTICS dbo.table1;
```

L'istruzione UPDATE STATISTICS è facile da usare. Occorre ricordare che aggiorna *tutte* le statistiche nella tabella, generando più lavoro del necessario. 

Se le prestazioni non sono un problema, questo è il metodo più semplice e più completo per garantire l'aggiornamento delle statistiche.

> [!NOTE]
> Quando si aggiornano tutte le statistiche in una tabella, il pool SQL dedicato esegue un'analisi per campionare la tabella per ogni oggetto statistiche. Se si tratta di una tabella di grandi dimensioni, che include molte colonne e molte statistiche, potrebbe risultare più efficiente aggiornare le singole statistiche in base alle necessità.

Per l'implementazione di una procedura `UPDATE STATISTICS`, vedere [Tabelle temporanee](develop-tables-temporary.md). Il metodo di implementazione è leggermente diverso rispetto alla procedura `CREATE STATISTICS` precedente, ma il risultato è lo stesso.
Per la sintassi completa, vedere [Aggiornare le statistiche](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="statistics-metadata"></a>Metadati delle statistiche

Esistono diverse visualizzazioni e funzioni di sistema che consentono di trovare informazioni sulle statistiche. Ad esempio, è possibile verificare se un oggetto statistiche non è aggiornato usando la funzione STATS_DATE(), che consente di vedere la data di creazione o dell'ultimo aggiornamento delle statistiche.

#### <a name="catalog-views-for-statistics"></a>Viste del catalogo per le statistiche

Queste visualizzazioni di sistema forniscono informazioni sulle statistiche:

| Vista del catalogo | Descrizione |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni colonna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni oggetto del database. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni schema del database. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni oggetto statistiche. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni colonna nell'oggetto statistiche. Si collega a sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni tabella (include le tabelle esterne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Una riga per ogni tipo di dati. |

#### <a name="system-functions-for-statistics"></a>Funzioni di sistema per le statistiche

Queste funzioni di sistema sono utili per usare le statistiche:

| Funzioni di sistema | Descrizione |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Data dell'ultimo aggiornamento dell'oggetto statistiche. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Riepilogo e informazioni dettagliate sulla distribuzione di valori riconosciuti dall'oggetto statistiche. |

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>Esempi di DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mostra i dati inclusi in un oggetto statistiche. Questi dati sono costituiti da tre parti:

- Intestazione
- Vettore di densità
- Istogramma

L'intestazione contiene i metadati sulle statistiche. L'istogramma mostra la distribuzione dei valori nella prima colonna chiave dell'oggetto statistiche. 

Il vettore di densità misura la correlazione tra le colonne. Il pool SQL dedicato calcola le stime della cardinalità con tutti i dati nell'oggetto statistiche.

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
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Ad esempio:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Differenze di DBCC SHOW_STATISTICS()

`DBCC SHOW_STATISTICS()` viene implementato in modo più rigoroso nel pool SQL dedicato rispetto a SQL Server:

- Non sono supportate funzionalità non documentate.
- Non è possibile usare Stats_stream.
- Non è possibile unire i risultati di specifici sottoinsiemi di dati statistici, ad esempio: STAT_HEADER JOIN DENSITY_VECTOR.
- Non è possibile impostare NO_INFOMSGS per l'eliminazione di un messaggio.
- Non è possibile usare parentesi quadre per i nomi delle statistiche.
- Non è possibile usare i nomi di colonna per identificare gli oggetti statistiche.
- Non è supportato l'errore personalizzato 2767.


## <a name="statistics-in-serverless-sql-pool"></a>Statistiche nel pool SQL senza server

Le statistiche vengono create per una determinata colonna in un determinato set di dati (percorso di archiviazione).

> [!NOTE]
> Impossibile creare statistiche per le colonne LOB.

### <a name="why-use-statistics"></a>Perché usare le statistiche?

Il pool SQL senza server è in grado di riconoscere i dati, più velocemente è possibile eseguire query su di esso. Raccogliere le statistiche sui dati è una delle prime attività da compiere per ottimizzare le query. 

Il Query Optimizer del pool SQL senza server è un ottimizzatore basato sui costi. Esegue un confronto fra i costi dei vari piani di query e poi sceglie quello che costa meno, che in molti casi è il piano eseguito più velocemente. 

Se, ad esempio, l'ottimizzatore stima che la data in base alla quale si sta filtrando la query restituirà una riga, verrà scelto un piano. Se invece stima che la data selezionata restituirà un milione righe, verrà restituito un piano diverso.

### <a name="automatic-creation-of-statistics"></a>Creazione automatica di statistiche

Il pool SQL senza server analizza le query utente in ingresso per le statistiche mancanti. Se non sono presenti, Query Optimizer creerà statistiche su singole colonne nel predicato della query o nella condizione di join per migliorare le stime di cardinalità del piano di query.

L'istruzione SELECT attiverà la creazione automatica di statistiche.

> [!NOTE]
> La creazione automatica di statistiche è attivata per i file Parquet. Per i file CSV, invece, è necessario creare manualmente le statistiche fino a quando non sarà supportata la creazione automatica di statistiche sui file CSV.

La creazione automatica di statistiche viene generata in modo sincrono; se nelle colonne non sono presenti tutte le statistiche, quindi, è possibile che si verifichi un leggero peggioramento delle prestazioni delle query. Il tempo necessario per creare le statistiche relative a una colonna dipende dalle dimensioni dei file di destinazione.

### <a name="manual-creation-of-statistics"></a>Creazione manuale di statistiche

Il pool SQL senza server consente di creare manualmente le statistiche. Per i file CSV, è necessario creare manualmente le statistiche perché la creazione automatica di statistiche non è ancora attivata per questi file. 

Per istruzioni su come creare manualmente le statistiche, vedere gli esempi seguenti.

### <a name="update-statistics"></a>Aggiornare le statistiche

Qualsiasi modifica ai dati nei file, nonché eliminazione o aggiunta di file, comporta modifiche alla distribuzione dei dati e rende le statistiche obsolete. In questo caso, le statistiche devono essere aggiornate.

Il pool SQL senza server ricrea automaticamente le statistiche se i dati vengono modificati in modo significativo. contemporaneamente, salva lo stato corrente del set di dati: percorsi di file, dimensioni, data dell'ultima modifica.

Vengono create nuove statistiche anche quando le statistiche esistenti diventano obsolete. L'algoritmo analizza i dati e li confronta con lo stato corrente del set di dati. Se le dimensioni delle modifiche superano una determinata soglia, le statistiche precedenti vengono eliminate e vengono ricreate rispetto al nuovo set di dati.

Le statistiche manuali non vengono mai dichiarate obsolete.

> [!NOTE]
> La ricreazione automatica delle statistiche è attivata per i file Parquet. Per i file CSV, invece, è necessario eliminare le statistiche esistenti e ricrearle manualmente fino a quando non sarà supportata la creazione automatica di statistiche sui file CSV. Vedere gli esempi seguenti su come eliminare e creare statistiche.

Quando si risolvono i problemi di una query è essenziale verificare prima di tutto che **le statistiche siano aggiornate**.

È necessario aggiornare le statistiche *quando* vengono apportate modifiche importanti al numero di righe o modifiche sostanziali alla distribuzione dei valori per una colonna specifica.

> [!NOTE]
> Se è stata apportata una modifica sostanziale nella distribuzione dei valori per una colonna, è necessario aggiornare le statistiche a prescindere dalla data dell'ultimo aggiornamento.

### <a name="implement-statistics-management"></a>Implementare la gestione delle statistiche

È possibile estendere la pipeline di dati in modo da accertarsi che le statistiche vengano aggiornate ogni volta che i dati vengono modificati in modo significativo tramite l'aggiunta, l'eliminazione o la modifica di file.

Di seguito vengono illustrati i principi guida per l'aggiornamento delle statistiche:

- Assicurarsi che ogni set di dati includa almeno un oggetto statistiche aggiornato. In questo modo, le informazioni sulle dimensioni delle tabelle (numero di righe e pagine) vengono aggiornate contestualmente all'aggiornamento delle statistiche.
- Concentrarsi sulle colonne che partecipano alle clausole WHERE, JOIN, GROUP BY, ORDER BY e DISTINCT.
- Aggiornare con maggiore frequenza le colonne "chiave crescente", ad esempio le date delle transazioni, poiché questi valori non saranno inclusi nell'istogramma delle statistiche.
- Aggiornare invece con minore frequenza le colonne relative alla distribuzione statica.

Per ulteriori informazioni, vedere [Stima della cardinalità](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Esempi: Creare statistiche a livello di colonna nel percorso OPENROWSET

Nell'esempio seguente vengono illustrate le varie opzioni disponibili per la creazione delle statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

> [!NOTE]
> È possibile creare statistiche a singola colonna solo in questo momento.
>
> Per eseguire sp_create_openrowset_statistics e sp_drop_openrowset_statistics, è necessario disporre delle autorizzazioni seguenti: AMMINISTRAre le operazioni BULK o AMMINISTRAre le operazioni BULK del DATABASE.

Per la creazione di statistiche viene usata la stored procedure seguente:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argomenti: [ @stmt = ] N'statement_text' - Specifica un'istruzione Transact-SQL che restituirà i valori di colonna da usare per le statistiche. Con il comando TABLESAMPLE è possibile specificare eventuali campioni di dati da usare. Se TABLESAMPLE non viene specificato, verrà usato il comando FULLSCAN.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Il campionamento CSV non funziona in questo caso, poiché per i file CSV è supportato solo il parametro FULLSCAN.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga

Per creare statistiche su una colonna, fornire una query che restituisca la colonna per la quale sono necessarie le statistiche.

Per impostazione predefinita, se non si specifica diversamente, il pool SQL senza server usa il 100% dei dati forniti nel DataSet durante la creazione delle statistiche.

Ad esempio, per creare le statistiche relative a una colonna Year del set di dati in base al file population.csv usando le opzioni predefinite (FULLSCAN), è necessario seguire questa procedura:

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

EXEC sys.sp_create_openrowset_statistics N'SELECT year
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

È possibile specificare le dimensioni del campione sotto forma di percentuale:

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

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Esempi: Aggiornare le statistiche

Per aggiornare le statistiche, è necessario eliminare le statistiche precedenti e creare nuove statistiche. Per l'eliminazione di statistiche viene usata la stored procedure seguente:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Per eseguire sp_create_openrowset_statistics e sp_drop_openrowset_statistics, è necessario disporre delle autorizzazioni seguenti: AMMINISTRAre le operazioni BULK o AMMINISTRAre le operazioni BULK del DATABASE.

Argomenti: [ @stmt = ] N'statement_text' - Specifica la stessa istruzione Transact-SQL usata per la creazione delle statistiche.

Per aggiornare le statistiche sulla colonna Year nel set di dati in base al file population.csv, è necessario eliminare le statistiche esistenti e crearne nuove:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
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

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Esempi: Creare le statistiche per la colonna di una tabella esterna

Nell'esempio seguente vengono illustrate le varie opzioni disponibili per la creazione delle statistiche. Le opzioni usate per ogni colonna dipendono dalle caratteristiche dei dati e dal modo in cui la colonna verrà usata nelle query.

> [!NOTE]
> È possibile creare statistiche a singola colonna solo in questo momento.

Per creare statistiche su una colonna, fornire un nome per l'oggetto statistiche e il nome della colonna.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argomenti: external_table specifica una tabella esterna per la creazione delle statistiche.

FULLSCAN calcola le statistiche analizzando tutte le righe. FULLSCAN e SAMPLE 100 PERCENT generano gli stessi risultati. Non è possibile usare FULLSCAN con l'opzione SAMPLE.

SAMPLE numero PERCENT specifica la percentuale approssimativa o il numero di righe presenti nella tabella o nella vista indicizzata che Query Optimizer può usare durante la creazione delle statistiche. Questo numero può essere compreso tra 0 e 100.

Non è possibile usare SAMPLE se viene specificata l'opzione FULLSCAN.

> [!NOTE]
> Il campionamento CSV non funziona in questo caso, poiché per i file CSV è supportato solo il parametro FULLSCAN.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Creare statistiche a colonna singola esaminando ogni riga

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Creare statistiche a colonna singola specificando le dimensioni del campione

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Esempi: Aggiornare le statistiche

Per aggiornare le statistiche, è necessario eliminare le statistiche precedenti e creare nuove statistiche. Prima si eliminano le statistiche esistenti:

```sql
DROP STATISTICS census_external_table.sState
```

E poi si creano nuove statistiche:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>Metadati delle statistiche

Esistono diverse visualizzazioni e funzioni di sistema che consentono di trovare informazioni sulle statistiche. Ad esempio, è possibile verificare se un oggetto statistiche non è aggiornato usando la funzione STATS_DATE(), che consente di vedere la data di creazione o dell'ultimo aggiornamento delle statistiche.

> [!NOTE]
> I metadati delle statistiche sono disponibili solo per le colonne della tabella esterna. I metadati delle statistiche non sono disponibili per le colonne OPENROWSET.

#### <a name="catalog-views-for-statistics"></a>Viste del catalogo per le statistiche

Queste visualizzazioni di sistema forniscono informazioni sulle statistiche:

| Vista del catalogo                                                 | Descrizione                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Una riga per ogni colonna.                                     |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Una riga per ogni oggetto del database.                     |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Una riga per ogni schema del database.                     |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Una riga per ogni oggetto statistiche.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Una riga per ogni colonna nell'oggetto statistiche. Si collega a sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Una riga per ogni tabella (include le tabelle esterne).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Una riga per ogni tipo di dati.                                  |

#### <a name="system-functions-for-statistics"></a>Funzioni di sistema per le statistiche

Queste funzioni di sistema sono utili per usare le statistiche:

| Funzioni di sistema                                              | Descrizione                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Data dell'ultimo aggiornamento dell'oggetto statistiche. |

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>Passaggi successivi

Per migliorare ulteriormente le prestazioni delle query per il pool SQL dedicato, vedere [monitorare il carico di lavoro e le](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) procedure consigliate [per il pool SQL dedicato](best-practices-sql-pool.md#maintain-statistics).

Per migliorare ulteriormente le prestazioni delle query per il pool SQL senza server, vedere [procedure consigliate per il pool SQL senza server](best-practices-sql-on-demand.md)
