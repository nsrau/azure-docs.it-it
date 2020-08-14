---
title: DateTimeToTicks in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL DateTimeToTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a6fcff691ee5278ed0a9e1c132f8ecebbee9431c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227241"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)

Converte il valore DateTime specificato in cicli. Un singolo segno di selezione rappresenta 100 nanosecondi o 1 10-milionesimo di secondo.
  
## <a name="syntax"></a>Sintassi
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argomenti
  
*DateTime*  
   Valore stringa ISO 8601 di data e ora UTC nel formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore intero positivo.

## <a name="remarks"></a>Osservazioni

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
