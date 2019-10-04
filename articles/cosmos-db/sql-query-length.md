---
title: LUNGHEZZA nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla lunghezza della funzione di sistema SQL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349763"
---
# <a name="length-azure-cosmos-db"></a>Lunghezza (Azure Cosmos DB)
 Restituisce il numero di caratteri dell'espressione stringa specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da valutare.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la lunghezza di una stringa.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Questo è il set di risultati.  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
