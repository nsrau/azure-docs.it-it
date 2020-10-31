---
title: ARRAY_LENGTH nel linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL di lunghezza della matrice in Azure Cosmos DB restituisce il numero di elementi dell'espressione di matrice specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c27f2269171bd6121973ace4e535d375adf2de
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089424"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Restituisce il numero di elementi dell'espressione di matrice specificato.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*arr_expr*  
   Espressione di matrice.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene illustrato come ottenere la lunghezza di una matrice utilizzando `ARRAY_LENGTH` .  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Questo è il set di risultati.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di matrice Azure Cosmos DB](sql-query-array-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
