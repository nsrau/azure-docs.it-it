---
title: CONCAt nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema di CONCAt SQL in Azure Cosmos DB restituisce una stringa risultante dalla concatenazione di due o più valori stringa
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302611"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da concatenare agli altri valori. La `CONCAT` funzione richiede almeno due *str_expr* argomenti.  
  
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
  
## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa in Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
