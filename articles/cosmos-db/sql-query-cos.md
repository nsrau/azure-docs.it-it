---
title: COS in Azure Cosmos DB linguaggio di query
description: Informazioni sul modo in cui il coseno (COS) funzione di sistema SQL in Azure Cosmos DB restituisce il coseno trigonometrico dell'angolo specificato, in radianti, nell'espressione specificata.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873404"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Restituisce il coseno trigonometrico dell'angolo specificato, espresso in radianti, nell'espressione specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>esempi
  
  Nell'esempio seguente viene calcolato il `COS` dell'angolo specificato.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Questo è il set di risultati.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
