---
title: CONTAINS nel linguaggio di query del database Cosmos di AzureCONTAINS in Azure Cosmos DB query language
description: Informazioni su come la funzione di sistema CONTAINS SQL in Azure Cosmos DB restituisce un valore booleano che indica se la prima espressione stringa contiene la seconda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302594"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS (Azure Cosmos DB)
 Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Stringa expression da cercare.  
  
*str_expr2*  
   È l'espressione stringa da trovare.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene verificato se "abc" contiene "ab" e se "abc" contiene "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Questo è il set di risultati.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
