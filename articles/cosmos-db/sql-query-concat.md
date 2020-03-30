---
title: CONCAT nel linguaggio di query del database Cosmos di AzureCONCAT in Azure Cosmos DB query language
description: Informazioni su come la funzione del sistema SQL CONCAT in Azure Cosmos DB restituisce una stringa che è il risultato della concatenazione di due o più valori stringaLearn about how the CONCAT SQL system function in Azure Cosmos DB returns a string that is the result of concatenating two or more string values
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302611"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Restituisce una stringa che rappresenta il risultato della concatenazione di due o più valori di stringa.  
  
## <a name="syntax"></a>Sintassi
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È un'espressione stringa da concatenare agli altri valori. La `CONCAT` funzione richiede almeno due *argomenti str_expr.*  
  
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

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
