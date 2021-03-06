---
title: StartsWith nel linguaggio di query di Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL STARTSWITH in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7217a8416354de260f958bfb5b980a8fdc391618
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334923"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce un valore booleano che indica se la prima espressione stringa inizia con il secondo.  
  
## <a name="syntax"></a>Sintassi
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Espressione stringa.
  
*str_expr2*  
   Espressione stringa da confrontare con l'inizio di *str_expr1*.

*bool_expr* Valore facoltativo per ignorare la distinzione tra maiuscole e minuscole. Se impostato su true, STARTSWITH eseguirà una ricerca senza distinzione tra maiuscole e minuscole. Se non è specificato, questo valore è false.

## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
L'esempio seguente verifica se la stringa "abc" inizia con "b" e "A".  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 Questo è il set di risultati.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
