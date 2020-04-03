---
title: Uso delle stored procedure
description: Suggerimenti per lo sviluppo di soluzioni mediante l'implementazione di stored procedure nel pool Sql Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a8350f8027a78ae5692e12661f2e0d2013ab4c46
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618958"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Utilizzo di stored procedure nel pool SQL SynapseUsing stored procedures in Synapse SQL pool
In questo articolo vengono forniti suggerimenti per lo sviluppo di soluzioni di pool SQL implementando stored procedure.

## <a name="what-to-expect"></a>Cosa aspettarsi

SQL pool supports many of the T-SQL features that are used in SQL Server. Ancora più importanti sono le funzionalità di scale-out specifiche, che si possono usare per migliorare le prestazioni della soluzione.

Inoltre, per mantenere la scalabilità e le prestazioni del pool SQL, esistono funzionalità e funzionalità aggiuntive con differenze di comportamento.


## <a name="introducing-stored-procedures"></a>Introduzione alle stored procedure
Le stored procedure sono un ottimo modo per incapsulare il codice SQL, che viene archiviato vicino ai dati del pool SQL. Le stored procedure consentono inoltre agli sviluppatori di modularizzare le soluzioni incapsulando il codice in unità gestibili, facilitando così una maggiore riusabilità del codice. Ogni stored procedure può anche accettare parametri per essere ancora più flessibile.

Il pool SQL fornisce un'implementazione semplificata e semplificata delle stored procedure. La differenza più grande rispetto a SQL Server è che la stored procedure non è codice precompilato. 

In generale, per i data warehouse, il tempo di compilazione è ridotto rispetto al tempo necessario per eseguire query su volumi di dati di grandi dimensioni. È più importante assicurarsi che il codice della stored procedure sia ottimizzato correttamente per query di grandi dimensioni. 

> [!TIP]
> L'obiettivo consiste nel risparmiare ore, minuti e secondi, non millisecondi. Pertanto, è utile considerare le stored procedure come contenitori per la logica SQL.     

Quando il pool SQL esegue la stored procedure, le istruzioni SQL vengono analizzate, convertite e ottimizzate in fase di esecuzione. Durante questo processo ogni istruzione viene convertita in query distribuite. Il codice SQL eseguito sui dati è diverso dalla query inviata.

## <a name="nesting-stored-procedures"></a>Annidamento di stored procedure
Quando le stored procedure chiamano altre stored procedure o eseguono istruzioni SQL dinamiche, la stored procedure o la chiamata di codice interna è detta annidata.

Il pool SQL supporta un massimo di otto livelli di annidamento. Al contrario, il livello di annidamento in SQL Server è 32.In contrast, the nest level in SQL ServerIS 32.

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

Il pool SQL non supporta attualmente [.@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql) Come tale, è necessario monitorare il livello del nido. È improbabile che superi il limite di otto anni di nidificazione. Tuttavia, in tal caso, è necessario rielaborare il codice per adattarlo ai livelli di annidamento entro questo limite.

## <a name="insertexecute"></a>INSERT..EXECUTE
Il pool SQL non consente di utilizzare il set di risultati di una stored procedure con un'istruzione INSERT. Esiste tuttavia un approccio alternativo. Per un esempio, vedere l'articolo sulle [tabelle temporanee](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Limitazioni
Esistono alcuni aspetti delle stored procedure Transact-SQLTransact-SQL che non sono implementate nel pool SQL, che sono i seguenti:

* Stored procedure temporanee
* Stored procedure numerate
* Stored procedure estese
* Stored procedure CLR
* Opzione di crittografia
* Opzione di replica
* Parametri con valori di tabella
* Parametri di sola lettura
* Parametri predefiniti
* Contesti di esecuzione
* Istruzione return

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

