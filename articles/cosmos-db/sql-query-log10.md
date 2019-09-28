---
title: LOG10 in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL LOG10 in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349726"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Restituisce il logaritmo in base 10 dell'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expression*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="remarks"></a>Note
  
  Le funzioni LOG10 e POWER sono inversamente correlate tra loro. Ad esempio, 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il valore LOG10 della variabile specificata (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Questo è il set di risultati.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
