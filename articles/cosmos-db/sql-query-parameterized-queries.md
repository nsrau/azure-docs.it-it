---
title: Query con parametri in Azure Cosmos DB
description: Informazioni sul modo in cui le query con parametri SQL forniscono una gestione efficace e l'escape dell'input dell'utente e impediscono l'esposizione accidentale dei dati tramite SQL injection.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74870820"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Query con parametri in Azure Cosmos DB

Cosmos DB supporta le query con parametri espressi dalla nota @ Notation. SQL con parametri fornisce una gestione efficace e l'escape dell'input dell'utente e impedisce l'esposizione accidentale dei dati tramite SQL injection.

## <a name="examples"></a>Esempi

Ad esempio, è possibile scrivere una query che accetta `lastName` e `address.state` come parametri ed eseguirla per diversi valori di `lastName` e `address.state` in base all'input dell'utente.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

È quindi possibile inviare questa richiesta a Cosmos DB come query JSON con parametri come la seguente:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Nell'esempio seguente viene impostato l'argomento TOP con una query con parametri: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

I valori dei parametri possono essere qualsiasi JSON valido: stringhe, numeri, valori booleani, valori null, persino matrici o JSON annidato. Poiché Cosmos DB è senza schema, i parametri non vengono convalidati rispetto a qualsiasi tipo.


## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modellare i dati del documento](modeling-data.md)
