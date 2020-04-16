---
title: Tabelle temporanee
description: Indicazioni essenziali per l'utilizzo di tabelle temporanee nel pool SQL Synapse, evidenziando i principi delle tabelle temporanee a livello di sessione.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408034"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Tabelle temporanee nel pool SQL Synapse
Questo articolo contiene le linee guida fondamentali per l'uso delle tabelle temporanee ed evidenzia i principi delle tabelle temporanee a livello di sessione. 

L'uso delle informazioni contenute in questo articolo consente di modularizzare il codice, migliorando sia la riusabilità che la facilità di manutenzione.

## <a name="what-are-temporary-tables"></a>Introduzione delle tabelle temporanee
Le tabelle temporanee sono utili durante l'elaborazione dei dati, in particolare durante la trasformazione in cui i risultati intermedi sono temporanei. Nel pool SQL esistono tabelle temporanee a livello di sessione.  

Le tabelle temporanee sono visibili solo alla sessione in cui sono state create e vengono eliminate automaticamente quando la sessione si disconnette.  

Le tabelle temporanee offrono un miglioramento delle prestazioni, perché i loro risultati vengono scritti in locale anziché nell'archiviazione remota.

Le tabelle temporanee sono utili durante l'elaborazione dei dati, in particolare durante la trasformazione in cui i risultati intermedi sono temporanei. Con SQL Analytics, le tabelle temporanee esistono a livello di sessione.  Sono visibili solo alla sessione in cui sono stati creati. Di conseguenza, vengono eliminati automaticamente quando la sessione si disconnette. 

## <a name="temporary-tables-in-sql-pool"></a>Tabelle temporanee nel pool SQL

Nella risorsa del pool SQL, le tabelle temporanee offrono un vantaggio in termini di prestazioni perché i risultati vengono scritti nell'archiviazione locale anziché remota.

### <a name="create-a-temporary-table"></a>Creazione di una tabella temporanea

Le tabelle temporanee vengono create aggiungendo un prefisso al nome di una tabella con `#`.  Ad esempio:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Le tabelle temporanee possono essere create anche con `CTAS` adottando esattamente lo stesso approccio:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` è un comando efficace e offre l'ulteriore vantaggio di essere efficiente nell'uso dello spazio dei log delle transazioni. 
> 
> 

## <a name="dropping-temporary-tables"></a>Eliminazione delle tabelle temporanee
Quando viene creata una nuova sessione, non deve esistere alcuna tabella temporanea.  

Se si chiama la stessa stored procedure, che crea un temporaneo `CREATE TABLE` con lo stesso nome, per garantire `DROP` che le istruzioni abbiano esito positivo, è possibile utilizzare un semplice controllo di preesistenza con a come nell'esempio seguente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Per la coerenza di codifica, è consigliabile usare questo modello sia per le tabelle che per le tabelle temporanee.  È anche una buona idea `DROP TABLE` da usare per rimuovere le tabelle temporanee quando hai finito con loro nel codice.  

Nello sviluppo di stored procedure, è comune vedere i comandi di rilascio in bundle insieme alla fine di una procedura per garantire che questi oggetti vengono puliti.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizzazione del codice
Poiché le tabelle temporanee possono essere visualizzate in qualsiasi punto di una sessione utente, questa funzionalità può essere sfruttata per semplificare il codice dell'applicazione.  

Ad esempio, la stored procedure seguente genera DDL per aggiornare tutte le statistiche nel database in base al nome della statistica:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

In questa fase, l'unica azione che si è verificata è la creazione di una stored procedure che genera una tabella temporanea, #stats_ddl, con istruzioni DDL.  

Questa stored procedure elimina un #stats_ddl esistente per garantire che non abbia esito negativo se viene eseguita più di una volta all'interno di una sessione.  

Tuttavia, poiché non esiste `DROP TABLE` alla fine della stored procedure, al termine della stored procedure, la tabella creata viene conservata in modo che possa essere letta all'esterno della stored procedure.  

Nel pool SQL, a differenza di altri database di SQL Server, è possibile utilizzare la tabella temporanea all'esterno della procedura che l'ha creata.  Le tabelle temporanee del pool SQL possono essere utilizzate **in qualsiasi punto** della sessione. Questa funzionalità può portare a codice più modulare e gestibile, come nell'esempio seguente:This feature can lead to more modular and manageable code as in the following example:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitazioni della tabella temporanea
Il pool SQL impone un paio di limitazioni durante l'implementazione di tabelle temporanee.  Attualmente sono supportate solo le tabelle temporanee nell'ambito della sessione.  Le tabelle temporanee globali non sono supportate.  

Inoltre, le viste non possono essere create su tabelle temporanee.  Le tabelle temporanee possono essere create solo con la distribuzione hash o round robin.  La distribuzione di tabelle temporanee replicata non è supportata. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di tabelle, vedere l'articolo Progettazione di tabelle usando le risorse di [SQL Analytics.To](sql-data-warehouse-tables-overview.md) learn more about developing tables, see the Designing tables using the SQL Analytics resources article.

