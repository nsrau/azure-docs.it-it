---
title: ASIN nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL arcoseno (ASIN) in Azure Cosmos DB restituisce l'angolo, in radianti, il cui seno è l'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302696"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Restituisce l'angolo, espresso in radianti, il cui seno è l'espressione numerica specificata. Il valore restituito viene definito anche arcoseno.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   Espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene restituito il `ASIN` di-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Set di risultati:  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
