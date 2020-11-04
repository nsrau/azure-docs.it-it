---
title: ARRAY_CONTAINS nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la matrice contiene la funzione di sistema SQL in Azure Cosmos DB restituisce un valore booleano che indica se la matrice contiene il valore specificato.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff88d66ee6d1ceee4f1cf4e7b6501ab323dbc79e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332662"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Restituisce un valore booleano che indica se la matrice contiene il valore specificato. È possibile cercare una corrispondenza parziale o completa di un oggetto usando un'espressione booleana all'interno del comando. 

## <a name="syntax"></a>Sintassi
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argomenti
  
*arr_expr*  
   Espressione di matrice in cui eseguire la ricerca.  
  
*expr*  
   Espressione da trovare.  

*bool_expr*  
   Espressione booleana. Se restituisce ' true ' e il valore di ricerca specificato è un oggetto, il comando controlla la presenza di una corrispondenza parziale (l'oggetto di ricerca è un subset di uno degli oggetti). Se restituisce ' false ', il comando controlla la presenza di una corrispondenza completa di tutti gli oggetti all'interno della matrice. Se non è specificato, il valore predefinito è false. 
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un valore booleano.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come verificare l'appartenenza a una matrice utilizzando `ARRAY_CONTAINS` .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Questo è il set di risultati.  
  
```json
[{"b1": true, "b2": false}]  
```  

L'esempio seguente illustra come verificare la corrispondenza di un JSON in una matrice usando ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Questo è il set di risultati.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di matrice Azure Cosmos DB](sql-query-array-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
