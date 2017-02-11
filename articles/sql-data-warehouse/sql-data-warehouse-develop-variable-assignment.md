---
title: Assegnazione di variabili in SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per l&quot;assegnazione di variabili Transact-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5ec9f7ad24dad833758d3b981fd4d8735119d813


---
# <a name="assign-variables-in-sql-data-warehouse"></a>Assegnare variabili in SQL Data Warehouse
Le variabili in SQL Data Warehouse vengono impostate usando l'istruzione `DECLARE` o `SET`.

Tutti modi seguenti sono perfettamente validi per impostare il valore di una variabile:

## <a name="setting-variables-with-declare"></a>Impostazione delle variabili con DECLARE
L'inizializzazione di variabili con DECLARE è uno dei modi più flessibili per impostare un valore della variabile in SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

È anche possibile usare DECLARE per impostare più di una variabile contemporaneamente. Non è possibile usare `SELECT` o `UPDATE` per eseguire questa operazione:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Non è possibile inizializzare e usare una variabile nella stessa istruzione DECLARE. Per illustrare il concetto, l'esempio seguente **non** è consentito, perché @p1 viene inizializzato e usato nella stessa istruzione DECLARE. Ciò comporterà un errore.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Impostazione di valori con SET
Set è un metodo molto comune per l'impostazione di una singola variabile.

Tutti gli esempi seguenti sono modi validi per impostare una variabile con SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

È possibile impostare una sola variabile alla volta con SET. Tuttavia, come illustrato sopra, gli operatori composti sono consentiti.

## <a name="limitations"></a>Limitazioni
Non è possibile usare SELECT o UPDATE per l'assegnazione di variabili.

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica sullo sviluppo][panoramica sullo sviluppo].

<!--Image references-->

<!--Article references-->
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


