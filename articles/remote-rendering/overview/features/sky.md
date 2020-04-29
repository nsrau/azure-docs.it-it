---
title: Riflessi del cielo
description: Viene descritto come configurare le mappe dell'ambiente per i riflessi Sky
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680609"
---
# <a name="sky-reflections"></a>Riflessi del cielo

Nel rendering remoto di Azure viene usata una trama Sky per leggere gli oggetti in modo realistico. Per le applicazioni di realtà potenziate, questa trama dovrebbe essere simile a quella del mondo reale, in modo da rendere gli oggetti convincenti. Questo articolo descrive come modificare la trama del cielo.

> [!NOTE]
> La trama celeste viene anche definita *mappa dell'ambiente*. Questi termini vengono usati in modo interscambiabile.

## <a name="object-lighting"></a>Illuminazione oggetti

Il rendering remoto di Azure impiega il *rendering basato su fisico* (PBR) per i calcoli di illuminazione realistici. Sebbene sia possibile aggiungere [fonti di luce](lights.md) alla scena, l'uso di una trama cielo ottimale ha il maggiore effetto.

Le immagini seguenti illustrano i risultati dell'illuminazione di superfici diverse solo con una trama cielo:

| Rugosità  | 0                                        | 0,25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Non metal  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Per ulteriori informazioni sul modello di illuminazione, vedere il capitolo [materiali](../../concepts/materials.md) .

> [!IMPORTANT]
> Il rendering remoto di Azure usa la trama Sky solo per i modelli di illuminazione. Il cielo non viene eseguito come sfondo, perché le applicazioni di realtà aumentata hanno già un background appropriato, ovvero il mondo reale.

## <a name="changing-the-sky-texture"></a>Modifica della trama del cielo

Per modificare la mappa dell'ambiente, è sufficiente [caricare una trama](../../concepts/textures.md) e modificare la sessione `SkyReflectionSettings`:

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

Si noti che `LoadTextureFromSASAsync` la variante viene utilizzata sopra perché viene caricata una trama incorporata. Nel caso di caricamento da [risorse di archiviazione BLOB collegate](../../how-tos/create-an-account.md#link-storage-accounts), usare la variante `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Tipi di trama Sky

È possibile usare le *trame* *[CubeMaps](https://en.wikipedia.org/wiki/Cube_mapping)* e 2D come mappe dell'ambiente.

Tutte le trame devono essere in un [formato di trama supportato](../../concepts/textures.md#supported-texture-formats). Non è necessario fornire mipmap per le trame Sky.

### <a name="cube-environment-maps"></a>Mappe dell'ambiente del cubo

Per riferimento, di seguito è riportato un mappa cubi senza wrapper:

![Mappa cubi di cui non è stato eseguito il wrapped](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Usare `LoadTextureFromSASAsync` with `TextureType.CubeMap` per caricare trame mappa cubi.

### <a name="sphere-environment-maps"></a>Mappe dell'ambiente Sphere

Quando si usa una trama 2D come mappa dell'ambiente, l'immagine deve essere nello [spazio delle coordinate sferiche](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Immagine Sky nelle coordinate sferiche](media/spheremap-example.png)

Usare `AzureSession.Actions.LoadTextureAsync` con `TextureType.Texture2D` per caricare le mappe dell'ambiente sferico.

## <a name="built-in-environment-maps"></a>Mappe dell'ambiente predefinite

Il rendering remoto di Azure fornisce alcune mappe dell'ambiente predefinite che sono sempre disponibili. Tutte le mappe dell'ambiente predefinite sono CubeMaps.

|Identificatore                         | Descrizione                                              | Illustrazione                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Ampia gamma di luci Strip, illuminazione luminosa di base interna    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Impostazione luce interna luminosa, più luci finestra      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Luci a colori variabili nell'impostazione dell'interno medio chiaro  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Luce di ambiente Hall moderatamente luminosa                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Impostazione Dim indoor con contrasto chiaro/scuro              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luce dell'ambiente diurna, luce luminosa dell'area della finestra     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Cielo notturno scuro e terreno con molte luci circostanti   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Luce solare chiaro e contrasto ombreggiatura                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Cancella cielo chiaro con illuminazione a fondo moderato            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Variazione moderata di sole e ombreggiatura                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Sera tramonto chiaro prossimo crepuscolo                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Chiaro, lussureggiante, verde e bianco chiaro, ombreggiato | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Giorno con luce chiara di ambiente luminoso                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | Uguale a TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Passaggi successivi

* [Luci](../../overview/features/lights.md)
* [Materiali](../../concepts/materials.md)
* [Trame](../../concepts/textures.md)
* [Strumento da riga di comando TexConv](../../resources/tools/tex-conv.md)
