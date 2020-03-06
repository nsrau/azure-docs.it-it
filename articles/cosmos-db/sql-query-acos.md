---
title: ARccOS in linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL ARCCOS (arccosice) in Azure Cosmos DB restituisce l'angolo, in radianti, il cui coseno corrisponde all'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300962"
---
# <a name="acos-azure-cosmos-db"></a>ARCCOS (Azure Cosmos DB)
 Restituisce l'angolo, espresso in radianti, il cui coseno corrisponde all'espressione numerica specificata. Denominato anche arcocoseno.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   Espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene restituito il `ACOS` di-1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Set di risultati:  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
