---
title: ARROTONDA nel linguaggio di query del database Cosmos di AzureROUND in Azure Cosmos DB query language
description: Informazioni sulla funzione del sistema SQL ROUND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302118"
---
# <a name="round-azure-cosmos-db"></a>ARROTONDA (Azure Cosmos DB)
 Restituisce un valore numerico, arrotondato al valore integer più vicino.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="remarks"></a>Osservazioni
  
  L'operazione di arrotondamento eseguita segue l'arrotondamento del punto medio da zero. Se l'input è un'espressione numerica che rientra esattamente tra due numeri interi, il risultato sarà il valore intero più vicino a zero.  
  
  |<numeric_expr>|Arrotondato|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente arrotonda numeri positivi e negativi al numero intero più prossimo.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Questo è il set di risultati.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema beneficerà di un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
