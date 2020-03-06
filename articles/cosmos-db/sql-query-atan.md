---
title: ATAN in linguaggio di query Azure Cosmos DB
description: Informazioni sul modo in cui la funzione di sistema SQL arcotangente (ATAN) in Azure Cosmos DB restituisce l'angolo, in radianti, la cui tangente corrisponde all'espressione numerica specificata.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302679"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Restituisce l'angolo, espresso in radianti, la cui tangente è l'espressione numerica specificata. (definito anche arcotangente).  
  
## <a name="syntax"></a>Sintassi
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argomenti
  
*numeric_expr*  
   Espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene restituito il `ATAN` del valore specificato.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Set di risultati:  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Note

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
