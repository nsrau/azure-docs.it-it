---
title: COS nel linguaggio di query del database Cosmos di AzureCOS in Azure Cosmos DB query language
description: Informazioni su come la funzione del sistema SQL Cosine (COS) in Azure Cosmos DB restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304022"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `COS` viene calcolato il valore dell'angolo specificato.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Questo è il set di risultati.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
