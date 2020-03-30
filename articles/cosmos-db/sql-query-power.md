---
title: POWER nel linguaggio di query del database Cosmos di AzurePOWER in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL POWER in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349643"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 Restituisce il valore dell'espressione specificata alla potenza specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr1*  
   È un'espressione numerica.  
  
*numeric_expr2*  
   È il potere a cui alzare *numeric_expr1*.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrata la generazione di un numero con potenza 3 (il cubo del numero).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Questo è il set di risultati.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
