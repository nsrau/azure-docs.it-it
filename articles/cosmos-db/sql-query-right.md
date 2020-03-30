---
title: DESTRA nel linguaggio di query del database Cosmos di AzureRIGHT in Azure Cosmos DB query language
description: Informazioni sulla funzione del sistema SQL DESTRA in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302152"
---
# <a name="right-azure-cosmos-db"></a>DESTRA (Azure Cosmos DB)
 Restituisce la parte destra di una stringa con il numero specificato di caratteri.  
  
## <a name="syntax"></a>Sintassi
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*str_expr*  
   È l'espressione stringa da cui estrarre i caratteri.  
  
*num_expr*  
   È un'espressione numerica che specifica il numero di caratteri.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di stringa.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente restituisce la parte destra di "abc" per diversi valori di lunghezza.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Questo è il set di risultati.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni stringa Database Cosmos di AzureString functions Azure Cosmos DB](sql-query-string-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
