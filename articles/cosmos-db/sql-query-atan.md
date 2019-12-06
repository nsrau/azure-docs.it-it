---
title: ATAN in linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL arcotangente (ATAN) in Azure Cosmos DB restituisce l'angolo, in radianti, la cui tangente corrisponde all'espressione numerica specificata.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fd398e343a3e617ceb9f49ca8f31ee776a7f073
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873455"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Restituisce l'angolo, espresso in radianti, la cui tangente è l'espressione numerica specificata. Detta anche arcotangente.  
  
## <a name="syntax"></a>Sintassi
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>esempi
  
  Nell'esempio seguente viene restituito il `ATAN` del valore specificato.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Questo è il set di risultati.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
