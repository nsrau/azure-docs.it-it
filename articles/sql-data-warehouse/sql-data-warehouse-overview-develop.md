<properties
   pageTitle="Sviluppare soluzioni per SQL Data Warehouse | Microsoft Azure"
   description="Tecniche di sviluppo e procedure consigliate per la creazione di soluzioni con Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Sviluppare soluzioni per SQL Data Warehouse

Esaminare gli articoli sullo sviluppo per capire meglio i principi relativi allo sviluppo, le procedure consigliate e le tecniche di codifica per SQL Data Warehouse.

## Concetti chiave
Gli articoli seguenti illustrano alcuni dei concetti chiave e alcune delle decisioni di progettazione che sarà necessario conoscere per lo sviluppo del data warehouse distribuito tramite SQL Data Warehouse:

- [Connessioni][]
- [Concorrenza][]
- [Transazioni][]
- [Schemi definiti dall'utente][]
- [Progettazione tabella][]
- [Chiavi di distribuzione hash][]
- [Partizioni di tabella][]
- [CTAS][]
- [Statistiche][]

## Tecniche di sviluppo
Questi articoli illustrano le tecniche di codifica, i suggerimenti e le procedure consigliate specifici per lo sviluppo di soluzioni con SQL Data Warehouse:

- [Stored procedure][]
- [Etichette][]
- [Viste][]
- [Tabelle temporanee][]
- [SQL dinamico][]
- [Cicli][]
- [Ridenominazione di oggetti][]
- [Trasformazione dei dati tramite pivot][]
- [Opzioni di raggruppamento][]
- [Assegnazione di variabili][]

## Passaggi successivi
Dopo aver letto gli articoli sullo sviluppo, esaminare la pagina relativa agli [argomenti di riferimento per Transact-SQL][] per altre informazioni sulla sintassi supportata per SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[Concorrenza]: sql-data-warehouse-develop-concurrency.md
[Connessioni]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[SQL dinamico]: sql-data-warehouse-develop-dynamic-sql.md
[Opzioni di raggruppamento]: sql-data-warehouse-develop-group-by-options.md
[Chiavi di distribuzione hash]: sql-data-warehouse-develop-hash-distribution-key.md
[Etichette]: sql-data-warehouse-develop-label.md
[Cicli]: sql-data-warehouse-develop-loops.md
[Trasformazione dei dati tramite pivot]: sql-data-warehouse-develop-pivot-unpivot.md
[Ridenominazione di oggetti]: sql-data-warehouse-develop-rename.md
[Statistiche]: sql-data-warehouse-develop-statistics.md
[Stored procedure]: sql-data-warehouse-develop-stored-procedures.md
[Progettazione tabella]: sql-data-warehouse-develop-table-design.md
[Partizioni di tabella]: sql-data-warehouse-develop-table-partitions.md
[Tabelle temporanee]: sql-data-warehouse-develop-temporary-tables.md
[Transazioni]: sql-data-warehouse-develop-transactions.md
[Schemi definiti dall'utente]: sql-data-warehouse-develop-user-defined-schemas.md
[Assegnazione di variabili]: sql-data-warehouse-develop-variable-assignment.md
[Viste]: sql-data-warehouse-develop-views.md

[argomenti di riferimento per Transact-SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->