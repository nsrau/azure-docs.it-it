---
title: ARccOS in linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL ARCCOS (arccosice) in Azure Cosmos DB restituisce l'angolo, in radianti, il cui coseno corrisponde all'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ed5adaad6c0ee160fd55341e1d7c48fcf3e49a28
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332815"
---
# <a name="acos-azure-cosmos-db"></a>ARCCOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  Nell'esempio seguente viene restituito l'oggetto `ACOS` di-1.  
  
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
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
