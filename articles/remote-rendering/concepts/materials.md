---
title: Materiali
description: Rendering della descrizione del materiale e delle proprietà del materiale
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681844"
---
# <a name="materials"></a>Materiali

I materiali sono [risorse condivise](../concepts/lifetime.md) che definiscono la modalità di rendering dei [mesh](meshes.md) . I materiali vengono usati per specificare quali [trame](textures.md) applicare, se rendere gli oggetti trasparenti e come verrà calcolata l'illuminazione.

I materiali vengono creati automaticamente durante la [conversione del modello](../how-tos/conversion/model-conversion.md) e sono accessibili in fase di esecuzione. È anche possibile creare materiali personalizzati dal codice e sostituire quelli esistenti. Questo scenario è particolarmente utile se si vuole condividere lo stesso materiale in molte mesh. Poiché le modifiche di un materiale sono visibili in ogni mesh che vi fa riferimento, questo metodo può essere usato per applicare facilmente le modifiche.

> [!NOTE]
> Alcuni casi d'uso, ad esempio l'evidenziazione di un oggetto selezionato, possono essere eseguiti modificando i materiali, ma sono molto più facili da raggiungere tramite [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipi di materiale

Il rendering remoto di Azure dispone di due tipi di materiale distinti:

* I [materiali PBR](../overview/features/pbr-materials.md) vengono usati per le superfici che devono essere sottoposte a rendering nel modo più appropriato possibile. L'illuminazione realistica viene calcolata per questi materiali usando il *rendering fisico* (PBR). Per ottenere il massimo da questo tipo di materiale, è importante fornire dati di input di alta qualità, ad esempio la rugosità e le mappe normali.

* I [materiali dei colori](../overview/features/color-materials.md) vengono usati per i casi in cui non è necessaria un'illuminazione aggiuntiva. Questi materiali sono sempre luminosi e sono più facili da configurare. I materiali colori vengono usati per i dati che non devono avere alcuna illuminazione o che incorporano già un'illuminazione statica, ad esempio i modelli ottenuti tramite [fotogrammetria](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Assegnazione del materiale mesh rispetto a MeshComponent

Le [mesh](meshes.md) hanno una o più sottomaglie. Ogni sottomesh fa riferimento a un solo materiale. È possibile modificare il materiale da usare direttamente sulla mesh oppure è possibile eseguire l'override del materiale da usare per una submesh in una [MeshComponent](meshes.md#meshcomponent).

Quando si modifica un materiale direttamente nella risorsa mesh, questa modifica ha effetto su tutte le istanze di tale mesh. La modifica in MeshComponent, tuttavia, interessa solo tale istanza di mesh. Il metodo più appropriato dipende dal comportamento desiderato, ma la modifica di un MeshComponent è l'approccio più comune.

## <a name="material-classes"></a>Classi Material

Tutti i materiali forniti dall'API derivano dalla classe `Material`di base. Il tipo può essere sottoposto a `Material.MaterialSubType` query tramite o eseguendone direttamente il cast:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Materiali PBR](../overview/features/pbr-materials.md)
* [Materiali a colori](../overview/features/color-materials.md)
