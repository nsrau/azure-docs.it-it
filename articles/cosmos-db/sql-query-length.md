---
title: LUNGHEZZA nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla lunghezza della funzione di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303716"
---
# <a name="length-azure-cosmos-db"></a>Lunghezza (Azure Cosmos DB)
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

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
