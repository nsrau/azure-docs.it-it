---
title: Utilizzo di SQL dinamico
description: Suggerimenti per le soluzioni di sviluppo che usano SQL dinamico per i pool SQL dedicati in Azure sinapsi Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 52bc7bdc63f754d52bf4a69097c1dd309a6dc3ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462785"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>SQL dinamico per i pool SQL dedicati in Azure sinapsi Analytics

In questo articolo sono disponibili suggerimenti per le soluzioni di sviluppo che usano SQL dinamico in pool SQL dedicati.

## <a name="dynamic-sql-example"></a>Esempio di SQL dinamico

Quando si sviluppa il codice dell'applicazione per i pool SQL dedicati, potrebbe essere necessario usare SQL dinamico per offrire soluzioni flessibili, generiche e modulari. Al momento i pool SQL dedicati non supportano i tipi di dati BLOB.

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
