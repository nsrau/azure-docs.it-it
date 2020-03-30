---
title: IS_ARRAY nel linguaggio di query del database Cosmos di Azure
description: Informazioni sulla funzione del sistema SQL IS_ARRAY in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5867850db6eb3d6552bc129cca3708ef7747072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303886"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY (Azure Cosmos DB)
 Restituisce un valore booleano che indica se il tipo di espressione specificata è una matrice.  
  
## <a name="syntax"></a>Sintassi
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*Expr*  
   È qualsiasi espressione.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente controlla gli oggetti di tipi JSON Boolean, Number, string, null, object, array e undefined utilizzando la `IS_ARRAY` funzione.  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Questo è il set di risultati.  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema beneficerà di un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di controllo dei tipi Azure Cosmos DBType checking functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
