---
title: CONTIENE nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema contiene SQL in Azure Cosmos DB restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302594"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINs (Azure Cosmos DB)
 Restituisce un valore booleano che indica se la prima espressione stringa contiene il secondo.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr1*  
   Stringa expression da cercare.  
  
*str_expr2*  
   Espressione stringa da trovare.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione booleana.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene controllato se "ABC" contiene "AB" e se "ABC" contiene "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Set di risultati:  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
