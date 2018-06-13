---
title: Uso di SQL dinamico in Azure SQL Data Warehouse | Microsoft Docs
description: Suggerimenti per l’utilizzo di SQL dinamico in SQL Data Warehouse di Azure per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 604074e0a645918f7033360b79a1b7cad050c9e4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
ms.locfileid: "31523167"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinamico in SQL Data Warehouse
Suggerimenti per l’utilizzo di SQL dinamico in SQL Data Warehouse di Azure per lo sviluppo di soluzioni.

## <a name="dynamic-sql-example"></a>Esempio di SQL dinamico

Durante lo sviluppo di codice dell'applicazione per SQL Data Warehouse potrebbe essere necessario usare SQL dinamico per offrire soluzioni flessibili, generiche e modulari. Attualmente SQL Data Warehouse non supporta i tipi di dati BLOB. Il mancato supporto dei tipi di dati BLOB potrebbe limitare le dimensioni delle stringhe poiché i tipi di dati BLOB includono i tipi varchar (max) e nvarchar (max). Se sono stati usati questi tipi nel codice dell'applicazione durante la creazione di stringhe di grandi dimensioni, è necessario separare il codice in blocchi e usare invece l'istruzione EXEC.

Un semplice esempio:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se la stringa è breve, è possibile usare [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) come di consueto.

> [!NOTE]
> Le istruzioni eseguite come SQL dinamico saranno ancora soggette a tutte le regole di convalida TSQL.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

