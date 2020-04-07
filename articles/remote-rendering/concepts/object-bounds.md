---
title: Limiti dell'oggetto
description: Viene illustrato come è possibile eseguire query nei limiti degli oggetti spaziali
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681714"
---
# <a name="object-bounds"></a>Limiti dell'oggetto

I limiti dell'oggetto rappresentano il volume occupato da [un'entità](entities.md) e dai relativi elementi figlio. Nel rendering remoto di Azure i limiti dell'oggetto vengono sempre forniti come riquadri di *delimitazione allineati all'asse* (AABB). I limiti dell'oggetto possono trovarsi nello *spazio locale* o nello *spazio globale.* In entrambi i casi, sono sempre allineati all'asse, il che significa che le estensioni e il volume possono differire tra la rappresentazione dello spazio locale e globale.

## <a name="querying-object-bounds"></a>Esecuzione di query sui limiti degli oggetti

L'AABB locale di una [mesh](meshes.md) può essere interrogato direttamente dalla risorsa mesh. Questi limiti possono essere trasformati nello spazio locale o nello spazio globale di un'entità utilizzando la trasformazione dell'entità.

È possibile calcolare i limiti di un'intera gerarchia di oggetti in questo modo, ma ciò richiede di attraversare la gerarchia, eseguire query sui limiti per ogni mesh e combinarli manualmente. Questa operazione è noiosa e inefficiente.

Un modo migliore `QueryLocalBoundsAsync` è `QueryWorldBoundsAsync` quello di chiamare o su un'entità. Il calcolo viene quindi scaricato sul server e restituito con un ritardo minimo.

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
