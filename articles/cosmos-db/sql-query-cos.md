---
title: COS in Azure Cosmos DB linguaggio di query
description: Informazioni sul modo in cui il coseno (COS) funzione di sistema SQL in Azure Cosmos DB restituisce il coseno trigonometrico dell'angolo specificato, in radianti, nell'espressione specificata.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5f60b9f321b7d1ef75a1927310c788ff3ee3270
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100848"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
## <a name="examples"></a>Esempio
  
  Nell'esempio seguente viene calcolato l'oggetto `COS` dell'angolo specificato.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Questo è il set di risultati.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
