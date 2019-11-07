---
title: Utilizzo di SQL dinamico
description: Suggerimenti per l’utilizzo di SQL dinamico in SQL Data Warehouse di Azure per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 87320128537a235e8047a2f826b0e59c08aef76b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692850"
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

