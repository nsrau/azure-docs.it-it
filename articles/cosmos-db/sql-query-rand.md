---
title: RAND nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL RAND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341655"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce un valore numerico generato in modo casuale da [0, 1).
 
## <a name="syntax"></a>Sintassi
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipi restituiti

  Restituisce un'espressione numerica.

## <a name="remarks"></a>Commenti

  `RAND` è una funzione non deterministica. Le chiamate ripetute di non `RAND` restituiscono gli stessi risultati. Questa funzione di sistema non utilizzerà l'indice.


## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene restituito un valore numerico generato in modo casuale.
  
```sql
SELECT RAND() AS rand 
```  
  
 Questo è il set di risultati.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
