---
title: Utilizzo di SQL dinamico
description: Suggerimenti per le soluzioni di sviluppo che usano SQL dinamico nel pool SQL sinapsi.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bce79b8e18b3ec6f1fd139af280086281bbdda98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213466"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>SQL dinamico nel pool SQL sinapsi

In questo articolo sono disponibili suggerimenti per le soluzioni di sviluppo che usano SQL dinamico nel pool SQL.

## <a name="dynamic-sql-example"></a>Esempio di SQL dinamico

Quando si sviluppa il codice dell'applicazione per il pool SQL, potrebbe essere necessario usare SQL dinamico per offrire soluzioni flessibili, generiche e modulari. Il pool SQL non supporta i tipi di dati BLOB in questo momento.

Il mancato supporto dei tipi di dati BLOB potrebbe limitare le dimensioni delle stringhe poiché i tipi di dati BLOB includono i tipi varchar (max) e nvarchar (max).

Se sono stati usati questi tipi nel codice dell'applicazione per creare stringhe di grandi dimensioni, è necessario suddividere il codice in blocchi e usare invece l'istruzione EXEC.

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
