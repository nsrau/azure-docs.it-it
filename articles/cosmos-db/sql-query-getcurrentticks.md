---
title: GetCurrentTicks in linguaggio di query Azure Cosmos DB
description: Informazioni sulla funzione di sistema SQL GetCurrentTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6b3cd5ab5849c33172e4a629c79fb792b82f1255
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227372"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

Restituisce la data e l'ora correnti, misurate in cicli.
  
## <a name="syntax"></a>Sintassi
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipi restituiti

Restituisce un valore intero positivo.

## <a name="remarks"></a>Osservazioni

Questa funzione di sistema non utilizzerà l'indice.

## <a name="examples"></a>Esempi

Di seguito è riportato un esempio che restituisce l'ora corrente, misurata in cicli:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- [Funzioni di data e ora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funzioni di sistema in Azure Cosmos DB](sql-query-system-functions.md)
- [Introduzione ad Azure Cosmos DB](introduction.md)
