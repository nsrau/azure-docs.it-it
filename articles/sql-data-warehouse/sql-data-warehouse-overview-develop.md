---
title: Risorse per lo sviluppo di un data warehouse in Azure | Documentazione Microsoft
description: Concetti di sviluppo, decisioni di progettazione, suggerimenti e tecniche di codifica per SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: 37344b7916d8ceb2ad3b6a34df9fc8681af4dff7


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisioni di progettazione e tecniche di codifica per SQL Data Warehouse
Esaminare questi articoli sullo sviluppo per capire meglio le decisioni chiave nella progettazione, i suggerimenti e le tecniche di codifica per SQL Data Warehouse.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti illustrano alcuni dei concetti chiave e alcune delle decisioni di progettazione che sarà necessario conoscere per lo sviluppo del data warehouse distribuito tramite SQL Data Warehouse:

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
Questi articoli illustrano le tecniche di codifica, i consigli e i suggerimenti consigliate specifici per lo sviluppo dell’SQL Data Warehouse:

* [stored procedure][stored procedures]
* [etichette][labels]
* [viste][views]
* [tabelle temporanee][temporary tables]
* [SQL dinamico][dynamic SQL]
* [cicli][looping]
* [opzioni di raggruppamento][group by options]
* [assegnazione di variabili][variable assignment]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver letto gli articoli sullo sviluppo, esaminare la pagina relativa agli [argomenti di riferimento per Transact-SQL][Transact-SQL reference] per altri dettagli sulla sintassi supportata per SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[concurrency]: ./sql-data-warehouse-develop-concurrency.md
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
[Transact-SQL reference]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->



<!--HONumber=Jan17_HO4-->


