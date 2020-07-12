---
title: DateTimeFromParts in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL DateTimeFromParts in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262062"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)

Restituisce un valore DateTime stringa costruito dai valori di input.
  
## <a name="syntax"></a>Sintassi
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argomenti
  
*numberYear* Valore intero per l'anno nel formato`YYYY`

*numberMonth*  
   Valore intero per il mese nel formato`MM`

*numberDay*  
   Valore intero per il giorno nel formato`DD`

*numberHour* (facoltativo) valore intero per l'ora nel formato`hh`

*numberMinute* (facoltativo) valore intero per il minuto nel formato`mm`

*numberSecond* (facoltativo) valore intero per il secondo nel formato`ss`

*numberOfFractionsOfSecond* (facoltativo) valore intero per il frazionario di un secondo nel formato`.fffffff`

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore stringa ISO 8601 di data e ora UTC nel formato in `YYYY-MM-DDThh:mm:ss.fffffffZ` cui:
  
  |Format|Descrizione|
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 = gennaio e così via)|
  |DD|giorno del mese a due cifre (da 01 a 31)|
  |T|significato per l'inizio degli elementi Time|
  |hh|ora a due cifre (da 00 a 23)|
  |MM|minuti a due cifre (da 00 a 59)|
  |ss|secondi a due cifre (da 00 a 59)|
  |. fffffff|secondi frazionari a sette cifre|
  |Z|Indicatore UTC (Coordinated Universal Time)||
  
 Per ulteriori informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Commenti

Se gli Integer specificati creeranno un valore DateTime non valido, DateTimeFromParts restituirà `undefined` .

Se non si specifica un argomento facoltativo, il valore sarà 0.

## <a name="examples"></a>Esempio

Di seguito è riportato un esempio che include solo gli argomenti obbligatori per costruire un valore DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Ecco un altro esempio che usa anche alcuni argomenti facoltativi per costruire un valore DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Ecco un altro esempio che usa anche tutti gli argomenti facoltativi per costruire un valore DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
