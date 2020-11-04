---
title: DateTimeDiff in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL DateTimeDiff in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 09b801c48bf2998a2d8926009cae76287c1ac9b6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342267"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Restituisce il conteggio (come valore intero con segno) dei limiti di DateTimePart specificati attraversati tra il *StartDate* e l' *EndDate* specificati.
  
## <a name="syntax"></a>Sintassi
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*StartDate*  
    Valore stringa ISO 8601 di data e ora UTC nel formato in `YYYY-MM-DDThh:mm:ss.fffffffZ` cui:
  
  |Formato|Descrizione|
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 = gennaio e così via)|
  |GG|giorno del mese a due cifre (da 01 a 31)|
  |T|significato per l'inizio degli elementi Time|
  |hh|ora a due cifre (da 00 a 23)|
  |mm|minuti a due cifre (da 00 a 59)|
  |ss|secondi a due cifre (da 00 a 59)|
  |. fffffff|secondi frazionari a sette cifre|
  |Z|Indicatore UTC (Coordinated Universal Time)||
  
  Per ulteriori informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

*EndDate*  
   Valore stringa ISO 8601 di data e ora UTC nel formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore intero con segno.

## <a name="remarks"></a>Commenti

DateTimeDiff restituirà `undefined` per i motivi seguenti:

- Il valore DateTimePart specificato non è valido
- StartDate o EndDate non è un valore DateTime ISO 8601 valido

DateTimeDiff restituirà sempre un valore intero con segno ed è una misurazione del numero di limiti DateTimePart superati, non di misura dell'intervallo di tempo.

## <a name="examples"></a>Esempi
  
Nell'esempio seguente viene calcolato il numero di limiti di giorno incrociato tra `2020-01-01T01:02:03.1234527Z` e `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

Nell'esempio seguente viene calcolato il numero di limiti di anno incrociato tra `2028-01-01T01:02:03.1234527Z` e `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

Nell'esempio seguente viene calcolato il numero di limiti di ora incrociati tra `2020-01-01T01:00:00.1234527Z` e `2020-01-01T01:59:59.1234567Z` . Anche se questi valori DateTime hanno una distanza superiore a 0,99 ore, `DateTimeDiff` restituisce 0 perché non sono stati superati i limiti di ora.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
