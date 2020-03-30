---
title: GetCurrentDateTime nel linguaggio di query di Azure Cosmos DBGetCurrentDateTime in Azure Cosmos DB query language
description: Informazioni sulla funzione di sistema SQL GetCurrentDateTime in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303903"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Restituisce la data e l'ora UTC (Coordinated Universal Time) correnti come stringa ISO 8601.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce il valore stringa ISO 8601 di `YYYY-MM-DDThh:mm:ss.fffffffZ` data e ora UTC corrente nel formato in cui:
  
  |||
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 : gennaio, ecc.)|
  |GG|giorno del mese a due cifre (da 01 a 31)|
  |T|significantificatore per gli elementi di inizio tempo|
  |hh|ora a due cifre (da 00 a 23)|
  |MM|minuti a due cifre (da 00 a 59)|
  |ss|secondi a due cifre (da 00 a 59)|
  |File con estensione fffffff|secondi frazionari a sette cifre|
  |Z|Designatore UTC (Coordinated Universal Time)||
  
  Per ulteriori informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Osservazioni

  GetCurrentDateTime() è una funzione non deterministica. 
  
  Il risultato restituito è UTC.

  La precisione è di 7 cifre, con una precisione di 100 nanosecondi.

## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come ottenere l'ora della data UTC corrente utilizzando la funzione incorporata GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Di seguito è riportato un set di risultati di esempio.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
