---
title: RAND nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL RAND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff098da778221868b0eddc17c426d2bf36eec0fe
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794337"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
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
