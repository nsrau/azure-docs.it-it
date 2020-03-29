---
title: Funzioni matematiche nel linguaggio di query di Azure Cosmos DBMathematical functions in Azure Cosmos DB query language
description: Informazioni sulle funzioni matematiche in Azure Cosmos DB per eseguire un calcolo, in base ai valori di input forniti come argomenti, e restituire un valore numerico.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873268"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funzioni matematiche (Azure Cosmos DB)  

Le funzioni matematiche eseguono un calcolo basato su valori di input passati come argomenti e restituiscono un valore numerico.

È possibile eseguire query come nell'esempio seguente:You can run queries like the following example:

```sql
    SELECT VALUE ABS(-4)
```

Il risultato è:

```json
    [4]
```

## <a name="functions"></a>Funzioni

Le seguenti funzioni matematiche predefinite supportate eseguono un calcolo, in genere basato su argomenti di input, e restituiscono un'espressione numerica.
  
||||  
|-|-|-|  
|[abs](sql-query-abs.md)|[Acos](sql-query-acos.md)|[Asin](sql-query-asin.md)|  
|[Atan](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[Gradi](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[Pavimento](sql-query-floor.md)|[Registro](sql-query-log.md)|  
|[LOG10 (informazioni in questo stati in comsi](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[Radianti](sql-query-radians.md)|[RAND](sql-query-rand.md)|[Rotondo](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[Peccato](sql-query-sin.md)|[Sqrt](sql-query-sqrt.md)|
|[Piazza](sql-query-square.md)|[Tan](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Tutte le funzioni matematiche, ad eccezione di RAND, sono deterministiche, ovvero restituiscono sempre lo stesso risultato ogni volta che vengono chiamate con un set specifico di valori di input.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
- [Funzioni definite dall'utente](sql-query-udfs.md)
- [Aggregazioni](sql-query-aggregates.md)
