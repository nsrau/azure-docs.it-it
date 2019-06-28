---
title: Aliasing in Azure Cosmos DB
description: Informazioni sui valori di aliasing nelle query SQL di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342959"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing in Azure Cosmos DB

È possibile in modo esplicito alias valori nelle query. Se una query avesse due proprietà con lo stesso nome, utilizzare l'aliasing per rinominare una o entrambe le proprietà in modo che si sta evitare ambiguità nel risultato proiettato.

## <a name="examples"></a>Esempi

La parola chiave utilizzata per l'alias è facoltativa, come illustrato nell'esempio seguente durante la proiezione del secondo valore come `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Passaggi successivi

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Clausola SELECT](sql-query-select.md)
- [Clausola FROM](sql-query-from.md)
