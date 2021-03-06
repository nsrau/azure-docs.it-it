---
title: Limiti degli oggetti
description: Illustra il modo in cui è possibile eseguire query sui limiti spaziali degli oggetti
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 421265bf1ee488c8e7d0c41e3ec9a250392d6f3d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202787"
---
# <a name="object-bounds"></a>Limiti degli oggetti

I limiti degli oggetti rappresentano il volume occupato da un'[entità](entities.md) e dai relativi elementi figlio. In Rendering remoto di Azure i limiti degli oggetti sono sempre assegnati come *rettangoli delimitatori allineati all'asse* (AABB, Axis Aligned Bounding Box). I limiti degli oggetti possono trovarsi nello *spazio locale* o nello *spazio globale*. In entrambi i casi sono sempre allineati all'asse. Questo significa che le estensioni e il volume della rappresentazione nello spazio locale possono essere diversi da quelli della rappresentazione nello spazio globale.

## <a name="querying-object-bounds"></a>Esecuzione di query sui limiti degli oggetti

È possibile eseguire query sul rettangolo delimitatore locale allineato all'asse di una [mesh](meshes.md) direttamente dalla risorsa mesh. Questi limiti possono essere trasformati nello spazio locale o nello spazio globale di un'entità usando la trasformazione dell'entità.

Con questo metodo è possibile calcolare i limiti di un'intera gerarchia di oggetti, ma è necessario attraversare la gerarchia, eseguire query sui limiti di ogni mesh e unire tali limiti manualmente. È un'operazione noiosa e poco efficiente.

Un metodo più efficiente consiste nel chiamare `QueryLocalBoundsAsync` o `QueryWorldBoundsAsync` su un'entità. Il calcolo viene quindi scaricato nel server e restituito in tempi brevi.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="api-documentation"></a>Documentazione dell'API

* [Entity. QueryLocalBoundsAsync C#](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [Entità C++:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Passaggi successivi

* [Query spaziali](../overview/features/spatial-queries.md)