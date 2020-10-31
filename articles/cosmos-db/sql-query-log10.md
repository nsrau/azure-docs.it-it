---
title: LOG10 in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL LOG10 in Azure Cosmos DB per restituire il logaritmo in base 10 dell'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 003479e4b9afaa3895bf18b7d2fc33979197d635
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075297"
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
  
## <a name="examples"></a>Esempio
  
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
