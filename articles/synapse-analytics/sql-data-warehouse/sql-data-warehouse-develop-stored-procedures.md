---
title: Uso delle stored procedure
description: Suggerimenti per lo sviluppo di soluzioni tramite l'implementazione di stored procedure nel pool SQL sinapsi.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 87c7eaa57f9da87bd83f89953afc09632d42b1f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213398"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Uso di stored procedure nel pool di SQL sinapsi

Questo articolo fornisce suggerimenti per lo sviluppo di soluzioni di pool SQL mediante l'implementazione di stored procedure.

## <a name="what-to-expect"></a>Cosa aspettarsi

Il pool SQL supporta molte delle funzionalità T-SQL usate in SQL Server. Ancora più importanti sono le funzionalità di scale-out specifiche, che si possono usare per migliorare le prestazioni della soluzione.

Inoltre, per semplificare la gestione della scalabilità e delle prestazioni del pool SQL, esistono funzionalità e funzionalità aggiuntive con differenze di comportamento.

## <a name="introducing-stored-procedures"></a>Introduzione alle stored procedure

Le stored procedure sono un ottimo modo per incapsulare il codice SQL archiviato vicino ai dati del pool SQL. Le stored procedure consentono inoltre agli sviluppatori di modularizzare le proprie soluzioni incapsulando il codice in unità gestibili, semplificando così la riusabilità del codice. Ogni stored procedure può anche accettare parametri per essere ancora più flessibile.

Il pool SQL fornisce un'implementazione di stored procedure semplificata e semplificata. La differenza principale rispetto a SQL Server è che il stored procedure non è un codice precompilato.

In generale, per i data warehouse, il tempo di compilazione è ridotto rispetto al tempo necessario per eseguire query su volumi di dati di grandi dimensioni. È più importante verificare che il codice stored procedure sia correttamente ottimizzato per le query di grandi dimensioni.

> [!TIP]
> L'obiettivo consiste nel risparmiare ore, minuti e secondi, non millisecondi. È quindi utile considerare le stored procedure come contenitori per la logica SQL.

Quando il pool SQL esegue il stored procedure, le istruzioni SQL vengono analizzate, convertite e ottimizzate in fase di esecuzione. Durante questo processo ogni istruzione viene convertita in query distribuite. Il codice SQL eseguito sui dati è diverso dalla query inviata.

## <a name="nesting-stored-procedures"></a>Annidamento di stored procedure

Quando le stored procedure chiamano altre stored procedure o eseguono istruzioni SQL dinamiche, la stored procedure o la chiamata di codice interna è detta annidata.

Il pool SQL supporta un massimo di otto livelli di annidamento. Al contrario, il livello di annidamento nella SQL Server è 32.

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

Il pool SQL attualmente non [supporta @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Di conseguenza, è necessario tenere traccia del livello di annidamento. È improbabile che venga superato il limite di otto livelli di annidamento. Tuttavia, se si esegue questa operazione, è necessario rielaborare il codice per adattarsi ai livelli di nidificazione entro questo limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

Il pool SQL non consente di utilizzare il set di risultati di un stored procedure con un'istruzione INSERT. Esiste, tuttavia, un approccio alternativo che è possibile usare. Per un esempio, vedere l'articolo sulle [tabelle temporanee](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Limitazioni

Esistono alcuni aspetti delle stored procedure Transact-SQL che non sono implementate nel pool SQL, come indicato di seguito:

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
