---
title: StringEquals nel linguaggio di query Azure Cosmos DB
description: Informazioni su come la funzione di sistema SQL StringEquals in Azure Cosmos DB restituisce un valore booleano che indica se la prima espressione stringa corrisponde alla seconda
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47d899534de535b5cd6a7c3fb2df78cdadbe11f2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338051"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce un valore booleano che indica se la prima espressione stringa corrisponde alla seconda.  
  
## <a name="syntax"></a>Sintassi
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Prima espressione stringa da confrontare.  
  
*str_expr2*  
   Seconda espressione stringa da confrontare.  

*bool_expr* Valore facoltativo per ignorare la distinzione tra maiuscole e minuscole. Se impostato su true, StringEquals eseguirà una ricerca senza distinzione tra maiuscole e minuscole. Se non è specificato, questo valore è false.
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente si verifica se "abc" corrisponde ad "ab" e se "abc" corrisponde ad "A".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Questo è il set di risultati.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
