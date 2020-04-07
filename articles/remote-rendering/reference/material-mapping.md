---
title: Mappatura dei materiali per i formati di modello
description: Descrive la conversione di default dai formati di origine del modello al materiale PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: ce287ed94066aac4b900d2ddb02579a54b8550f6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680388"
---
# <a name="material-mapping-for-model-formats"></a>Mappatura dei materiali per i formati di modello

Quando un asset di origine viene [convertito come modello,](../how-tos/conversion/model-conversion.md)il convertitore crea [materiali](../concepts/materials.md) per ogni [mesh.](../concepts/meshes.md) La modalità di creazione dei materiali può essere [sostituita.](../how-tos/conversion/override-materials.md) Tuttavia per impostazione predefinita la conversione creerà [materiali PBR](../overview/features/pbr-materials.md). Poiché ogni formato di file di origine, ad esempio FBX, utilizza le proprie convenzioni per definire i materiali, tali convenzioni devono essere mappate ai parametri del materiale PBR del rendering remoto di Azure.Since every source file format, like FBX, uses its own conventions to define materials, those conventions must be mapped to the PBR material parameters of Azure Remote Rendering. 

Questo articolo elenca i mapping esatti utilizzati per convertire i materiali dalle risorse di origine ai materiali di runtime.

## <a name="gltf"></a>glTF

Quasi tutto, dalla specifica glTF 2.0 è supportato in Rendering remoto di Azure, ad eccezione di *EmissiveFactor* e *EmissiveTexture*.

La tabella seguente illustra il mapping:

| glTF | Rendering remoto di Azure |
|:-------------------|:--------------------------|
|   baseColorFactor (fattore di colore base)   |   albedoColor              |
|   baseColorTexture (informazioni in baseAColorTexture)  |   albedoMap                |
|   fattore metallico    |   metallurtà                |
|   metallicTexture (Texture)   |   metalnessMappa             |
|   roughnessFactor   |   Rugosità                |
|   ruviditàTexture  |   roughnessMap             |
|   occlusionFactor   |   Occlusione                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMappa                |
|   AlphaCutoff (tagliata in falda parola)       |   Soglia alphaClip       |
|   AlfaMode.OPAQUE  |   alphaClipEnabled - false, isTransparent - false |
|   AlphaMode.MASK    |   alphaClipEnabled - true, isTransparent - false  |
|   AlphaMode.BLEND   |   isTransparent - true     |
|   doubleSided       |   è DoubleSided            |
|   emissivoFattore    |   -                        |
|   emissivoTexture   |   -                        |

Ogni trama in glTF `texCoord` può avere un valore, che è supportato anche nei materiali di rendering remoto di Azure.Each texture in glTF can have a value, which is also supported in the Azure Remote Rendering materials.

### <a name="embedded-textures"></a>Trame incorporate

Sono supportate * \** le trame incorporate nei file .bin o * \*.glb.*

### <a name="supported-gltf-extension"></a>Estensione glTF supportata

Oltre al set di funzionalità di base, il rendering remoto di Azure supporta le seguenti estensioni glTF:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [MSFT_texture_dds](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_texture_dds/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): Corrisponde ai materiali di [colore](../overview/features/color-materials.md). Per i materiali *emissivi,* si consiglia di utilizzare questa estensione.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): Invece delle texture metalliche-ruvidità, è possibile fornire texture diffuse-specular-glossiness. L'implementazione del rendering remoto di Azure segue direttamente le formule di conversione dall'estensione.

## <a name="fbx"></a>Fbx

Il formato FBX è a fonte chiusa e i materiali FBX non sono compatibili con i materiali PBR in generale. FBX utilizza una descrizione complessa delle superfici con molti parametri e proprietà univoci e non tutte vengono usate dalla pipeline di **rendering remoto di Azure.**

> [!IMPORTANT]
> La pipeline di conversione del modello di rendering remoto di Azure supporta solo **FBX 2011 e versioni successive.**

Il formato FBX definisce un approccio conservativo per i materiali, ci sono solo due tipi nella specifica ufficiale FBX:

* *Lambert* - Non comunemente usato già da un po 'di tempo, ma è ancora supportato dalla conversione in Phong al momento della conversione.
* *Phong* - Quasi tutti i materiali e la maggior parte degli strumenti di contenuto utilizzano questo tipo.

Il modello Phong è più preciso e viene utilizzato come *unico* modello per i materiali FBX. Sotto di esso sarà indicato come il *materiale FBX*.

> Maya utilizza due estensioni personalizzate per FBX definendo proprietà personalizzate per i tipi PBR e Stingray di un materiale. Questi dettagli non sono inclusi nella specifica FBX, pertanto non è attualmente supportato da Azure Remote Rendering.

I materiali FBX utilizzano il concetto Diffuse-Specular-SpecularLevel, quindi per convertire da una trama diffusa a una mappa albedo è necessario calcolare gli altri parametri per sottrarli dalla diffusione.

> Tutti i colori e le trame in FBX si trovano nello spazio sRGB (noto anche come spazio Gamma), ma il rendering remoto di Azure funziona con spazio lineare durante la visualizzazione e alla fine del frame riconverte tutto nello spazio sRGB. La pipeline dell'asset di Rendering remoto di Azure converte tutto in vano lineare per inviarlo come dati preparati al renderer.

Questa tabella mostra come vengono mappate le trame dai materiali FBX ai materiali di rendering remoto di Azure.This table shows how textures are mapped from FBX Materials to Azure Remote Rendering materials. Alcuni di essi non sono utilizzati direttamente, ma in combinazione con altre texture che partecipano alle formule (ad esempio la trama diffusa):

| Fbx | Rendering remoto di Azure |
|:-----|:----|
| Colore Ambiente | Mappa dell'occlusione   |
| DiffuseColor | *utilizzato per Albedo, Metalness* |
| TransparentColore | *utilizzato per canale alfa di Albedo* |
| Fattore di trasparenza | *utilizzato per canale alfa di Albedo* |
| Opacità | *utilizzato per canale alfa di Albedo* |
| SpecularColore | *utilizzato per Albedo, Metalness, Roughness* |
| Fattore Speculare| *utilizzato per Albedo, Metalness, Roughness* |
| ShininessEsponente | *utilizzato per Albedo, Metalness, Roughness* |
| Mappa Normale | Mappa Normale |
| Urto | *convertito in NormalMap* |
| EmissivoColore | - |
| EmissiveFattore | - |
| ReflectionColor | - |
| SpostamentoColore | - |

La mappatura di cui sopra è la parte più complessa della conversione del materiale, a causa di molte ipotesi che devono essere fatte. Di seguito discuteremo questi presupposti.

Alcune definizioni utilizzate di seguito:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Rosso: 0,2125 `Specular`. Verde: 0,7154 `Specular`. Blu 0,0721
* `DiffuseBrightness`0,299 . `Diffuse` Rosso<sup>2</sup> - 0,587 . `Diffuse` Verde<sup>2</sup> - 0,114 . `Diffuse` Blu<sup>2</sup>
* `SpecularBrightness`0,299 . `Specular` Rosso<sup>2</sup> - 0,587 . `Specular` Verde<sup>2</sup> - 0,114 . `Specular` Blu<sup>2</sup>
* `SpecularStrength`: max(`Specular`. Rosso, `Specular`. Verde, `Specular`. Blu)

La formula SpecularIntensity viene ottenuta da [qui](https://en.wikipedia.org/wiki/Luma_(video)).
La formula di luminosità è descritta in questa [specifica](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Rugosità

`Roughness`viene calcolato `Specular` `ShininessExponent` da e utilizzando [questa formula](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). La formula è un'approssimazione della rugosità dall'esponente speculare di Phong:

```Cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metallizza

`Metalness`viene calcolato `Diffuse` `Specular` da e utilizzando questa [formula dalla specifica glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

L'idea qui è che risolviamo l'equazione: Ax<sup>2</sup>
Fondamentalmente, le superfici dielettriche riflettono circa il 4% della luce in modo speculare, e il resto è diffuso. Le superfici metalliche non riflettono la luce in modo diffuso, ma il tutto in modo speculare.
Questa formula ha alcuni inconvenienti, perché non c'è modo di distinguere tra plastica lucida e superfici metalliche lucide. Presumiamo che la maggior parte del tempo la superficie ha proprietà metalliche, e di conseguenza superfici di plastica / gomma lucide potrebbero non apparire come previsto.
```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo`viene calcolato `Diffuse`da `Specular`, `Metalness`, e .

Come descritto nella sezione Metalness, le superfici dielettriche riflettono circa il 4% della luce.  
L'idea qui è quella di `Dielectric` `Metal` interpolare `Metalness` linearmente tra e colori utilizzando il valore come fattore. Se la `0.0`metallia è , quindi a seconda dello speculare sarà o un colore scuro (se speculare è alto) o diffuso non cambierà (se non è presente alcuno speculare). Se la metallo è un valore elevato, il colore diffuso scomparirà a favore del colore speculare.

```Cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB`è stato calcolato dalla formula precedente, ma il canale alfa richiede calcoli aggiuntivi. Il formato FBX è vago sulla trasparenza e ha molti modi per definirla. Diversi strumenti di contenuto utilizzano metodi diversi. L'idea qui è quella di unificarli in una formula. Tuttavia, rende alcune risorse visualizzate in modo non corretto come trasparenti, se non vengono create in modo comune.

Viene calcolato da `TransparentColor` `TransparencyFactor`, `Opacity`, , :

se `Opacity` è definito, quindi `AlbedoAlpha`  =  `Opacity` utilizzarlo direttamente: altrimenti  
se `TransparencyColor` è definito, allora `AlbedoAlpha` - 1.0 - ((`TransparentColor`. Rosso `TransparentColor`: . Il `TransparentColor`verde è . Blu) / 3.0) altro  
se `TransparencyFactor`, `AlbedoAlpha` allora 1,0 -`TransparencyFactor`

Il `Albedo` colore finale ha quattro `AlbedoRGB` canali, combinando il con il `AlbedoAlpha`.

### <a name="summary"></a>Summary

Per riassumere `Albedo` qui, sarà molto `Diffuse`vicino `Specular` all'originale , se è vicino a zero. In caso contrario, la superficie sarà simile a una superficie metallica e perde il colore diffuso. La superficie sarà più lucida e `ShininessExponent` riflettente `Specular` se è abbastanza grande ed è luminosa. In caso contrario, la superficie sarà ruvida e rifletterà a malapena l'ambiente.

### <a name="known-issues"></a>Problemi noti

* La formula corrente non funziona bene per la geometria colorata semplice. Se `Specular` è abbastanza luminoso, allora tutte le geometrie diventano superfici metalliche riflettenti senza alcun colore. La soluzione qui `Specular` è quello di abbassare al 30% dall'originale o di utilizzare l'impostazione di conversione [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* I materiali PBR `Maya` sono `3DS Max` stati aggiunti di recente e gli strumenti di creazione dei contenuti. Usano proprietà personalizzate della casella nera definite dall'utente per passarlo a FBX. Rendering remoto di Azure non legge tali proprietà aggiuntive perché non sono documentate e il formato è closed source.

## <a name="next-steps"></a>Passaggi successivi

* [Conversione del modello](../how-tos/conversion/model-conversion.md)
* [Sostituzione dei materiali durante la conversione del modello](../how-tos/conversion/override-materials.md)
