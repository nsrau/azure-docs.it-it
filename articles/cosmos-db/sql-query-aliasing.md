---
title: Aliasing in Azure Cosmos DB
description: Informazioni sui valori di aliasing in Azure Cosmos DB query SQL
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002079"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing in Azure Cosmos DB

È possibile eseguire l'aliasing esplicito dei valori nelle query. Se una query ha due proprietà con lo stesso nome, usare l'aliasing per rinominare una o entrambe le proprietà in modo che siano ambiguità nel risultato previsto.

## <a name="examples"></a>Esempi

La parola chiave AS utilizzata per l'aliasing è facoltativa, come illustrato nell'esempio seguente quando si proietta il secondo `NameInfo`valore come:

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

- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clausola SELECT](sql-query-select.md)
- [Clausola FROM](sql-query-from.md)
