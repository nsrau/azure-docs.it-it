---
title: Materiali
description: Descrizione del materiale di rendering e proprietà del materiale
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681844"
---
# <a name="materials"></a>Materiali

I materiali sono [risorse condivise](../concepts/lifetime.md) che definiscono la modalità di rendering [delle anem.](meshes.md) I materiali vengono [utilizzati](textures.md) per specificare quali trame applicare, se rendere trasparenti gli oggetti e come verrà calcolata l'illuminazione.

I materiali vengono creati automaticamente durante [la conversione](../how-tos/conversion/model-conversion.md) del modello e sono accessibili in fase di esecuzione. È inoltre possibile creare materiali personalizzati dal codice e sostituire quelli esistenti. Questo scenario ha particolarmente senso se si desidera condividere lo stesso materiale tra più maglie. Poiché le modifiche di un materiale sono visibili su ogni mesh che vi fa riferimento, questo metodo può essere utilizzato per applicare facilmente le modifiche.

> [!NOTE]
> Alcuni casi d'uso, ad esempio l'evidenziazione di un oggetto selezionato, possono essere eseguite modificando i materiali, ma sono molto più facili da ottenere tramite [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipi di materiale

Il rendering remoto di Azure ha due tipi di materiale distinti:Azure Remote Rendering has two distinct material types:

* [I materiali PBR](../overview/features/pbr-materials.md) vengono utilizzati per le superfici che devono essere renderizzate nel modo più corretto possibile. L'illuminazione realistica viene calcolata per questi materiali utilizzando il *rendering basato fisicamente* (PBR). Per ottenere il massimo da questo tipo di materiale, è importante fornire dati di input di alta qualità, ad esempio rugosità e mappe normali.

* [I materiali di colore](../overview/features/color-materials.md) vengono utilizzati per i casi in cui non si desidera alcuna illuminazione aggiuntiva. Questi materiali sono sempre pieni di luce e sono più facili da configurare. I materiali di colore vengono utilizzati per i dati che non dovrebbero avere alcuna illuminazione o incorporano già l'illuminazione statica, ad esempio i modelli ottenuti tramite [la fotogrammetria](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Assegnazione di materiale Mesh e MeshComponent

[Le noglie](meshes.md) hanno uno o più submeshes. Ogni mesh secondaria fa riferimento a un materiale. È possibile modificare il materiale da utilizzare direttamente sulla mesh oppure sostituire il materiale da utilizzare per una sottomesh su un [oggetto MeshComponent](meshes.md#meshcomponent).

Quando si modifica un materiale direttamente sulla risorsa mesh, questa modifica influisce su tutte le istanze di tale mesh. La modifica di esso su MeshComponent, tuttavia, ha effetto solo su tale istanza di mesh. Il metodo più appropriato dipende dal comportamento desiderato, ma la modifica di un Oggetto MeshComponent è l'approccio più comune.

## <a name="material-classes"></a>Classi di materiali

Tutti i materiali forniti dall'API `Material`derivano dalla classe base . Il loro tipo può `Material.MaterialSubType` essere interrogato attraverso o lanciandoli direttamente:

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
* [Materiali colore](../overview/features/color-materials.md)
