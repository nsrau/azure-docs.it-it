---
title: Risorse per lo sviluppo di un data warehouse in Azure | Microsoft Docs
description: Concetti di sviluppo, decisioni di progettazione, suggerimenti e tecniche di codifica per SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 03/22/2018
ms.author: jrj;barbkess
ms.openlocfilehash: c81a6f9af556a86f10d2482333fdc98f9cfb1454
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisioni di progettazione e tecniche di codifica per SQL Data Warehouse
Esaminare questi articoli sullo sviluppo per capire meglio le decisioni chiave nella progettazione, i suggerimenti e le tecniche di codifica per SQL Data Warehouse.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti illustrano concetti e decisioni di progettazione per lo sviluppo di un data warehouse distribuito con SQL Data Warehouse:

* [connessioni][connections]
* [concorrenza][concurrency]
* [transazioni][transactions]
* [schemi definiti dall'utente][user-defined schemas]
* [distribuzione di tabelle][table distribution]
* [indici di tabella][table indexes]
* [partizioni di tabella][table partitions]
* [CTAS][CTAS]
* [statistiche][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Sviluppo dei suggerimenti e delle tecniche di codifica
Questi articoli illustrano le tecniche di codifica, i consigli e i suggerimenti specifici per lo sviluppo di un SQL Data Warehouse:

* [stored procedure][stored procedures]
* [etichette][labels]
* [viste][views]
* [tabelle temporanee][temporary tables]
* [SQL dinamico][dynamic SQL]
* [cicli][looping]
* [opzioni di raggruppamento][group by options]
* [assegnazione di variabili][variable assignment]

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
