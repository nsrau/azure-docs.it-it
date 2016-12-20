---
title: Decisioni di progettazione e tecniche di codifica per lo sviluppo di SQL Data Warehouse | Documentazione Microsoft
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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d1c2255264a4240eecc51199050f8406d141ace2


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisioni di progettazione e tecniche di codifica per SQL Data Warehouse
Esaminare questi articoli sullo sviluppo per capire meglio le decisioni chiave nella progettazione, i suggerimenti e le tecniche di codifica per SQL Data Warehouse.

## <a name="key-design-decisions"></a>Decisioni chiave nella progettazione
Gli articoli seguenti illustrano alcuni dei concetti chiave e alcune delle decisioni di progettazione che sarà necessario conoscere per lo sviluppo del data warehouse distribuito tramite SQL Data Warehouse:

* [Connessioni][Connessioni]
* [Concorrenza][Concorrenza]
* [Transazioni][Transazioni]
* [Schemi definiti dall'utente][Schemi definiti dall'utente]
* [Distribuzione di tabelle in SQL Data Warehouse][Distribuzione di tabelle in SQL Data Warehouse]
* [Indicizzazione di tabelle in SQL Data Warehouse][Indicizzazione di tabelle in SQL Data Warehouse]
* [Partizioni di tabella][Partizioni di tabella]
* [CTAS][CTAS]
* [Statistiche][Statistiche]

## <a name="development-recommendations-and-coding-techniques"></a>Sviluppo dei suggerimenti e delle tecniche di codifica
Questi articoli illustrano le tecniche di codifica, i consigli e i suggerimenti consigliate specifici per lo sviluppo dell’SQL Data Warehouse:

* [Stored procedure][Stored procedure]
* [Etichette][Etichette]
* [Viste][Viste]
* [Tabelle temporanee][Tabelle temporanee]
* [SQL dinamico][SQL dinamico]
* [Cicli][Cicli]
* [Opzioni di raggruppamento][Opzioni di raggruppamento]
* [Assegnazione di variabili][Assegnazione di variabili]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver letto gli articoli sullo sviluppo, vedere la pagina relativa agli [argomenti di riferimento per Transact-SQL][argomenti di riferimento per Transact-SQL] per altre informazioni sulla sintassi supportata per SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[Concorrenza]: ./sql-data-warehouse-develop-concurrency.md
[Connessioni]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dinamico]: ./sql-data-warehouse-develop-dynamic-sql.md
[Opzioni di raggruppamento]: ./sql-data-warehouse-develop-group-by-options.md
[Etichette]: ./sql-data-warehouse-develop-label.md
[Cicli]: ./sql-data-warehouse-develop-loops.md
[Statistiche]: ./sql-data-warehouse-tables-statistics.md
[Stored procedure]: ./sql-data-warehouse-develop-stored-procedures.md
[Distribuzione di tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-distribute.md
[Indicizzazione di tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-index.md
[Partizioni di tabella]: ./sql-data-warehouse-tables-partition.md
[Tabelle temporanee]: ./sql-data-warehouse-tables-temporary.md
[Transazioni]: ./sql-data-warehouse-develop-transactions.md
[Schemi definiti dall'utente]: ./sql-data-warehouse-develop-user-defined-schemas.md
[Assegnazione di variabili]: ./sql-data-warehouse-develop-variable-assignment.md
[Viste]: ./sql-data-warehouse-develop-views.md
[argomenti di riferimento per Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[Ridenominazione di oggetti]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


