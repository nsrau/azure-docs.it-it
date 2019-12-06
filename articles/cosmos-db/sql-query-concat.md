---
title: CONCAt nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema di CONCAt SQL in Azure Cosmos DB restituisce una stringa risultante dalla concatenazione di due o più valori stringa
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adb8564d4addaaa3f4a383b32941549aed3d53bd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871551"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   Espressione stringa da concatenare agli altri valori. La funzione `CONCAT` richiede almeno due argomenti di *str_expr* .  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>esempi
  
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
