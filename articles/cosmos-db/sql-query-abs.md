---
title: ABS nel linguaggio di query del database Cosmos di AzureABS in Azure Cosmos DB query language
description: Informazioni su come la funzione del sistema SQL Absolute(ABS) in Azure Cosmos DB restituisce il valore positivo dell'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6d173827f2695cc20fa208d390731acf0edb3848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301098"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure Cosmos DB)
 Restituisce il valore assoluto (positivo) dell'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente vengono illustrati `ABS` i risultati dell'utilizzo della funzione su tre numeri diversi.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Questo è il set di risultati.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema beneficerà di un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
