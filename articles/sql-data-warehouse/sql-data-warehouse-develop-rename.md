<properties
   pageTitle="Rinominare in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la ridenominazione di tabelle in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Rinominare in SQL Data Warehouse
SQL Data Warehouse usa l'istruzione [RENAME][] per rinominare le tabelle. Questo comportamento è diverso da SQL Server, che usa `sp_rename`. Attualmente è possibile rinominare solo le tabelle utente. Le tabelle esterne e i database non possono essere rinominati.

## Rinominare tabella

Quando si rinomina una tabella, tutti gli oggetti e le proprietà associati alla tabella vengono aggiornati per fare riferimento al nuovo nome della tabella. Ad esempio, le definizioni della tabella, gli indici, i vincoli e le autorizzazioni vengono aggiornati. Le visualizzazioni non vengono aggiornate.

La sintassi per rinominare una tabella è:

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## Modificare uno schema di tabella

Per modificare lo schema a cui appartiene un oggetto, usare l'istruzione ALTER SCHEMA:

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## La ridenominazione della tabella richiede un blocco esclusivo sulla tabella

Non è possibile rinominare una tabella mentre è in uso. Una ridenominazione di una tabella richiede un blocco esclusivo sulla tabella. Se la tabella è in uso, si potrebbe dover terminare la sessione utilizzando la tabella. Per terminare una sessione, usare il comando [KILL][]. Ad esempio, `KILL 'SID1234'`. Prestare attenzione quando si usa l'istruzione KILL perché, quando una sessione viene terminata, verrà eseguito il rollback di qualsiasi lavoro transazionale non sottoposto a commit. Per altre informazioni, vedere [Sessions and requests][] (Sessioni e richieste). Per altre informazioni sull'impatto dell'arresto di una query transazionale e sugli effetti di un rollback, vedere [Ottimizzazione delle transazioni per SQL Data Warehouse][]


## Passaggi successivi
Per altri riferimenti T-SQL, vedere [T-SQL references][] (Riferimenti T-SQL).

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[sessions and requests]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[T-SQL references]: ./sql-data-warehouse-reference-tsql-statements.md
[Ottimizzazione delle transazioni per SQL Data Warehouse]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->