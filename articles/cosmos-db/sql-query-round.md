---
title: ROUND in linguaggio Azure Cosmos DB query
description: Informazioni sulla funzione di sistema SQL ROUND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e2447c713ff418f8d5ccc0e6bc64bf81f000524
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082726"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
## <a name="remarks"></a>Commenti
  
  L'operazione di arrotondamento eseguita segue l'arrotondamento a zero. Se l'input è un'espressione numerica che rientra esattamente tra due numeri interi, il risultato sarà il valore integer più vicino a zero. Questa funzione di sistema trae vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).
  
  |<numeric_expr>|Arrotondato|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0.5|1|
  |6,5000|7||
  
## <a name="examples"></a>Esempio
  
  L'esempio seguente arrotonda numeri positivi e negativi al numero intero più prossimo.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Questo è il set di risultati.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
