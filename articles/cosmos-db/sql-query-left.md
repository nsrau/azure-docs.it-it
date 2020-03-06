---
title: LEFT in Azure Cosmos DB linguaggio di query
description: Informazioni sulla funzione di sistema SQL LASCIAta in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303750"
---
# <a name="left-azure-cosmos-db"></a>A sinistra (Azure Cosmos DB)
 Restituisce la parte sinistra di una stringa con il numero specificato di caratteri.  
  
## <a name="syntax"></a>Sintassi
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da cui estrarre i caratteri.  
  
*num_expr*  
   Espressione numerica che specifica il numero di caratteri.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la parte sinistra di "abc" per diversi valori di lunghezza.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Set di risultati:  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema trarrà vantaggio da un [indice di intervallo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
