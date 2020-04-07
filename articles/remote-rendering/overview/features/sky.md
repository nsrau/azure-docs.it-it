---
title: Riflessi del cielo
description: Descrive come impostare le mappe di ambiente per i riflessi del cielo
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680609"
---
# <a name="sky-reflections"></a>Riflessi del cielo

In Rendering remoto di Azure viene usata una trama del cielo per illuminare gli oggetti in modo realistico. Per le applicazioni di realtà aumentata, questa trama dovrebbe assomigliare all'ambiente reale, per rendere gli oggetti più convincenti. Questo articolo descrive come modificare la texture del cielo.

> [!NOTE]
> La texture del cielo è anche detta *mappa dell'ambiente.* Questi termini sono usati in modo intercambiabile.

## <a name="object-lighting"></a>Illuminazione degli oggetti

Il rendering remoto di Azure usa il *rendering basato fisicamente* (PBR) per calcoli di illuminazione realistici. Anche se è possibile aggiungere [sorgenti](lights.md) di luce alla scena, l'utilizzo di una buona texture del cielo ha il massimo impatto.

Le immagini seguenti mostrano i risultati dell'illuminazione di superfici diverse solo con una texture del cielo:

| Rugosità  | 0                                        | 0,25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Non-Metallo  | ![Dielettrico0](media/dielectric-0.png)   | ![Parco Verde](media/dielectric-0.25.png)  | ![Parco Verde](media/dielectric-0.5.png)  | ![Parco Verde](media/dielectric-0.75.png)  | ![Parco Verde](media/dielectric-1.png)  |
| Metal      | ![Parco Verde](media/metallic-0.png)  | ![Parco Verde](media/metallic-0.25.png)    | ![Parco Verde](media/metallic-0.5.png)    | ![Parco Verde](media/metallic-0.75.png)    | ![Parco Verde](media/metallic-1.png)    |

Per ulteriori informazioni sul modello di illuminazione, vedere il capitolo [dei materiali.](../../concepts/materials.md)

> [!IMPORTANT]
> Il rendering remoto di Azure usa la trama del cielo solo per i modelli di illuminazione. Non rende il cielo come sfondo, poiché le applicazioni di Realtà Aumentata hanno già uno sfondo adeguato - il mondo reale.

## <a name="changing-the-sky-texture"></a>Cambiare la texture del cielo

Per cambiare la mappa dell'ambiente, tutto quello che dovete `SkyReflectionSettings`fare è caricare una [texture](../../concepts/textures.md) e cambiare la sessione di :

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

Si noti che la `LoadTextureFromSASAsync` variante viene utilizzata in precedenza perché viene caricata una trama incorporata. In caso di caricamento da archivi `LoadTextureAsync` BLOB [collegati,](../../how-tos/create-an-account.md#link-storage-accounts)usare la variante.

## <a name="sky-texture-types"></a>Tipi di texture del cielo

È possibile utilizzare sia *[mappe cubo](https://en.wikipedia.org/wiki/Cube_mapping)* che *trame 2D* come mappe di ambiente.

Tutte le trame devono essere in un [formato di trama supportato.](../../concepts/textures.md#supported-texture-formats) Non è necessario fornire mipmap per le texture del cielo.

### <a name="cube-environment-maps"></a>Mappe dell'ambiente cubo

Per riferimento, ecco una mappa cubo senza ritorno di wrapping:For reference, here is an unwrapped cubemap:

![Una mappa cubo senza ritorno di wrappingAn unwrapped cubemap](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Utilizzare `LoadTextureFromSASAsync` `TextureType.CubeMap` con per caricare le trame della mappa cubo.

### <a name="sphere-environment-maps"></a>Mappe dell'ambiente Sphere

Quando si utilizza una trama 2D come mappa di ambiente, l'immagine deve essere nello spazio delle [coordinate sferiche](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Un'immagine del cielo in coordinate sferiche](media/spheremap-example.png)

Utilizzare `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` con per caricare mappe di ambiente sferiche.

## <a name="built-in-environment-maps"></a>Mappe di ambiente integrate

Il rendering remoto di Azure offre alcune mappe di ambiente predefinite sempre disponibili. Tutte le mappe di ambiente incorporate sono mappe cubo.

|Identificatore                         | Descrizione                                              | Illustrazione                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Varietà di luci a strisce, illuminazione di base interna luminosa    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Luminosa illuminazione interna, più luci finestra      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Luci colorate in modo variabile in condizioni interne di luce media  | ![Studio Colorato](media/colorful-studio.png)
|builtin://Hangar                   | Luce della sala ambiente moderatamente luminosa                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Ambiente interno dim con contrasto chiaro-scuro              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luce ambiente diurna, luce luminosa della finestra     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Cielo notturno scuro e terra con molte luci circostanti   | ![SataraNotte](media/satara-night.png)
|builtin://SunnyVondelpark          | Luce solare intensa e contrasto delle ombre                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Luce del cielo chiaro con illuminazione piana moderata            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Moderato sole e ombra                         | ![LacrimeOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Luce del tramonto serale in avvicinamento al tramonto                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Luminoso, lussureggiante-verde, e bianco toni della luce, terreno oscurato | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Digiorno con luminosa luce ambiente                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | Uguale a TearsOfSteelBridge                               | ![DefaultSky (informazioni in lingua predefinita)](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Passaggi successivi

* [Luci](../../overview/features/lights.md)
* [Materiali](../../concepts/materials.md)
* [Trame](../../concepts/textures.md)
* [Lo strumento da riga di comando TexConv](../../resources/tools/tex-conv.md)
