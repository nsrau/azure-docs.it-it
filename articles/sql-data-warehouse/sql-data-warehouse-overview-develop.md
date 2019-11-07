---
title: Risorse per lo sviluppo di un data warehouse in Azure
description: Concetti di sviluppo, decisioni di progettazione, suggerimenti e tecniche di codifica per SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645606"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisioni di progettazione e tecniche di codifica per SQL Data Warehouse
Esaminare questi articoli sullo sviluppo per capire meglio le decisioni chiave nella progettazione, i suggerimenti e le tecniche di codifica per SQL Data Warehouse.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti illustrano concetti e decisioni di progettazione per lo sviluppo di un data warehouse distribuito con SQL Data Warehouse:

* [connections][connections]
* [Concorrenza][concurrency]
* [Transazioni][transactions]
* [Schemi definiti dall'utente][user-defined schemas]
* [Distribuzione di tabelle in SQL Data Warehouse][table distribution]
* [Indicizzazione di tabelle in SQL Data Warehouse][table indexes]
* [Partizioni di tabella][table partitions]
* [CTAS][CTAS]
* [Statistiche][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Sviluppo dei suggerimenti e delle tecniche di codifica
Questi articoli illustrano le tecniche di codifica, i consigli e i suggerimenti specifici per lo sviluppo di un SQL Data Warehouse:

* [Stored procedure][stored procedures]
* [Etichette][labels]
* [Viste][views]
* [Tabelle temporanee][temporary tables]
* [SQL dinamico][dynamic SQL]
* [Cicli][looping]
* [Opzioni di raggruppamento][group by options]
* [Assegnazione di variabili][variable assignment]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni di riferimento, vedere [Istruzioni T-SQL di SQL Data Warehouse](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
