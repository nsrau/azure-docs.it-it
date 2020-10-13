---
title: Utilizzare stored procedure
description: Suggerimenti per l'implementazione di stored procedure in sinapsi SQL per lo sviluppo di soluzioni.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 09/23/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f2046614f3665a699d02c76210676fb32f99fc73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288920"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>Usare stored procedure in sinapsi SQL

Suggerimenti per l'implementazione di stored procedure nel pool SQL sinapsi (data warehouse) per lo sviluppo di soluzioni.

## <a name="what-to-expect"></a>Risultati previsti

Sinapsi SQL supporta molte delle funzionalità T-SQL usate in SQL Server. Ancora più importanti sono le funzionalità di scale-out specifiche, che si possono usare per migliorare le prestazioni della soluzione.

Per mantenere la scalabilità e le prestazioni del pool SQL, esistono anche alcune caratteristiche e funzionalità con differenze comportamentali e altre che non sono supportate.

## <a name="stored-procedures-in-synapse-sql"></a>Stored procedure in sinapsi SQL

Le stored procedure sono un ottimo modo per incapsulare il codice SQL e archiviarlo vicino ai dati nell'data warehouse. Le stored procedure consentono agli sviluppatori di modularizzare le proprie soluzioni incapsulando il codice in unità gestibili, facilitando una maggiore riusabilità del codice. Ogni stored procedure può anche accettare parametri per essere ancora più flessibile. Nell'esempio seguente, è possibile visualizzare le procedure che eliminano gli oggetti esterni se sono presenti nel database:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Queste procedure possono essere eseguite utilizzando `EXEC` l'istruzione in cui è possibile specificare il nome e i parametri della procedura:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Sinapsi SQL fornisce un'implementazione di stored procedure semplificata e semplificata. La differenza principale rispetto a SQL Server è che la stored procedure non è codice precompilato. Nei data warehouse il tempo di compilazione è limitato rispetto al tempo necessario per eseguire query su grandi volumi di dati. È più importante assicurarsi che il codice della stored procedure sia correttamente ottimizzato per le query di grandi dimensioni. L'obiettivo consiste nel risparmiare ore, minuti e secondi, non millisecondi. È quindi più utile pensare alle stored procedure come contenitori per la logica di SQL.

Quando si esegue la stored procedure sinapsi SQL, le istruzioni SQL vengono analizzate, convertite e ottimizzate in fase di esecuzione. Durante questo processo ogni istruzione viene convertita in query distribuite. Il codice SQL eseguito sui dati è diverso dalla query inviata.

## <a name="encapsulate-validation-rules"></a>Incapsula regole di convalida

Le stored procedure consentono di individuare la logica di convalida in un singolo modulo archiviato nel database SQL. Nell'esempio seguente, è possibile vedere come convalidare i valori dei parametri e modificarne i valori predefiniti.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

La logica nella procedura SQL convaliderà i parametri di input quando viene chiamata la stored procedure.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Annidamento di stored procedure

Quando le stored procedure chiamano altre stored procedure o eseguono istruzioni SQL dinamiche, la stored procedure o la chiamata di codice interna è detta annidata.
Un esempio di procedura nidificata è illustrato nel codice seguente:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Questa procedura accetta un parametro che rappresenta un nome e quindi chiama altre routine per eliminare gli oggetti con questo nome.
Il pool SQL sinapsi supporta un massimo di otto livelli di annidamento. Questa funzionalità è leggermente diversa rispetto a SQL Server. In SQL Server i livelli di annidamento sono 32.

La chiamata di stored procedure di massimo livello equivale al livello di annidamento 1.

```sql
EXEC clean_up 'mytest'
```

Se la stored procedure effettua anche un'altra chiamata EXEC, il livello di annidamento aumenta a due.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Se la seconda routine esegue poi istruzioni in SQL dinamico, il livello di annidamento aumenterà a tre.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> La sinapsi SQL attualmente non supporta [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). È necessario tenere traccia del livello di annidamento. È improbabile superare il limite di otto livelli di annidamento, ma se lo si fa, è necessario rielaborare il codice per adattare i livelli di annidamento entro tale limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

Sinapsi SQL non consente di utilizzare il set di risultati di un stored procedure con un'istruzione INSERT. È possibile usare un approccio alternativo. Per un esempio, vedere l'articolo sulle [tabelle temporanee](develop-tables-temporary.md).

## <a name="limitations"></a>Limitazioni

Esistono alcuni aspetti delle stored procedure Transact-SQL che non sono implementate in sinapsi SQL, ad esempio:

* Stored procedure temporanee
* Stored procedure numerate
* Stored procedure estese
* Stored procedure CLR
* Opzione di crittografia
* Opzione di replica
* Parametri con valori di tabella
* Parametri di sola lettura
* parametri predefiniti (nel pool di cui è stato effettuato il provisioning)
* Contesti di esecuzione
* Istruzione return

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](develop-overview.md).
