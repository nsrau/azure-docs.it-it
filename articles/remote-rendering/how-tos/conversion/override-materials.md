---
title: Sostituzione dei materiali durante la conversione del modello
description: Spiega il flusso di lavoro che esegue l'override del materiale in fase di conversione
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681480"
---
# <a name="override-materials-during-model-conversion"></a>Sostituzione dei materiali durante la conversione del modello

Durante la conversione, le impostazioni del materiale nel modello di origine vengono utilizzate per definire i [materiali PBR](../../overview/features/pbr-materials.md) utilizzati dal renderer.
A volte la [conversione predefinita](../../reference/material-mapping.md) non fornisce i risultati desiderati ed è necessario apportare modifiche.
Quando un modello viene convertito per l'uso nel rendering remoto di Azure, è possibile fornire un file di sostituzione del materiale per personalizzare la modalità di conversione del materiale in base al materiale.
La sezione sulla [configurazione della conversione](configure-model-conversion.md) del modello contiene istruzioni per dichiarare il nome del file di override del materiale.

## <a name="the-override-file-used-during-conversion"></a>Il file di sostituzione utilizzato durante la conversione

Come semplice esempio, diciamo che un modello box ha un unico materiale, chiamato "Default". Il colore albedo deve essere regolato per l'uso in ARR.
In questo caso, è possibile creare un `box_materials_override.json` file come segue:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Il `box_materials_override.json` file viene inserito nel `ConversionSettings.json` contenitore `box.fbx`di input e viene aggiunto un accanto a , che indica alla conversione dove trovare il file di sostituzione (vedere [Configurazione della conversione del modello](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

Quando il modello viene convertito, verranno applicate le nuove impostazioni.

### <a name="color-materials"></a>Materiali colore

Il modello [di materiale](../../overview/features/color-materials.md) a colori descrive una superficie costantemente ombreggiata che è indipendente dall'illuminazione.
Ciò è utile, ad esempio, per le risorse create dagli algoritmi di fotogrammetria.
Nei file di sostituzione del materiale, un materiale `unlit` può `true`essere dichiarato come materiale a colori impostando su .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignora mappe texture specifiche

A volte è possibile che il processo di conversione ignori mappe di trama specifiche. Questo potrebbe essere il caso quando il modello è stato generato da uno strumento che genera mappe speciali non comprese correttamente dal renderer. Ad esempio, un "OpacityMap" che viene utilizzato per definire un elemento diverso da opacità, o un modello in cui il "NormalMap" è memorizzato come "BumpMap". (In quest'ultimo caso si desidera ignorare "NormalMap", che causerà il convertitore di utilizzare "BumpMap" come "NormalMap".)

Il principio è semplice. Basta aggiungere una `ignoreTextureMaps` proprietà chiamata e aggiungere qualsiasi mappa texture che si desidera ignorare:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Per l'elenco completo delle mappe di trama che è possibile ignorare, vedere lo schema JSON riportato di seguito.

## <a name="json-schema"></a>Schema JSON

Lo schema JSON completo per i file di materiale è fornito qui. Ad eccezione `unlit` `ignoreTextureMaps`di e , le proprietà disponibili sono un sottoinsieme delle proprietà descritte nelle sezioni sui modelli di [materiale di colore](../../overview/features/color-materials.md) e materiale [PBR.](../../overview/features/pbr-materials.md)

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Materiali colore](../../overview/features/color-materials.md)
* [Materiali PBR](../../overview/features/pbr-materials.md)