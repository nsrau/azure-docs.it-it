---
title: ACOS nel linguaggio di query di Azure Cosmos DBACOS in Azure Cosmos DB query language
description: Informazioni su come la funzione del sistema SQL ACOS (arccosice) in Azure Cosmos DB restituisce l'angolo, in radianti, il cui coseno è l'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300962"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Restituisce l'angolo, espresso in radianti, il cui coseno corrisponde all'espressione numerica specificata. Denominato anche arcocoseno.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `ACOS` viene restituito il valore di -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Questo è il set di risultati.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
