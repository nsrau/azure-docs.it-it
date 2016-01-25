<properties
   pageTitle="Tabelle temporanee in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di tabelle temporanee in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Tabelle temporanee in SQL Data Warehouse
Le tabelle temporanee sono disponibili esistono a livello di sessione in SQL Data Warehouse. Sono definite come tabelle temporanee locali e, a differenza delle tabelle di SQL Server, è possibile accedervi da un punto qualsiasi della sessione.

Le tabelle temporanee sono molto utili durante l'elaborazione dati, soprattutto durante la trasformazione in cui i risultati intermedi sono temporanei.

Questo articolo evidenzia alcuni metodi specifici per l'uso di tabelle temporanee che consentono di modularizzare il codice.

## Modularizzazione del codice

Il fatto che le tabelle temporanee siano visibili in qualsiasi punto di una sessione utente può essere sfruttato per modularizzare il codice dell'applicazione.

Ecco un esempio funzionante.

La stored procedure seguente genera il codice DDL necessario per aggiornare le statistiche relative a ogni colonna nel database:

```
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

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
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
```

Si noti che non è stata effettivamente eseguita alcuna operazione con questi dati. La stored procedure ha semplicemente generato il codice DDL e lo ha archiviato in una tabella temporanea.

Tuttavia, in SQL Data Warehouse è possibile usare tale tabella temporanea all'esterno delle stored procedure che l'ha creata. Questo comportamento è diverso rispetto a SQL Server. In realtà la tabella temporanea può essere usata **ovunque** all'interno della sessione.

In questo modo è possibile ottenere codice più modulare e gestibile.

```
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

In alcuni casi le funzioni inline e con più istruzioni possono anche essere sostituite usando questa tecnica.

> [AZURE.NOTE]È anche possibile estendere questa soluzione. Se ad esempio si vuole solo aggiornare una singola tabella, è semplicemente necessario filtrare la tabella #stats\_ddl.

## Limitazioni della tabella temporanea
SQL Data Warehouse impone un paio di limitazioni quando si implementano tabelle temporanee.

Ecco le limitazioni principali:

- Le tabelle temporanee globali non sono supportate.
- Non è possibile creare visualizzazioni nelle tabelle temporanee.


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->