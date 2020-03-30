---
title: RAND nel linguaggio di query di Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL RAND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302220"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Restituisce un valore numerico generato casualmente da [0,1).
 
## <a name="syntax"></a>Sintassi
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipi restituiti

  Restituisce un'espressione numerica.

## <a name="remarks"></a>Osservazioni

  `RAND` è una funzione non deterministica. Le chiamate ripetitive `RAND` di non restituiscono gli stessi risultati.

## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene restituito un valore numerico generato casualmente.
  
```sql
SELECT RAND() AS rand 
```  
  
 Questo è il set di risultati.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
