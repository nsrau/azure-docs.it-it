---
title: Aliasing in Azure Cosmos DB
description: Informazioni su come usare l'aliasing nelle query SQL del database Cosmos di Azure per differenziare due proprietà con lo stesso nome
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873472"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing in Azure Cosmos DB

È possibile aliasre in modo esplicito i valori nelle query. Se una query ha due proprietà con lo stesso nome, utilizzare l'aliasing per rinominare una o entrambe le proprietà in modo che non ambiguità nel risultato proiettato.

## <a name="examples"></a>Esempi

La parola chiave AS utilizzata per l'aliasing è facoltativa, come `NameInfo`illustrato nell'esempio seguente quando si proietta il secondo valore come:

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
