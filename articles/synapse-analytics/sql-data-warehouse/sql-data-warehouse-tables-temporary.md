---
title: Tabelle temporanee
description: Linee guida essenziali per l'uso di tabelle temporanee nel pool Synapse SQL, con una particolare attenzione per i principi delle tabelle temporanee a livello di sessione.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 5f10b987fa8783084b14774b9bce5e857f3c59c4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650487"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Tabelle temporanee nel pool Synapse SQL
Questo articolo contiene le linee guida fondamentali per l'uso delle tabelle temporanee ed evidenzia i principi delle tabelle temporanee a livello di sessione. 

Usando le informazioni di questo articolo è possibile modularizzare il codice, aumentando le possibilità di riutilizzo e la facilità di manutenzione.

## <a name="what-are-temporary-tables"></a>Introduzione delle tabelle temporanee
Le tabelle temporanee sono utili per l'elaborazione dati, soprattutto durante la trasformazione in cui i risultati intermedi sono temporanei. Nel pool SQL le tabelle temporanee esistono a livello di sessione.  

Sono visibili solo per la sessione in cui sono state create e vengono eliminate automaticamente quando la sessione si disconnette.  

Le tabelle temporanee offrono un miglioramento delle prestazioni, perché i loro risultati vengono scritti in locale anziché nell'archiviazione remota.

Le tabelle temporanee sono utili per l'elaborazione dati, soprattutto durante la trasformazione in cui i risultati intermedi sono temporanei. Con il pool SQL, le tabelle temporanee esistono a livello di sessione.  Sono visibili solo per la sessione in cui sono state create. Di conseguenza, vengono eliminati automaticamente quando la sessione si disconnette. 

## <a name="temporary-tables-in-sql-pool"></a>Tabelle temporanee nel pool SQL

Nella risorsa pool SQL le tabelle temporanee offrono un miglioramento delle prestazioni, perché i risultati vengono scritti in locale invece che nell'archiviazione remota.

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

Se si chiama la stessa stored procedure, che crea una variabile temporanea con lo stesso nome, per assicurarsi che le istruzioni `CREATE TABLE` riescano è possibile eseguire un semplice controllo di preesistenza con `DROP`, come nell'esempio seguente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Una procedura consigliata per la coerenza del codice consiste nell'usare questo modello sia per le tabelle che per le tabelle temporanee.  È inoltre consigliabile usare `DROP TABLE` per rimuovere le tabelle temporanee quando non sono più necessarie nel codice.  

Nello sviluppo di stored procedure in genere i comandi di eliminazione vengono raggruppati alla fine di una procedura per assicurare che questi oggetti vengano puliti.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizzazione del codice
Dal momento che le tabelle temporanee sono visibili ovunque in una sessione utente, questa funzionalità può essere sfruttata per modularizzare il codice dell'applicazione.  

Ad esempio, la stored procedure seguente genera un'istruzione DDL per aggiornare tutte le statistiche nel database in base al nome:

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

In questa fase l'unica azione che si è verificata è la creazione di una stored procedure che genera una tabella temporanea, #stats_ddl, con le istruzioni DDL.  

Questa stored procedure elimina una tabella #stats_ddl esistente per assicurare che non generi errori se viene eseguita più volte all'interno di una sessione.  

Tuttavia, poiché non esiste `DROP TABLE` alla fine della stored procedure, al termine della stored procedure, la tabella creata viene conservata in modo che possa essere letta all'esterno della stored procedure.  

Nel pool SQL, diversamente da altri database di SQL Server, è possibile usare la tabella temporanea all'esterno della procedura che l'ha creata.  Le tabelle temporanee del pool SQL possono essere usate **ovunque** all'interno della sessione. Questa funzionalità assicura un codice più modulare e gestibile, come nell'esempio seguente:

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
Il pool SQL impone un paio di limitazioni quando si implementano tabelle temporanee.  Attualmente sono supportate solo le tabelle temporanee nell'ambito della sessione.  Le tabelle temporanee globali non sono supportate.  

Non è possibile creare viste nelle tabelle temporanee.  Le tabelle temporanee possono essere create solo con la distribuzione hash o round robin.  La distribuzione di tabelle temporanee replicate non è supportata. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di tabelle, vedere l'articolo [Progettazione di tabelle con le risorse Synapse SQL](sql-data-warehouse-tables-overview.md).

