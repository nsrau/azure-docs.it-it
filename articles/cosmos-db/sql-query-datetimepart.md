---
title: DateTimePart in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL DateTimePart in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: dd88f8d6a0f5271da97d6f2f95eecb60bf94ed78
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095799"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Restituisce il valore dell'oggetto DateTimePart specificato tra il valore DateTime specificato.
  
## <a name="syntax"></a>Sintassi
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argomenti
  
*DateTimePart*  
   Parte della data in cui DateTimePart restituirà il valore. Questa tabella elenca tutti gli argomenti DateTimePart validi:

| DateTimePart | abbreviazioni        |
| ------------ | -------------------- |
| Year         | "Year", "aaaa", "yy" |
| Month        | "month", "mm", "m"   |
| Giorno          | "Day", "dd", "d"     |
| Ora         | "hour", "HH"         |
| Minuto       | "minute", "Mi", "n"  |
| Second       | "Second", "SS", "s"  |
| Millisecond  | "millisecondo", "MS"  |
| Microsecondi  | "microsecond", "MCS" |
| Nanosecondo   | "nanosecond", "NS"   |

*DateTime*  
   Valore stringa ISO 8601 di data e ora UTC nel formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore intero positivo.

## <a name="remarks"></a>Commenti

DateTimePart restituirà `undefined` per i motivi seguenti:

- Il valore DateTimePart specificato non è valido
- DateTime non è un valore DateTime ISO 8601 valido

Questa funzione di sistema non utilizzerà l'indice.

## <a name="examples"></a>Esempio

Di seguito è riportato un esempio in cui viene restituito il valore integer del mese:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Di seguito è riportato un esempio che restituisce il numero di microsecondi:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
