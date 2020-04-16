---
title: Usare tabelle temporanee con Synapse SQLUse temporary tables with Synapse SQL
description: Indicazioni essenziali per l'utilizzo di tabelle temporanee in Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428758"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tabelle temporanee in Synapse SQL

Questo articolo contiene indicazioni essenziali per l'utilizzo di tabelle temporanee ed evidenzia i principi delle tabelle temporanee a livello di sessione all'interno di Synapse SQL. 

Sia il pool SQL che le risorse sql su richiesta (anteprima) possono utilizzare tabelle temporanee. SQL su richiesta presenta limitazioni descritte alla fine di questo articolo. 

## <a name="what-are-temporary-tables"></a>Introduzione delle tabelle temporanee

Le tabelle temporanee sono utili durante l'elaborazione dei dati, in particolare durante la trasformazione in cui i risultati intermedi sono temporanei. Con Synapse SQL, le tabelle temporanee esistono a livello di sessione.  Sono visibili solo alla sessione in cui sono stati creati. Di conseguenza, vengono eliminati automaticamente quando la sessione si disconnette. 

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

### <a name="dropping-temporary-tables"></a>Eliminazione delle tabelle temporanee
Quando viene creata una nuova sessione, non deve esistere alcuna tabella temporanea.  Tuttavia, se si chiama la stessa stored procedure che crea un `CREATE TABLE` temporaneo con lo stesso nome, per `DROP`assicurarsi che le istruzioni abbiano esito positivo, utilizzare un semplice controllo di preesistenza con : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Per la coerenza di codifica, è consigliabile usare questo modello sia per le tabelle che per le tabelle temporanee.  È anche una buona idea `DROP TABLE` da usare per rimuovere le tabelle temporanee quando hai finito con loro.  

Nello sviluppo di stored procedure, è comune vedere i comandi di rilascio in bundle insieme alla fine di una procedura per garantire che questi oggetti vengono puliti.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>Modularizzazione del codice
Le tabelle temporanee possono essere utilizzate in qualsiasi punto di una sessione utente. Questa funzionalità può quindi essere sfruttata per semplificare il codice dell'applicazione.  Per dimostrarlo, la stored procedure seguente genera DDL per aggiornare tutte le statistiche nel database in base al nome della statistica:

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

In questa fase, l'unica azione che si è verificata è la creazione di una stored procedure che genera l'#stats_ddl tabella temporanea.  La stored procedure elimina #stats_ddl se esiste già. Questo calo assicura che non abbia esito negativo se viene eseguito più di una volta all'interno di una sessione.  

Poiché non è `DROP TABLE` presente un alla fine della stored procedure, quando la stored procedure viene completata, la tabella creata rimane e può essere letta all'esterno della stored procedure.  

A differenza di altri database di SQL Server, Synapse SQL consente di utilizzare la tabella temporanea al di fuori della procedura che l'ha creata.  Le tabelle temporanee create tramite il pool SQL possono essere utilizzate **in qualsiasi punto** della sessione. Di conseguenza, si avrà codice più modulare e gestibile, come illustrato nell'esempio seguente:As a result, you'll have more modular and manageable code, as demonstrated in the sample below:

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

### <a name="temporary-table-limitations"></a>Limitazioni della tabella temporanea

Il pool SQL ha alcune limitazioni di implementazione per le tabelle temporanee:SQL pool does a few implementation limitations for temporary tables:

- Sono supportate solo le tabelle temporanee con ambito sessione.  Le tabelle temporanee globali non sono supportate.
- Non è possibile creare viste su tabelle temporanee.
- Le tabelle temporanee possono essere create solo con la distribuzione hash o round robin.  La distribuzione di tabelle temporanee replicata non è supportata. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Tabelle temporanee in SQL su richiesta (anteprima)Temporary tables in SQL on-demand (preview)

Le tabelle temporanee in SQL su richiesta sono supportate, ma il loro utilizzo è limitato. Non possono essere utilizzati nelle query che riguardano i file di destinazione. 

Ad esempio, non è possibile unire una tabella temporanea con i dati dei file nell'archivio. Il numero di tabelle temporanee è limitato a 100 e la loro dimensione totale è limitata a 100 MB.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di tabelle, vedere l'articolo [Progettazione di tabelle usando le risorse SQL di Synapse.To](develop-tables-overview.md) learn more about developing tables, see the Designing tables using the Synapse SQL resources article.

