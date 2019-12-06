---
title: ATN2 in linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema ATN2 SQL in Azure Cosmos DB restituisce il valore principale dell'arcotangente di y/x, espresso in radianti
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11321ef9d7b81af279b04e0e435b19c645cf3bcf
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871653"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Restituisce il valore principale dell'arcotangente di y/x, espresso in radianti.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>esempi
  
  L'esempio seguente calcola l'arcotangente per i componenti x e y specificati.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Questo è il set di risultati.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
