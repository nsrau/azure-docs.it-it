---
title: Mapping del materiale per i formati di modello
description: Descrive la conversione predefinita dai formati di origine del modello al materiale PBR
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893094"
---
# <a name="material-mapping-for-model-formats"></a>Mapping del materiale per i formati di modello

Quando un asset di origine viene [convertito come modello](../how-tos/conversion/model-conversion.md), il convertitore crea [materiali](../concepts/materials.md) per ogni [mesh](../concepts/meshes.md). Il modo in cui vengono creati i materiali può essere [sottoposto a override](../how-tos/conversion/override-materials.md). Per impostazione predefinita, tuttavia, la conversione creerà [materiali PBR](../overview/features/pbr-materials.md). Poiché ogni formato di file di origine, ad esempio FBX, USA le proprie convenzioni per definire i materiali, è necessario eseguire il mapping di tali convenzioni ai parametri del materiale PBR del rendering remoto di Azure. 

Questo articolo elenca i mapping esatti usati per convertire i materiali dalle risorse di origine ai materiali di Runtime.

## <a name="gltf"></a>glTF

Quasi tutti gli elementi della specifica glTF 2,0 sono supportati nel rendering remoto di Azure, ad eccezione di *EmissiveFactor* e *EmissiveTexture*.

La tabella seguente illustra il mapping:

| glTF | Rendering remoto di Azure |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalismo                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   rugosità                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   occlusione                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode. OPAQUE  |   alphaClipEnabled = false, transparent = false |
|   alphaMode. MASK    |   alphaClipEnabled = true, transparent = false  |
|   alphaMode. BLEND   |   Transparent = true     |
|   Biadesivo       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Ogni trama in glTF può avere un `texCoord` valore, che è anche supportato nei materiali di rendering remoto di Azure.

### <a name="embedded-textures"></a>Trame incorporate

Sono supportate le trame incorporate nei file * \* . bin* o * \* . glb* .

### <a name="supported-gltf-extension"></a>Estensione glTF supportata

Inoltre, per il set di funzionalità di base, il rendering remoto di Azure supporta le estensioni glTF seguenti:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): corrisponde ai [materiali colori](../overview/features/color-materials.md). Per i materiali *emissivo* , è consigliabile usare questa estensione.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): è possibile creare trame lucentezza, invece delle trame di rugosità metallizzata. L'implementazione del rendering remoto di Azure segue direttamente le formule di conversione dall'estensione.

## <a name="fbx"></a>FBX

Il formato FBX è closed source e i materiali FBX non sono compatibili con i materiali PBR in generale. FBX usa una descrizione complessa delle superfici con molti parametri e proprietà univoci e **non tutte vengono usate dalla pipeline di rendering remoto di Azure**.

> [!IMPORTANT]
> La pipeline di conversione del modello di rendering remoto di Azure supporta solo **FBX 2011 e versioni successive**.

Il formato FBX definisce un approccio conservativo per i materiali. nella specifica FBX ufficiale sono disponibili solo due tipi:

* *Lambert* , che non viene comunemente usato per un certo periodo di tempo, ma è ancora supportato dalla conversione a Phong in fase di conversione.
* *Phong* : quasi tutti i materiali e la maggior parte degli strumenti di contenuto usano questo tipo.

Il modello Phong è più preciso e viene usato come *unico* modello per i materiali FBX. Sotto verrà indicato come *materiale FBX*.

> Maya usa due estensioni personalizzate per FBX definendo proprietà personalizzate per i tipi PBR e Stingray di un materiale. Questi dettagli non sono inclusi nella specifica FBX, quindi non è attualmente supportata dal rendering remoto di Azure.

I materiali FBX usano il concetto di SpecularLevel diffusa e speculare, quindi per eseguire la conversione da una trama diffusa a una mappa albedo è necessario calcolare gli altri parametri per sottrarli dalla diffusione.

> Tutti i colori e le trame in FBX sono nello spazio sRGB (noto anche come spazio gamma), ma il rendering remoto di Azure funziona con lo spazio lineare durante la visualizzazione e alla fine del frame converte tutti gli elementi nello spazio sRGB. La pipeline di asset di rendering remoto di Azure converte tutti gli elementi nello spazio lineare per inviarli come dati preparati al renderer.

Questa tabella illustra come viene eseguito il mapping delle trame da materiali FBX ai materiali di rendering remoto di Azure. Alcuni di essi non vengono usati direttamente, ma in combinazione con altre trame che partecipano alle formule (ad esempio, la trama diffusa):

| FBX | Rendering remoto di Azure |
|:-----|:----|
| AmbientColor | Mappa di occlusione   |
| DiffuseColor | *usato per albedo, metality* |
| TransparentColor | *usato per il canale alfa di albedo* |
| TransparencyFactor | *usato per il canale alfa di albedo* |
| Opacità | *usato per il canale alfa di albedo* |
| SpecularColor | *usato per albedo, metallurgia, rugosità* |
| SpecularFactor| *usato per albedo, metallurgia, rugosità* |
| ShininessExponent | *usato per albedo, metallurgia, rugosità* |
| NormalMap | NormalMap |
| Bump | *convertito in NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

Il mapping precedente è la parte più complessa della conversione del materiale, a causa di numerose ipotesi che devono essere apportate. Questi presupposti sono descritti di seguito.

Alcune definizioni usate di seguito:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. ∗ Rosso 0,2125 +  `Specular` . ∗ Verde 0,7154 + `Specular` . Blu ∗ 0,0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Rosso<sup>2</sup> + 0,587 * `Diffuse` . Verde<sup>2</sup> + 0,114 * `Diffuse` . Blu<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Rosso<sup>2</sup> + 0,587 * `Specular` . Verde<sup>2</sup> + 0,114 * `Specular` . Blu<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Rosso, `Specular` Verde, `Specular` . Blu

La formula SpecularIntensity è ottenuta da [qui](https://en.wikipedia.org/wiki/Luma_(video)).
La formula della luminosità è descritta in questa [specifica](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf).

### <a name="roughness"></a>Rugosità

`Roughness` viene calcolato da `Specular` e `ShininessExponent` utilizzando [questa formula](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf). La formula è un'approssimazione di rugosità dall'esponente speculare Phong:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalismo

`Metalness` viene calcolato da `Diffuse` e `Specular` utilizzando questa [formula dalla specifica glTF](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

L'idea è che si risolve l'equazione: AX<sup>2</sup> + BX + C = 0.
Fondamentalmente, le superfici dichiarative riflettono circa il 4% della luce in modo speculare e il resto è diffuso. Le superfici metalliche riflettono nessuna luce in modo diffuso, ma in modo speculare.
Questa formula presenta alcuni svantaggi, perché non esiste alcun modo per distinguere tra superficie lucida e plastica lucida. Si presuppone che la maggior parte del tempo la superficie abbia proprietà metalliche e, di conseguenza, le superfici di plastica o di gomma lucide potrebbero non apparire come previsto.

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

`Albedo` viene calcolato da `Diffuse` , `Specular` e `Metalness` .

Come descritto nella sezione metality, le superfici dielettriche riflettono circa il 4% della luce.  
L'idea è di eseguire un'interpolazione lineare tra `Dielectric` e `Metal` i colori usando il `Metalness` valore come fattore. Se la metallurgia è `0.0` , a seconda del tipo speculare sarà un colore scuro (se speculare è alta) o la diffusione non verrà modificata (se non è presente alcun oggetto speculare). Se la metallurgia è un valore elevato, il colore diffuso scomparirà a favore del colore speculare.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` è stato calcolato dalla formula precedente, ma il canale alfa richiede calcoli aggiuntivi. Il formato FBX è vago sulla trasparenza e può essere definito in molti modi. Diversi strumenti di contenuto usano metodi diversi. L'idea è di unificarle in un'unica formula. In questo modo, alcuni asset vengono erroneamente visualizzati come trasparenti, tuttavia, se non vengono creati in modo comune.

Questa operazione viene calcolata da `TransparentColor` , `TransparencyFactor` , `Opacity` :

Se `Opacity` è definito, usarlo direttamente: `AlbedoAlpha`  =  `Opacity` else  
Se `TransparencyColor` è definito, quindi `AlbedoAlpha` = 1,0-( `TransparentColor` . Rosso + `TransparentColor` . Verde + `TransparentColor` . Blu)/3,0) else  
Se `TransparencyFactor` , quindi `AlbedoAlpha` = 1,0- `TransparencyFactor`

Il `Albedo` colore finale ha quattro canali, combinando `AlbedoRGB` con `AlbedoAlpha` .

### <a name="summary"></a>Riepilogo

Per riepilogare qui, `Albedo` sarà molto vicino all'originale `Diffuse` , se `Specular` è vicino a zero. In caso contrario, la superficie apparirà come una superficie metallica e perderà il colore diffuso. L'aspetto della superficie sarà più lucido e riflettente se `ShininessExponent` è sufficientemente grande ed `Specular` è chiaro. In caso contrario, la superficie apparirà approssimativa e rispecchierà a malapena l'ambiente.

### <a name="known-issues"></a>Problemi noti

* La formula corrente non funziona correttamente per la geometria colorata semplice. Se `Specular` è sufficientemente chiaro, tutte le geometrie diventano superfici metallizzate riflettenti senza alcun colore. La soluzione alternativa consiste nel ridurre `Specular` al 30% dall'originale o usare l'impostazione di conversione [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model).
* I materiali PBR sono stati recentemente aggiunti agli `Maya` strumenti e alla `3DS Max` creazione del contenuto. Usano proprietà personalizzate della casella nera definite dall'utente per passarle a FBX. Il rendering remoto di Azure non legge tali proprietà aggiuntive perché non sono documentate e il formato è closed-source.

## <a name="next-steps"></a>Passaggi successivi

* [Conversione di modelli](../how-tos/conversion/model-conversion.md)
* [Override dei materiali durante la conversione del modello](../how-tos/conversion/override-materials.md)
