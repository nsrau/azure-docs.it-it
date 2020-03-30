---
title: LENGTH nel linguaggio di query di Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL LENGTH in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303716"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
 Restituisce il numero di caratteri dell'espressione stringa specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da valutare.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la lunghezza di una stringa.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Questo è il set di risultati.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
