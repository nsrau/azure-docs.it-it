---
title: GetCurrentDateTime in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentDateTime in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11222b2df1acee07647e02c5a4f1f65e3cf3e525
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098536"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Restituisce la data e l'ora UTC (Coordinated Universal Time) correnti come stringa ISO 8601.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Tipi restituiti
  
  Restituisce la data e l'ora UTC correnti del valore stringa ISO 8601 nel formato in `YYYY-MM-DDThh:mm:ss.fffffffZ` cui:
  
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

GetCurrentDateTime () è una funzione non deterministica. Il risultato restituito è UTC. La precisione è 7 cifre, con un'accuratezza di 100 nanosecondi.

Questa funzione di sistema non utilizzerà l'indice.

## <a name="examples"></a>Esempio
  
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
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
