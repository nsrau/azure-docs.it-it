---
title: REPLICA in linguaggio Azure Cosmos DB query
description: Informazioni sulla replica della funzione di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aea29cfff6b3827cfb9169722e48120e3a5a3709
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794323"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICA (Azure Cosmos DB)
 Ripete un valore stringa il numero di volte specificato.
  
## <a name="syntax"></a>Sintassi
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa.
  
*num_expr*  
   È un'espressione numerica. Se *num_expr* è negativo o non finito, il risultato è indefinito.
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.
  
## <a name="remarks"></a>Commenti

  La lunghezza massima del risultato è di 10.000 caratteri, ad esempio (length (*str_expr*) *  *num_expr*) <= 10.000. Questa funzione di sistema non utilizzerà l'indice.

## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come utilizzare `REPLICATE` in una query.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Questo è il set di risultati.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
