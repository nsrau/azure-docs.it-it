---
title: ARRAY_CONCAT nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL Concat dell'array in Azure Cosmos DB restituisce una matrice risultante dalla concatenazione di due o più valori di matrice
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78295879"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Restituisce una matrice che rappresenta il risultato della concatenazione di due o più valori della matrice.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*arr_expr*  
   Espressione di matrice da concatenare agli altri valori. La `ARRAY_CONCAT` funzione richiede almeno due *arr_expr* argomenti.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di matrice.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente illustra come concatenare due matrici.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Questo è il set di risultati.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di matrice Azure Cosmos DB](sql-query-array-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
