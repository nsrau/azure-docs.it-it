<properties
   pageTitle="Assegnazione di variabili in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'assegnazione di variabili Transact-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
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
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Assegnare variabili in SQL Data Warehouse
Le variabili in SQL Data Warehouse vengono impostate usando l'istruzione `DECLARE` o `SET`.

Tutti modi seguenti sono perfettamente validi per impostare il valore di una variabile:

## Impostazione delle variabili con DECLARE

L'inizializzazione di variabili con DECLARE è uno dei modi più flessibili per impostare un valore della variabile in SQL Data Warehouse.

```
DECLARE @v  int = 0
;
```

È anche possibile usare DECLARE per impostare più di una variabile contemporaneamente. Non è possibile usare `SELECT` o `UPDATE` per eseguire questa operazione:

```
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Non è possibile inizializzare e usare una variabile nella stessa istruzione DECLARE. Per illustrare il concetto, l'esempio seguente **non** è consentito, perché @p1 viene inizializzato e usato nella stessa istruzione DECLARE. Ciò comporterà un errore.

```
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## Impostazione di valori con SET
Set è un metodo molto comune per l'impostazione di una singola variabile.

Tutti gli esempi seguenti sono modi validi per impostare una variabile con SET:

```
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

È possibile impostare una sola variabile alla volta con SET. Tuttavia, come illustrato sopra, gli operatori composti sono consentiti.

## Limitazioni
Non è possibile usare SELECT o UPDATE per l'assegnazione di variabili.


## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->