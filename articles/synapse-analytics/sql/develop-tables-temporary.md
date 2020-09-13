---
title: Usare le tabelle temporanee in sinapsi SQL
description: Linee guida essenziali per l'uso di tabelle temporanee in sinapsi SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4559c72481dfa0cefb2ce84cab56a50d0bf182ef
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030328"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tabelle temporanee in sinapsi SQL

Questo articolo contiene indicazioni essenziali per l'uso di tabelle temporanee ed evidenzia i principi delle tabelle temporanee a livello di sessione all'interno di sinapsi SQL. 

Il pool SQL e le risorse SQL su richiesta (anteprima) possono usare le tabelle temporanee. SQL su richiesta presenta limitazioni che vengono discusse alla fine di questo articolo. 

## <a name="temporary-tables"></a>Tabelle temporanee

Le tabelle temporanee sono utili per l'elaborazione dati, soprattutto durante la trasformazione in cui i risultati intermedi sono temporanei. Con la sinapsi SQL, le tabelle temporanee esistono a livello di sessione.  Sono visibili solo per la sessione in cui sono state create. Di conseguenza, vengono eliminati automaticamente quando la sessione si disconnette. 

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

### <a name="drop-temporary-tables"></a>Elimina tabelle temporanee
Quando viene creata una nuova sessione, non deve esistere alcuna tabella temporanea.  Tuttavia, se si chiama lo stesso stored procedure che crea un oggetto temporaneo con lo stesso nome, per garantire che le `CREATE TABLE` istruzioni abbiano esito positivo, usare un semplice controllo di pre-esistenza con  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Una procedura consigliata per la coerenza del codice consiste nell'usare questo modello sia per le tabelle che per le tabelle temporanee.  È inoltre consigliabile utilizzare `DROP TABLE` per rimuovere le tabelle temporanee al termine dell'operazione.  

Nello sviluppo di stored procedure in genere i comandi di eliminazione vengono raggruppati alla fine di una procedura per assicurare che questi oggetti vengano puliti.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Codice modularizzare
Le tabelle temporanee possono essere utilizzate in qualsiasi punto di una sessione utente. Questa funzionalità può quindi essere sfruttata per consentire di modularizzare il codice dell'applicazione.  Per illustrare, il stored procedure seguente genera DDL per aggiornare tutte le statistiche nel database in base al nome della statistica:

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

In questa fase, l'unica azione che si è verificata è la creazione di un stored procedure che genera la #stats_ddl tabella temporanea.  Il stored procedure Elimina #stats_ddl se esiste già. Questa eliminazione garantisce che non abbia esito negativo se eseguita più volte all'interno di una sessione.  

Poiché non esiste un oggetto `DROP TABLE` alla fine del stored procedure, al termine del stored procedure, la tabella creata rimane e può essere letta all'esterno della stored procedure.  

Diversamente da altri database di SQL Server, sinapsi SQL consente di usare la tabella temporanea all'esterno della procedura che l'ha creata.  Le tabelle temporanee create tramite il pool SQL possono essere utilizzate in **qualsiasi punto** all'interno della sessione. Di conseguenza, si otterrà codice più modulare e gestibile, come illustrato nell'esempio seguente:

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

Il pool SQL presenta alcune limitazioni di implementazione per le tabelle temporanee:

- Sono supportate solo le tabelle temporanee con ambito sessione.  Le tabelle temporanee globali non sono supportate.
- Non è possibile creare viste nelle tabelle temporanee.
- Le tabelle temporanee possono essere create solo con la distribuzione hash o round robin.  La distribuzione di tabelle temporanee replicate non è supportata. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Tabelle temporanee in SQL su richiesta (anteprima)

Le tabelle temporanee in SQL su richiesta sono supportate, ma il loro utilizzo è limitato. Non possono essere usati nelle query che hanno come destinazione i file. 

Non è ad esempio possibile unire in join una tabella temporanea con dati provenienti da file nell'archivio. Il numero di tabelle temporanee è limitato a 100 e le relative dimensioni totali sono limitate a 100 MB.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di tabelle, vedere l'articolo [Progettazione di tabelle con le risorse Synapse SQL](develop-tables-overview.md).

