---
title: LOG10 in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL LOG10 in Azure Cosmos DB per restituire il logaritmo in base 10 dell'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302492"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Restituisce il logaritmo in base 10 dell'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expression*  
   Espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="remarks"></a>Note
  
  Le funzioni LOG10 e POWER sono inversamente correlate. Ad esempio: 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene dichiarata una variabile e restituito il valore LOG10 della variabile specificata (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Set di risultati:  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
