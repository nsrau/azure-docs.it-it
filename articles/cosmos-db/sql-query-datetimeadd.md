---
title: DateTimeAdd in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL DateTimeAdd in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 0b2741a3d2b013ba7bd97038eb4ba4512f36af11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86262065"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)

Restituisce un valore stringa DateTime risultante dall'aggiunta di un valore numerico specificato (come intero con segno) a una stringa DateTime specificata  
  
## <a name="syntax"></a>Sintassi
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Argomenti
  
*DateTimePart*  
   Parte di data in cui DateTimeAdd aggiunge un numero intero. Questa tabella elenca tutti gli argomenti DateTimePart validi:

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

*numeric_expr*  
   Valore intero con segno che verrà aggiunto al DateTimePart del valore DateTime specificato.

*DateTime*  
   Valore stringa ISO 8601 di data e ora UTC nel formato in `YYYY-MM-DDThh:mm:ss.fffffffZ` cui:
  
  |Formato|Descrizione|
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 = gennaio e così via)|
  |GG|giorno del mese a due cifre (da 01 a 31)|
  |T|significato per l'inizio degli elementi Time|
  |hh|ora a due cifre (da 00 a 23)|
  |MM|minuti a due cifre (da 00 a 59)|
  |ss|secondi a due cifre (da 00 a 59)|
  |. fffffff|secondi frazionari a sette cifre|
  |Z|Indicatore UTC (Coordinated Universal Time)||
  
  Per ulteriori informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore stringa ISO 8601 di data e ora UTC nel formato in `YYYY-MM-DDThh:mm:ss.fffffffZ` cui:
  
  |Formato|Descrizione|
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 = gennaio e così via)|
  |GG|giorno del mese a due cifre (da 01 a 31)|
  |T|significato per l'inizio degli elementi Time|
  |hh|ora a due cifre (da 00 a 23)|
  |MM|minuti a due cifre (da 00 a 59)|
  |ss|secondi a due cifre (da 00 a 59)|
  |. fffffff|secondi frazionari a sette cifre|
  |Z|Indicatore UTC (Coordinated Universal Time)||

## <a name="remarks"></a>Commenti

DateTimeAdd restituirà `undefined` per i motivi seguenti:

- Il valore DateTimePart specificato non è valido
- Il numeric_expr specificato non è un numero intero valido
- Il valore DateTime nell'argomento o nel risultato non è un valore DateTime ISO 8601 valido.

## <a name="examples"></a>Esempi
  
Nell'esempio seguente viene aggiunto un mese a DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

Nell'esempio seguente vengono sottratte 2 ore da DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
