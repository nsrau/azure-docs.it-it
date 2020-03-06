---
title: ATN2 in linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema ATN2 SQL in Azure Cosmos DB restituisce il valore principale dell'arcotangente di y/x, espresso in radianti
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302662"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Restituisce il valore principale dell'arcotangente di y/x, espresso in radianti.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   Espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  L'esempio seguente calcola l'arcotangente per i componenti x e y specificati.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Set di risultati:  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
