---
title: Aliasing in Azure Cosmos DB
description: Informazioni su come usare l'aliasing in Azure Cosmos DB query SQL per distinguere due proprietà con lo stesso nome
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873472"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing in Azure Cosmos DB

È possibile eseguire l'aliasing esplicito dei valori nelle query. Se una query ha due proprietà con lo stesso nome, usare l'aliasing per rinominare una o entrambe le proprietà in modo che siano ambiguità nel risultato previsto.

## <a name="examples"></a>esempi

La parola chiave AS utilizzata per l'aliasing è facoltativa, come illustrato nell'esempio seguente quando si proietta il secondo valore come `NameInfo`:

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
