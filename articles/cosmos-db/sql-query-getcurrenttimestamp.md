---
title: GetCurrentTimestamp in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentTimestamp in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351002"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Restituisce il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970. 
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce un valore numerico, il numero corrente di millisecondi trascorsi dal periodo UNIX, ovvero il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="remarks"></a>Note

  GetCurrentTimestamp () è una funzione non deterministica.
  
  Il risultato restituito è UTC (Coordinated Universal Time).

## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come ottenere il timestamp corrente utilizzando la funzione predefinita GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Di seguito è riportato un esempio di set di risultati.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
