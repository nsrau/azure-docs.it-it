---
title: ARRAY_SLICE nel linguaggio di query del database Cosmos di AzureUse in Azure Cosmos DB query language
description: Informazioni su come la funzione del sistema SQL della sezione Array in Azure Cosmos DB restituisce parte di un'espressione di matriceLearn about how the Array slice SQL system function in Azure Cosmos DB returns part of an array expression
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303325"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Restituisce parte di un'espressione di matrice.
  
## <a name="syntax"></a>Sintassi
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argomenti
  
*arr_expr*  
   È qualsiasi espressione di matrice.  
  
*num_expr*  
   Indice numerico in base zero in corrispondenza del quale iniziare la matrice. È possibile usare valori negativi per specificare l'indice iniziale rispetto all'ultimo elemento della matrice, ad esempio -1 fa riferimento all'ultimo elemento nella matrice.  

*num_expr* Espressione numerica facoltativa che imposta il numero massimo di elementi nella matrice risultante.    

## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione di matrice.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come ottenere `ARRAY_SLICE`sezioni diverse di una matrice utilizzando .  
  
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

- [Funzioni di matrice Azure Cosmos DBArray functions Azure Cosmos DB](sql-query-array-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
