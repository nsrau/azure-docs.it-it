---
title: ARRAY_SLICE nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL Slice della matrice in Azure Cosmos DB restituisce parte di un'espressione di matrice
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c66e9ee2ff4b1c279e0fcc4e735be583cf55a2c8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089356"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce parte di un'espressione di matrice.
  
## <a name="syntax"></a>Sintassi
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*arr_expr*  
   Qualsiasi espressione di matrice.  
  
*num_expr*  
   Indice numerico in base zero in corrispondenza del quale iniziare la matrice. È possibile usare valori negativi per specificare l'indice iniziale rispetto all'ultimo elemento della matrice, ad esempio -1 fa riferimento all'ultimo elemento nella matrice.  

*num_expr* Espressione numerica facoltativa che imposta il numero massimo di elementi nella matrice risultante.    

## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di matrice.  
  
## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene illustrato come ottenere sezioni diverse di una matrice utilizzando `ARRAY_SLICE` .  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Questo è il set di risultati.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di matrice Azure Cosmos DB](sql-query-array-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
