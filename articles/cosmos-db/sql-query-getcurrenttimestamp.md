---
title: GetCurrentTimestamp nel linguaggio di query di Azure Cosmos DBGetCurrentTimestamp in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL GetCurrentTimestamp in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351002"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Restituisce il numero di millisecondi trascorsi dalle 00:00:00 di giovedì 1 gennaio 1970. 
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un valore numerico, ovvero il numero corrente di millisecondi trascorsi dall'epoca Unix, ovvero il numero di millisecondi trascorsi dalle 00:00:00 di giovedì 1 gennaio 1970.

## <a name="remarks"></a>Osservazioni

  GetCurrentTimestamp() è una funzione non deterministica.
  
  Il risultato restituito è UTC (Coordinated Universal Time).

## <a name="examples"></a>Esempi
  
  L'esempio seguente mostra come ottenere il timestamp corrente utilizzando la funzione incorporata GetCurrentTimestamp().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Di seguito è riportato un set di risultati di esempio.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
