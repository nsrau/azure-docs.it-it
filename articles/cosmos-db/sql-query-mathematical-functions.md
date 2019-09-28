---
title: Funzioni matematiche nel linguaggio di query Azure Cosmos DB
description: Informazioni sulle funzioni matematiche di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fe4a84db3f2cbcfc2d9841caf520404afec2a297
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349664"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funzioni matematiche (Azure Cosmos DB)  

Le funzioni matematiche eseguono un calcolo basato su valori di input passati come argomenti e restituiscono un valore numerico.

È possibile eseguire query come nell'esempio seguente:

```sql
    SELECT VALUE ABS(-4)
```

Il risultato è:

```json
    [4]
```

## <a name="functions"></a>Funzioni

Le funzioni matematiche predefinite supportate seguenti eseguono un calcolo, in genere in base agli argomenti di input, e restituiscono un'espressione numerica.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ROUND](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Tutte le funzioni matematiche, ad eccezione di RAND, sono funzioni deterministiche. Ciò significa che restituiscono gli stessi risultati ogni volta che vengono chiamate con un set specifico di valori di input.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregati](sql-query-aggregates.md)
