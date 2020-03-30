---
title: ASIN nel linguaggio di query del database Cosmos di AzureASIN in Azure Cosmos DB query language
description: Informazioni su come la funzione del sistema SQL Arcsine (ASIN) in Azure Cosmos DB restituisce l'angolo, in radianti, il cui seno è l'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302696"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Restituisce l'angolo, espresso in radianti, il cui seno è l'espressione numerica specificata. Detta anche arcoseno.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `ASIN` viene restituito il valore di -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Questo è il set di risultati.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
