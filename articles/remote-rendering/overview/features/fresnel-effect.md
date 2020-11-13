---
title: Effetto Fresnel
description: Pagina spiegazione funzionalità per l'effetto materiale Fresnel
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 0596d118d1f3c09bc295891f023fe9990f3f1e05
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557922"
---
# <a name="fresnel-effect"></a>Effetto Fresnel

La funzionalità materiale effetto Fresnel è un effetto ad hoc non fisicamente corretto. La funzionalità è basata sull'osservazione fisica degli oggetti che diventano più riflessivi in questi angoli. La reflection Fresnel è già incorporata fisicamente nel [modello di materiale PBR](../../overview/features/pbr-materials.md) usato nel rendering remoto di Azure. Al contrario, la funzionalità materiale effetto Fresnel è semplicemente un effetto di colore additivo, senza dipendenze dalle [luci](../../overview/features/lights.md) o dall' [ambiente Sky](../../overview/features/sky.md).

L'effetto Fresnel fornisce agli oggetti interessati un luccichio colorato attorno ai bordi. Le informazioni sulla personalizzazione degli effetti e gli esempi dei risultati del rendering sono disponibili nelle sezioni seguenti.

## <a name="enabling-the-fresnel-effect"></a>Abilitazione dell'effetto Fresnel

Per usare la funzionalità effetto Fresnel, è necessario abilitarla sui materiali in questione. È possibile abilitarla impostando il bit FresnelEffect di [PbrMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) sul [materiale PBR](../../overview/features/pbr-materials.md). Lo stesso modello si applica a [ColorMaterialFeatures](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering) e al [materiale del colore](../../overview/features/color-materials.md). Per una dimostrazione sull'utilizzo, vedere la sezione Esempi di codice.

Dopo l'abilitazione, l'effetto Fresnel sarà immediatamente visibile. Per impostazione predefinita, lo splendore sarà bianco (1, 1, 1, 1) e un esponente di 1. È possibile personalizzare queste impostazioni usando i set di parametri riportati di seguito.

## <a name="customizing-the-effect-appearance"></a>Personalizzazione dell'aspetto degli effetti

Attualmente, l'effetto Fresnel può essere personalizzato in base al materiale usando le proprietà seguenti:

| Material (proprietà) | Type | Spiegazione |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | Il colore che viene aggiunto al massimo come il luccichio di Fresnel. Il canale alfa è attualmente ignorato. |
| FresnelEffectExponent | float | La diffusione del luccichio di Fresnel. Intervalli compresi tra 0,01 (distribuiti su tutti gli oggetti) e 10 (solo gli angoli più abbelliti). |

In pratica, le diverse impostazioni relative a colori ed esponenti hanno un aspetto simile al seguente:

![Esempi di effetto Fresnel](./media/fresnel-effect-examples.png)

L'esponente dell'effetto Fresnel viene incrementato progressivamente da 1 a 10 per ogni riga di colore. Questa operazione estrae progressivamente il luccichio dei bordi degli oggetti visualizzati. Anche l'effetto Fresnel non è influenzato dalla trasparenza, come si può notare nell'esempio seguente:

![Esempi di trasparenza effetto Fresnel](./media/fresnel-effect-transparent-examples.png)

Come illustrato, gli oggetti sulla diagonale sono completamente trasparenti, ma lo splendore Fresnel rimane. L'effetto imita il Fresnel fisico basato su questo aspetto, presente anche in queste schermate.

## <a name="code-samples"></a>Esempi di codice

Gli esempi di codice seguenti mostrano l'abilitazione e la personalizzazione dell'effetto Fresnel sia per un [materiale PBR](../../overview/features/pbr-materials.md) che per un [materiale a colori](../../overview/features/color-materials.md):

```cs
    void SetFresnelEffect(AzureSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<AzureSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>Documentazione dell'API

* [PbrMaterialFeatures C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [PbrMaterialFeatures C++](https://docs.microsoft.com/cpp/api/remote-rendering/pbrmaterialfeatures)
* [ColorMaterialFeatures C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [ColorMaterialFeatures C++](https://docs.microsoft.com/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](../../concepts/materials.md)
* [Materiali PBR](../../overview/features/pbr-materials.md)
* [Materiali colori](../../overview/features/color-materials.md)