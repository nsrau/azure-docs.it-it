---
title: RIGHT in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL direttamente in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302152"
---
# <a name="right-azure-cosmos-db"></a>A destra (Azure Cosmos DB)
 Restituisce la parte destra di una stringa con il numero specificato di caratteri.  
  
## <a name="syntax"></a>Sintassi
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da cui estrarre i caratteri.  
  
*num_expr*  
   Espressione numerica che specifica il numero di caratteri.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la parte destra di "abc" per diversi valori di lunghezza.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Set di risultati:  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
