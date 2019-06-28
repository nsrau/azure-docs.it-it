---
title: Query con parametri in Azure Cosmos DB
description: Informazioni sulle query SQL con parametrizzata
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342832"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Query con parametri in Azure Cosmos DB

COSMOS DB supporta le query con parametri espressi dalla consueta notazione @. SQL con parametri fornisce alla gestione affidabile ed escape dell'input dell'utente e impedisce l'esposizione accidentale dei dati mediante attacchi SQL injection.

## <a name="examples"></a>Esempi

Ad esempio, è possibile scrivere una query che accetta `lastName` e `address.state` come parametri ed eseguirlo per diversi valori di `lastName` e `address.state` basate sull'input dell'utente.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

È quindi possibile inviare la richiesta a Cosmos DB come query con parametri JSON simile al seguente:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

L'esempio seguente imposta l'argomento principale con una query con parametri: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

I valori dei parametri possono essere qualsiasi valore JSON valido: stringhe, numeri, valori booleani, null, persino matrici o una stringa JSON nidificata. Poiché Cosmos DB è senza schema, i parametri non vengono convalidati rispetto a qualsiasi tipo.


## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dati del documento modello](modeling-data.md)
