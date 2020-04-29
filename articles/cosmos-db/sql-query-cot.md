---
title: LETTIno in linguaggio Azure Cosmos DB query
description: Informazioni sul modo in cui la funzione di sistema SQL della cotangente (COT) in Azure Cosmos DB restituisce la cotangente trigonometrica dell'angolo specificato, in radianti, nell'espressione numerica specificata.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299488"
---
# <a name="cot-azure-cosmos-db"></a>LETTIno (Azure Cosmos DB)
 Restituisce la cotangente trigonometrica dell'angolo specificato, espresso in radianti, nell'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene calcolato l' `COT` oggetto dell'angolo specificato.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Questo è il set di risultati.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
