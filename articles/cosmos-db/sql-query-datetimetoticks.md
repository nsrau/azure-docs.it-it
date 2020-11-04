---
title: DateTimeToTicks in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL DateTimeToTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336334"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Converte il valore DateTime specificato in cicli. Un singolo segno di selezione rappresenta 100 nanosecondi o 1 10-milionesimo di secondo. 

## <a name="syntax"></a>Sintassi
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argomenti
  
*DateTime*  
   Valore stringa ISO 8601 di data e ora UTC nel formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore numerico con segno, il numero corrente di cicli di 100-nanosecondi trascorsi dal periodo UNIX. In altre parole, DateTimeToTicks restituisce il numero di cicli di 100-nanosecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="remarks"></a>Commenti

Il valore di DateTimeDateTimeToTicks restituirà `undefined` se DateTime non è un valore DateTime ISO 8601 valido

Questa funzione di sistema non utilizzerà l'indice.

## <a name="examples"></a>Esempi

Di seguito è riportato un esempio che restituisce il numero di cicli:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Di seguito è riportato un esempio che restituisce il numero di cicli senza specificare il numero di secondi frazionari:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
