---
title: SQL dinamico in SQL Data Warehouse | Documentazione Microsoft
description: "Suggerimenti per l’utilizzo di SQL dinamico in SQL Data Warehouse di Azure per lo sviluppo di soluzioni."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6da1efc35b624e0b06693037b4f91d71f0f40eb4


---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinamico in SQL Data Warehouse
Durante lo sviluppo di codice dell'applicazione per SQL Data Warehouse potrebbe essere necessario utilizzare SQL dinamico per offrire soluzioni flessibili, generiche e modulari. Attualmente SQL Data Warehouse non supporta i tipi di dati BLOB. Ciò potrebbe limitare le dimensioni delle stringhe poiché i tipi di BLOB includono tipi varchar(max) e nvarchar(max). Se sono stati utilizzati questi tipi nel codice dell'applicazione durante la creazione di stringhe molto grandi, è necessario separare il codice in blocchi e utilizzare invece l'istruzione EXEC.

Un semplice esempio:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se la stringa è corta, è possibile usare [sp_executesql][sp_executesql] come di consueto.

> [!NOTE]
> Le istruzioni eseguite come SQL dinamico saranno ancora soggette a tutte le regole di convalida TSQL.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica sullo sviluppo][panoramica sullo sviluppo].

<!--Image references-->

<!--Article references-->
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


