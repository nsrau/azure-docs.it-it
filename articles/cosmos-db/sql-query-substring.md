---
title: SUBSTRING nel linguaggio di query di Azure Cosmos DBSUBSTRING in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL SUBSTRING in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303699"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Restituisce parte di un'espressione stringa a partire dalla posizione in base al carattere zero specificata e continua fino alla lunghezza specificata o alla fine della stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa.
  
*num_expr1*  
   È un'espressione numerica per indicare il carattere iniziale. Il valore 0 è il primo carattere di *str_expr*.
  
*num_expr2*  
   È un'espressione numerica per indicare il numero massimo di caratteri di *str_expr* da restituire. Un valore pari o inferiore a 0 restituisce una stringa vuota.

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

Questa funzione di sistema beneficerà di un `0`indice di [intervallo](index-policy.md#includeexclude-strategy) se la posizione iniziale è .

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
