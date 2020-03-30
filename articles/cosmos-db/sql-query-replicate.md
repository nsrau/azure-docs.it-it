---
title: REPLICATE nel linguaggio di query del database Cosmos di AzureREPLICATE in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL REPLICATE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302186"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICATE (Azure Cosmos DB)
 Ripete un valore stringa il numero di volte specificato.
  
## <a name="syntax"></a>Sintassi
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa.
  
*num_expr*  
   È un'espressione numerica. Se *num_expr* è negativo o non finito, il risultato è indefinito.
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.
  
## <a name="remarks"></a>Osservazioni
  La lunghezza massima del risultato è di 10.000 caratteri, ad esempio (lunghezza (*str_expr*) e *num_expr*) <10.000.

## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene `REPLICATE` illustrato come utilizzare in una query.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Questo è il set di risultati.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
