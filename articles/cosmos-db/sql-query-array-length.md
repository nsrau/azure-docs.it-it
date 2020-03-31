---
title: ARRAY_LENGTH nel linguaggio di query del database Cosmos di AzureUse in Azure Cosmos DB query language
description: Informazioni su come la funzione del sistema SQL di lunghezza dell'array in Azure Cosmos DB restituisce il numero di elementi dell'espressione di matrice specificataLearn about how the Array length SQL system function in Azure Cosmos DB returns the number of elements of the specified array expression
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303988"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 Restituisce il numero di elementi dell'espressione di matrice specificato.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*arr_expr*  
   È un'espressione di matrice.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio riportato di seguito viene `ARRAY_LENGTH`illustrato come ottenere la lunghezza di una matrice utilizzando .  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Questo è il set di risultati.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di matrice Azure Cosmos DBArray functions Azure Cosmos DB](sql-query-array-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
