---
title: GetCurrentDateTime in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentDateTime in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303903"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Restituisce la data e l'ora UTC (Coordinated Universal Time) correnti come stringa ISO 8601.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Tipi restituiti
  
  Restituisce la data e l'ora UTC correnti del valore stringa ISO 8601 nel formato `YYYY-MM-DDThh:mm:ss.fffffffZ` dove:
  
  |||
  |-|-|
  |AAAA|anno a quattro cifre|
  |MM|mese a due cifre (01 = gennaio e così via)|
  |GG|giorno del mese a due cifre (da 01 a 31)|
  |Elemento|significato per l'inizio degli elementi Time|
  |hh|ora a due cifre (da 00 a 23)|
  |mm|minuti a due cifre (da 00 a 59)|
  |ss|secondi a due cifre (da 00 a 59)|
  |. fffffff|secondi frazionari a sette cifre|
  |Z|Indicatore UTC (Coordinated Universal Time)||
  
  Per ulteriori informazioni sul formato ISO 8601, vedere [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Note

  GetCurrentDateTime () è una funzione non deterministica. 
  
  Il risultato restituito è UTC.

  La precisione è 7 cifre, con un'accuratezza di 100 nanosecondi.

## <a name="examples"></a>Esempi
  
  Nell'esempio seguente viene illustrato come ottenere la data e ora UTC correnti utilizzando la funzione predefinita GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Di seguito è riportato un esempio di set di risultati.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione a Azure Cosmos DB](introduction.md)
