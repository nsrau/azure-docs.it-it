---
title: ATAN nel linguaggio di query del database Cosmos di Azure
description: Informazioni su come la funzione del sistema SQL Arctangent (ATAN ) in Azure Cosmos DB restituisce l'angolo, in radianti, la cui tangente è l'espressione numerica specificata
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
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
   È un'espressione numerica.  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un'espressione numerica.  
  
## <a name="examples"></a>Esempi
  
  Nell'esempio seguente `ATAN` viene restituito l'oggetto del valore specificato.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Questo è il set di risultati.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni matematiche Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
