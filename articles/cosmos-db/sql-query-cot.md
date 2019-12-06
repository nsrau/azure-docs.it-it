---
title: LETTIno in linguaggio Azure Cosmos DB query
description: Informazioni sul modo in cui la funzione di sistema SQL della cotangente (COT) in Azure Cosmos DB restituisce la cotangente trigonometrica dell'angolo specificato, in radianti, nell'espressione numerica specificata.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871500"
---
# <a name="cot-azure-cosmos-db"></a>LETTIno (Azure Cosmos DB)
 Restituisce la cotangente trigonometrica dell'angolo specificato, espresso in radianti, nell'espressione numerica specificata.  
  
## <a name="syntax"></a>Sintassi
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>esempi
  
  Nell'esempio seguente viene calcolato il `COT` dell'angolo specificato.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Questo è il set di risultati.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
