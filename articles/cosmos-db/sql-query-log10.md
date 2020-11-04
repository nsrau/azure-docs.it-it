---
title: LOG10 in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL LOG10 in Azure Cosmos DB per restituire il logaritmo in base 10 dell'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27f7d916c1a3f84f26674fea6b04597fd9e546bc
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338432"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
## <a name="remarks"></a>Commenti
  
  Le funzioni LOG10 e POWER sono inversamente correlate. Ad esempio, 10 ^ LOG10(n) = n. Questa funzione di sistema non utilizzerà l'indice.
  
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
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
