---
title: CONCAt nel linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL CONCAt in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351254"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da concatenare agli altri valori. La funzione `CONCAT` richiede almeno due argomenti *str_expr* .  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la stringa concatenata dei valori specificati.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Questo è il set di risultati.  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
