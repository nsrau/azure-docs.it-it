---
title: Query con parametri in Azure Cosmos DB
description: Informazioni sulle query con parametri SQL
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 45c1344c32e35f60f35ba8ed105e912d92574cce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003600"
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
- [Dati del documento modello](modeling-data.md)
