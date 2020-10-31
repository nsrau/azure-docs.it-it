---
title: TicksToDateTime in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL TicksToDateTime in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12d286fb5c8cf6d602d95da53ef8de376f9a49cf
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93093861"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Converte il valore dei cicli specificati in un valore DateTime.
  
## <a name="syntax"></a>Sintassi
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Argomenti

*Tick*  

Un valore numerico con segno, il numero corrente di cicli di 100 nanosecondi trascorsi dal periodo UNIX. In altre parole, è il numero di cicli di 100 nanosecondi trascorsi dal 00:00:00 giovedì, 1 gennaio 1970.

## <a name="return-types"></a>Tipi restituiti

Restituisce il valore della stringa ISO 8601 di data e ora UTC nel formato in `YYYY-MM-DDThh:mm:ss.fffffffZ` cui:
  
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

## <a name="remarks"></a>Commenti

TicksToDateTime restituirà `undefined` se il valore dei cicli specificato non è valido.

## <a name="examples"></a>Esempio
  
Nell'esempio seguente i cicli vengono convertiti in un valore DateTime:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
