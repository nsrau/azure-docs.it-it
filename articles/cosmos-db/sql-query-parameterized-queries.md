---
title: Query con parametri in Azure Cosmos DB
description: Informazioni sul modo in cui le query con parametri SQL forniscono una gestione efficace e l'escape dell'input dell'utente e impediscono l'esposizione accidentale dei dati tramite SQL injection.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: f66bc89ef56dd0c2291903d531a4637210abd8df
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496985"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Query con parametri in Azure Cosmos DB

Azure Cosmos DB supporta le query con parametri espressi dalla nota @ Notation. SQL con parametri fornisce una gestione efficace e l'escape dell'input dell'utente e impedisce l'esposizione accidentale dei dati tramite SQL injection.

## <a name="examples"></a>Esempi

Ad esempio, è possibile scrivere una query che accetta `lastName` e `address.state` come parametri ed eseguirla per diversi valori di `lastName` e `address.state` in base all'input dell'utente.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

È quindi possibile inviare questa richiesta a Azure Cosmos DB come query JSON con parametri come la seguente:

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

I valori dei parametri possono essere qualsiasi JSON valido: stringhe, numeri, valori booleani, valori null, persino matrici o JSON annidato. Poiché Azure Cosmos DB è senza schema, i parametri non vengono convalidati rispetto a qualsiasi tipo.

Ecco alcuni esempi per le query con parametri in ogni SDK Azure Cosmos DB:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modellare i dati del documento](modeling-data.md)
