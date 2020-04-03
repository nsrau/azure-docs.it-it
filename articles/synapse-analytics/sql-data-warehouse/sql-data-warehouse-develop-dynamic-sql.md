---
title: Utilizzo di SQL dinamicoUsing dynamic SQL
description: Suggerimenti per soluzioni di sviluppo che usano SQL dinamico nel pool Sql Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a285c273a0bc590a9f5b4ade782f2195a361cd6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619029"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>SQL dinamico nel pool SQL Synapse
In questo articolo sono inclusi suggerimenti per le soluzioni di sviluppo che usano SQL dinamico nel pool SQL.

## <a name="dynamic-sql-example"></a>Esempio di SQL dinamico

Quando si sviluppa codice dell'applicazione per il pool SQL, potrebbe essere necessario usare SQL dinamico per fornire soluzioni flessibili, generiche e modulari. Il pool SQL non supporta i tipi di dati BLOB in questo momento. 

Il mancato supporto dei tipi di dati BLOB potrebbe limitare le dimensioni delle stringhe poiché i tipi di dati BLOB includono i tipi varchar (max) e nvarchar (max). 

Se questi tipi sono stati utilizzati nel codice dell'applicazione per compilare stringhe di grandi dimensioni, è necessario suddividere il codice in blocchi e utilizzare invece l'istruzione EXEC.

Un semplice esempio:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se la stringa è breve, è possibile usare [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) come di consueto.

> [!NOTE]
> Le istruzioni eseguite come SQL dinamico saranno comunque soggette a tutte le regole di convalida T-SQL.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).

