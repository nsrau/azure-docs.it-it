---
title: Utilizzare stored procedure
description: Suggerimenti per l'implementazione di stored procedure nel pool SQL sinapsi (data warehouse) per lo sviluppo di soluzioni.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 294652a42d3b6a2468f024ce7ebdbdfc3615f9e1
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647877"
---
# <a name="use-stored-procedures-in-sql-pool"></a>Usare stored procedure nel pool SQL

Suggerimenti per l'implementazione di stored procedure nel pool SQL sinapsi (data warehouse) per lo sviluppo di soluzioni.

## <a name="what-to-expect"></a>Risultati previsti

Il pool SQL supporta molte delle funzionalità T-SQL usate in SQL Server. Ancora più importanti sono le funzionalità di scale-out specifiche, che si possono usare per migliorare le prestazioni della soluzione.

Tuttavia, per mantenere la scalabilità e le prestazioni del pool SQL sono disponibili anche alcune funzionalità e funzionalità con differenze comportamentali e altre che non sono supportate.

## <a name="stored-procedures-in-sql-pool"></a>Stored procedure nel pool SQL

Le stored procedure sono un ottimo modo per incapsulare il codice SQL, archiviandolo vicino i dati nel data warehouse. Le stored procedure consentono agli sviluppatori di rendere modulari le soluzioni incapsulando il codice in unità gestibili, facilitano così il riutilizzo del codice stesso. Ogni stored procedure può anche accettare parametri per essere ancora più flessibile.

Il pool SQL fornisce un'implementazione di stored procedure semplificata e semplificata. La differenza principale rispetto a SQL Server è che la stored procedure non è codice precompilato. Nei data warehouse il tempo di compilazione è limitato rispetto al tempo necessario per eseguire query su grandi volumi di dati. È più importante assicurarsi che il codice della stored procedure sia correttamente ottimizzato per le query di grandi dimensioni. L'obiettivo consiste nel risparmiare ore, minuti e secondi, non millisecondi. È quindi più utile pensare alle stored procedure come contenitori per la logica di SQL.

Quando il pool SQL esegue il stored procedure, le istruzioni SQL vengono analizzate, convertite e ottimizzate in fase di esecuzione. Durante questo processo ogni istruzione viene convertita in query distribuite. Il codice SQL eseguito sui dati è diverso dalla query inviata.

## <a name="nesting-stored-procedures"></a>Annidamento di stored procedure

Quando le stored procedure chiamano altre stored procedure o eseguono istruzioni SQL dinamiche, la stored procedure o la chiamata di codice interna è detta annidata.

Il pool SQL supporta un massimo di otto livelli di annidamento. Questo comportamento è leggermente diverso rispetto a SQL Server. In SQL Server i livelli di annidamento sono 32.

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
EXEC sp_executesql N'SELECT ''another nest level'''  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> Il pool SQL attualmente non supporta [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). È necessario tenere traccia del livello di annidamento. È improbabile superare il limite di otto livelli di annidamento, ma se lo si fa, è necessario rielaborare il codice per adattare i livelli di annidamento entro tale limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

Il pool SQL non consente di utilizzare il set di risultati di un stored procedure con un'istruzione INSERT. Si può tuttavia un approccio alternativo. Per un esempio, vedere l'articolo sulle [tabelle temporanee](develop-tables-temporary.md).

## <a name="limitations"></a>Limitazioni

Esistono alcuni aspetti delle stored procedure Transact-SQL che non sono implementate nel pool SQL.

ovvero:

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

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](develop-overview.md).
