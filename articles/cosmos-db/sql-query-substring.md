---
title: Sottostringa nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla sottostringa della funzione di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 17888ccd8fc51ed96f7fc92a0f9275d2c8cb56f8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340831"
---
# <a name="substring-azure-cosmos-db"></a>Sottostringa (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce parte di un'espressione stringa a partire dalla posizione in base al carattere zero specificata e continua fino alla lunghezza specificata o alla fine della stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa.
  
*num_expr1*  
   Espressione numerica che indica il carattere iniziale. Il valore 0 è il primo carattere di *str_expr*.
  
*num_expr2*  
   Espressione numerica che indica il numero massimo di caratteri di *str_expr* da restituire. Il valore 0 o less restituisce una stringa vuota.

## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la sottostringa di "abc" a partire da 1 e per una lunghezza di 1 carattere.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Questo è il set di risultati.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema trarrà vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy) se la posizione iniziale è `0` .

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
