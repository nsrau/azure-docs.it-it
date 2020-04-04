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
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633533"
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

Se la stringa è breve, è possibile usare [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) come di consueto.

> [!NOTE]
> Le istruzioni eseguite come SQL dinamico saranno comunque soggette a tutte le regole di convalida T-SQL.

## <a name="next-steps"></a>Passaggi successivi

Per altri suggerimenti sullo sviluppo, vedere la [panoramica dello sviluppo](sql-data-warehouse-overview-develop.md).
