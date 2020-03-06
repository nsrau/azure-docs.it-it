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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
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
  
 Set di risultati:  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
