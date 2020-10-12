---
title: Inverti in Azure Cosmos DB linguaggio di query
description: Informazioni sull'inversione della funzione di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302169"
---
# <a name="reverse-azure-cosmos-db"></a>Inverti (Azure Cosmos DB)
 Restituisce un valore stringa in ordine inverso.  
  
## <a name="syntax"></a>Sintassi
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come utilizzare `REVERSE` in una query.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Questo è il set di risultati.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
