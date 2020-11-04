---
title: GetCurrentTicks in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 610c545bf25822d27e0a641a1b2631f899502420
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340159"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Restituisce il numero di cicli di 100-nanosecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore numerico con segno, il numero corrente di cicli di 100-nanosecondi trascorsi dal periodo UNIX. In altre parole, GetCurrentTicks restituisce il numero di cicli di 100 nanosecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="remarks"></a>Commenti

GetCurrentTicks () è una funzione non deterministica. Il risultato restituito è UTC (Coordinated Universal Time).

Questa funzione di sistema non utilizzerà l'indice.

## <a name="examples"></a>Esempi

Di seguito è riportato un esempio che restituisce l'ora corrente, misurata in cicli:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
