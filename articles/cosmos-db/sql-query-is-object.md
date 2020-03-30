---
title: IS_OBJECT nel linguaggio di query del database Cosmos di Azure
description: Informazioni sulla funzione del sistema SQL IS_OBJECT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303801"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 Restituisce un valore booleano che indica se il tipo di espressione specificata è un oggetto JSON.  
  
## <a name="syntax"></a>Sintassi
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*Expr*  
   È qualsiasi espressione.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente controlla gli oggetti di tipi JSON Boolean, Number, string, null, object, array e undefined utilizzando la `IS_OBJECT` funzione.  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Questo è il set di risultati.  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema beneficerà di un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di controllo dei tipi Azure Cosmos DBType checking functions Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
