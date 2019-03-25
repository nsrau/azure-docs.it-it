---
title: Assegnare variabili in Azure SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per l'assegnazione di variabili T-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: ee97f7e5cda8b954fb697f73746e416d88d38c2d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401684"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Assegnazioni di variabili in Azure SQL Data Warehouse

Suggerimenti per l'assegnazione di variabili T-SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni.

## <a name="setting-variables-with-declare"></a>Impostazione delle variabili con DECLARE

Le variabili in SQL Data Warehouse vengono impostate usando l'istruzione `DECLARE` o `SET`. L'inizializzazione di variabili con DECLARE è uno dei modi più flessibili per impostare un valore della variabile in SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

È anche possibile usare DECLARE per impostare più di una variabile contemporaneamente. Non è possibile usare SELECT o UPDATE per eseguire le operazioni seguenti:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Non è possibile inizializzare e usare una variabile nella stessa istruzione DECLARE. Per illustrare il concetto, l'esempio seguente **non** è consentito, perché @p1 viene inizializzato e usato nella stessa istruzione DECLARE. L'esempio seguente restituisce un errore.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Impostazione di valori con SET

SET è un metodo comune per l'impostazione di una singola variabile.

Le istruzioni seguenti sono tutte valide per impostare una variabile con SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

È possibile impostare una sola variabile alla volta con SET. Tuttavia, gli operatori composti sono consentiti.

## <a name="limitations"></a>Limitazioni

AGGIORNAMENTO non è possibile utilizzare per l'assegnazione di variabile.

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
