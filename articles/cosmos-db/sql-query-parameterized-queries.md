---
title: Query con parametri in Azure Cosmos DBParameterized queries in Azure Cosmos DB
description: Informazioni su come le query con parametri SQL forniscono una gestione ed un'escapazione affidabili dell'input dell'utente e impediscono l'esposizione accidentale dei dati tramite SQL injection.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870820"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Query con parametri in Azure Cosmos DBParameterized queries in Azure Cosmos DB

Cosmos DB supporta le query con parametri espressi dalla notazione familiare. SQL con parametri fornisce una gestione ed un'escaping affidabili dell'input dell'utente e impedisce l'esposizione accidentale dei dati tramite SQL injection.

## <a name="examples"></a>Esempi

Ad esempio, è possibile scrivere `lastName` `address.state` una query che accetta e `lastName` come `address.state` parametri ed eseguirla per vari valori di e in base all'input dell'utente.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

È quindi possibile inviare questa richiesta a Cosmos DB come query JSON con parametri come segue:You can then send this request to Cosmos DB as a parameterized JSON query like the following:

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

I valori dei parametri possono essere qualsiasi JSON valido: stringhe, numeri, valori booleani, null, matrici uniformi o JSON annidato. Poiché Cosmos DB è privo di schema, i parametri non vengono convalidati in base a qualsiasi tipo.


## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modellare i dati del documento](modeling-data.md)
