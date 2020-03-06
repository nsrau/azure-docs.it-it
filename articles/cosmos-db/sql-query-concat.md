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
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302611"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Restituisce la stringa risultante dalla concatenazione di due o più valori stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da concatenare agli altri valori. La funzione `CONCAT` richiede almeno due argomenti di *str_expr* .  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la stringa concatenata dei valori specificati.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Set di risultati:  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
