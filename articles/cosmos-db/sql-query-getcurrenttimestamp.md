---
title: GetCurrentTimestamp in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentTimestamp in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1e0a8d69edab0c01005268ee49c23625236f03a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606920"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)

 Restituisce il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipi restituiti
  
Restituisce un valore numerico con segno, il numero corrente di millisecondi trascorsi dal periodo UNIX, ovvero il numero di millisecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="remarks"></a>Commenti

GetCurrentTimestamp () è una funzione non deterministica. Il risultato restituito è UTC (Coordinated Universal Time).

Questa funzione di sistema non utilizzerà l'indice.

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
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
