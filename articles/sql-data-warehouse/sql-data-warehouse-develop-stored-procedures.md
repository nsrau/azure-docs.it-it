---
title: Uso di stored procedure in Azure SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per l'implementazione delle stored procedure in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5659e8f29d87c48c447a5cb81c836b0be9dabd45
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Uso di stored procedure in SQL Data Warehouse
Suggerimenti per l'implementazione delle stored procedure in Azure SQL Data Warehouse per lo sviluppo di soluzioni.

## <a name="what-to-expect"></a>Cosa aspettarsi

SQL Data Warehouse supporta molte delle funzionalità T-SQL che si usano in SQL Server. Ancora più importanti sono le funzionalità di scale-out specifiche, che si possono usare per migliorare le prestazioni della soluzione.

Tuttavia, per mantenere la scalabilità e le prestazioni di SQL Data Warehouse sono disponibili anche funzionalità e caratteristiche con differenze di comportamento e altra che non sono supportate.


## <a name="introducing-stored-procedures"></a>Introduzione alle stored procedure
Le stored procedure sono un ottimo modo per incapsulare il codice SQL, archiviandolo vicino i dati nel data warehouse. Le stored procedure consentono agli sviluppatori di rendere modulari le soluzioni incapsulando il codice in unità gestibili, facilitano così il riutilizzo del codice stesso. Ogni stored procedure può anche accettare parametri per essere ancora più flessibile.

SQL Data Warehouse fornisce un'implementazione semplificata e ottimizzata delle stored procedure. La differenza principale rispetto a SQL Server è che la stored procedure non è codice precompilato. Nei data warehouse il tempo di compilazione è limitato rispetto al tempo necessario per eseguire query su grandi volumi di dati. È più importante assicurarsi che il codice della stored procedure sia correttamente ottimizzato per le query di grandi dimensioni. L'obiettivo consiste nel risparmiare ore, minuti e secondi, non millisecondi. È quindi più utile pensare alle stored procedure come contenitori per la logica di SQL.     

Quando SQL Data Warehouse esegue la stored procedure, le istruzioni SQL vengono analizzate, convertite e ottimizzate in fase di esecuzione. Durante questo processo ogni istruzione viene convertita in query distribuite. Il codice SQL eseguito sui dati è diverso dalla query inviata.

## <a name="nesting-stored-procedures"></a>Annidamento di stored procedure
Quando le stored procedure chiamano altre stored procedure o eseguono istruzioni SQL dinamiche, la stored procedure o la chiamata di codice interna è detta annidata.

SQL Data Warehouse supporta un massimo di otto livelli di annidamento. Questo comportamento è leggermente diverso rispetto a SQL Server. In SQL Server i livelli di annidamento sono 32.

La chiamata di stored procedure di massimo livello equivale al livello di annidamento 1.

```sql
EXEC prc_nesting
```
Se la stored procedure effettua anche un'altra chiamata EXEC, il livello di annidamento aumenta a due.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se la seconda routine esegue poi istruzioni in SQL dinamico, il livello di annidamento aumenterà a tre.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Tenere presente che SQL Data Warehouse attualmente non supporta [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). È necessario tenere traccia del livello di annidamento. È improbabile superare il limite di otto livelli di annidamento, ma se lo si fa, è necessario rielaborare il codice per adattare i livelli di annidamento entro tale limite.

## <a name="insertexecute"></a>INSERT..EXECUTE
SQL Data Warehouse non consente di usare il set di risultati di una stored procedure con un'istruzione INSERT. Si può tuttavia un approccio alternativo. Per un esempio, vedere l'articolo sulle [tabelle temporanee](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Limitazioni
Esistono alcuni aspetti delle stored procedure Transact-SQL che non sono implementate in SQL Data Warehouse.

Sono:

* Stored procedure temporanee
* Stored procedure numerate
* Stored procedure estese
* Stored procedure CLR
* 
* Opzione di crittografia
* Opzione di replica
* Parametri con valori di tabella
* Parametri di sola lettura
* Parametri predefiniti
* Contesti di esecuzione
* Istruzione return

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

