---
title: Limiti degli oggetti
description: Illustra il modo in cui è possibile eseguire query sui limiti di oggetti spaziali
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681714"
---
# <a name="object-bounds"></a>Limiti degli oggetti

I limiti degli oggetti rappresentano il volume occupato da un' [entità](entities.md) e dai relativi elementi figlio. Nel rendering remoto di Azure, i limiti degli oggetti vengono sempre specificati come AABB ( *Axis aligned delimitator box* ). I limiti degli oggetti possono trovarsi in uno *spazio locale* o nello *spazio globale*. In entrambi i casi, sono sempre allineati all'asse, il che significa che gli extent e il volume possono variare tra la rappresentazione locale e quella globale dello spazio.

## <a name="querying-object-bounds"></a>Esecuzione di query sui limiti degli oggetti

È possibile eseguire query sulla AABB locale di una [mesh](meshes.md) direttamente dalla risorsa mesh. Questi limiti possono essere trasformati nello spazio locale o nello spazio globale di un'entità usando la trasformazione dell'entità.

È possibile calcolare i limiti di un'intera gerarchia di oggetti in questo modo, ma ciò richiede per attraversare la gerarchia, eseguire una query sui limiti per ogni mesh e combinarli manualmente. Questa operazione è noiosa e inefficiente.

Un modo migliore consiste nel chiamare `QueryLocalBoundsAsync` o `QueryWorldBoundsAsync` su un'entità. Il calcolo viene quindi scaricato nel server e restituito con un ritardo minimo.

``` cs
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

## <a name="next-steps"></a>Passaggi successivi

* [Query spaziali](../overview/features/spatial-queries.md)
