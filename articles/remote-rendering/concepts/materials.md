---
title: Materiali
description: 'Rendering: descrizione e proprietà del materiale'
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c9de26d9a83319eee62cf6d30cf2c83f66446fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020253"
---
# <a name="materials"></a>Materiali

I materiali sono [risorse condivise](../concepts/lifetime.md) che definiscono la modalità di rendering delle [reti mesh](meshes.md). I materiali vengono usati per specificare quali [trame](textures.md) applicare, se rendere gli oggetti trasparenti e come calcolare l'illuminazione.

I materiali vengono creati automaticamente durante la [conversione modello](../how-tos/conversion/model-conversion.md) e sono accessibili in fase di esecuzione. È anche possibile creare materiali personalizzati partendo dal codice e sostituire quelli esistenti. Questo scenario è particolarmente utile se si vuole condividere lo stesso materiale in molte reti mesh. Poiché le modifiche di un materiale sono visibili in ogni rete mesh che vi fa riferimento, questo metodo può essere usato per applicare facilmente eventuali modifiche.

> [!NOTE]
> Alcuni casi d'uso, ad esempio l'evidenziazione di un oggetto selezionato, possono essere eseguiti modificando i materiali, ma sono molto più facili da ottenere tramite il comando [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md).

## <a name="material-types"></a>Tipi di materiale

Il rendering remoto di Azure dispone di due tipi di materiale distinti:

* [I materiali PBR](../overview/features/pbr-materials.md) vengono usati per le superfici di cui è necessario eseguire il rendering nel modo più appropriato possibile. L'illuminazione realistica per questi materiali viene calcolata mediante *PBR* (Physically Based Rendering). Per ottenere il massimo da questo tipo di materiale, è importante fornire dati di input di alta qualità, ad esempio il mapping di rugosità e normali.

* [I materiali a colori](../overview/features/color-materials.md) vengono usati nei casi in cui non è necessaria un'illuminazione aggiuntiva. Questi materiali sono sempre luminosi e sono più facili da configurare. I materiali a colori vengono usati per i dati che non devono avere alcuna illuminazione o che incorporano già un'illuminazione statica, ad esempio i modelli ottenuti tramite [fotogrammetria](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Assegnazione materiale mesh e MeshComponent a confronto

Le [reti mesh](meshes.md) presentano una o più mesh secondarie. Ogni mesh secondaria fa riferimento a un solo materiale. È possibile modificare il materiale da usare direttamente sulla mesh oppure eseguire l'override del materiale da usare per una mesh secondaria in un elemento [MeshComponent](meshes.md#meshcomponent).

Quando si modifica un materiale direttamente nella risorsa mesh, questa modifica ha effetto su tutte le istanze di tale mesh. La modifica in MeshComponent, tuttavia, interessa solo tale istanza della mesh. Il metodo più appropriato dipende dal comportamento desiderato, ma la modifica di un MeshComponent è l'approccio più comune.

## <a name="material-de-duplication"></a>Deduplica dei materiali

Durante la conversione, più materiali con le stesse proprietà e le stesse trame vengono automaticamente deduplicati in un singolo materiale. Anche se è possibile disabilitare questa funzionalità nelle [impostazioni di conversione](../how-tos/conversion/configure-model-conversion.md), è consigliabile lasciarla attivata per ottenere prestazioni ottimali.

## <a name="material-classes"></a>Classi di materiali

Tutti i materiali forniti dall'API derivano dalla classe di base `Material`. Il tipo può essere sottoposto a query tramite `Material.MaterialSubType` o eseguendone direttamente il cast:

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```


## <a name="next-steps"></a>Passaggi successivi

* [Materiali PBR](../overview/features/pbr-materials.md)
* [Materiali a colori](../overview/features/color-materials.md)
