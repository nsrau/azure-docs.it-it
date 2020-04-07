---
title: Ottenere informazioni su un modello convertitoGet information about a converted model
description: Descrizione di tutti i parametri di conversione del modello
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681519"
---
# <a name="get-information-about-a-converted-model"></a>Ottenere informazioni su un modello convertitoGet information about a converted model

Il file arrAsset prodotto dal servizio di conversione è destinato esclusivamente all'utilizzo da parte del servizio di rendering. In alcuni casi, tuttavia, può essere necessario accedere alle informazioni su un modello senza avviare una sessione di rendering. Pertanto, il servizio di conversione inserisce un file JSON accanto al file arrAsset nel contenitore di output. Ad esempio, se `buggy.gltf` un file viene convertito, il `buggy.info.json` contenitore `buggy.arrAsset`di output conterrà un file denominato accanto all'asset convertito. Contiene informazioni sul modello di origine, sul modello convertito e sulla conversione stessa.

## <a name="example-info-file"></a>Esempio di file *di informazioni*

Di seguito è riportato un file di `buggy.gltf` *informazioni* di esempio prodotto dalla conversione di un file denominato :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informazioni nel file di informazioni

### <a name="the-files-section"></a>La sezione *file*

Questa sezione contiene i nomi dei file forniti.

* `input`: il nome del file di origine.
* `output`: il nome del file di output, quando l'utente ha specificato un nome non predefinito.

### <a name="the-conversionsettings-section"></a>La sezione *conversionSettings*

Questa sezione contiene una copia di [ConversionSettings](configure-model-conversion.md#settings-file) specificata quando il modello è stato convertito.

### <a name="the-inputinfo-section"></a>La sezione *inputInfo*

In questa sezione vengono riportate le informazioni sul formato del file di origine.

* `sourceAssetExtension`: l'estensione del file di origine.
* `sourceAssetFormat`: descrizione del formato del file di origine.
* `sourceAssetFormatVersion`: versione del formato del file di origine.
* `sourceAssetGenerator`: il nome dello strumento che ha generato il file di origine, se disponibile.

### <a name="the-inputstatistics-section"></a>La sezione *inputStatistics*

In questa sezione vengono fornite informazioni sulla scena di origine. Spesso si dissibroni tra i valori in questa sezione e i valori equivalenti nello strumento che ha creato il modello di origine. Tali differenze sono previste, perché il modello viene modificato durante i passaggi di esportazione e conversione.

* `numMeshes`: il numero di parti mesh, in cui ogni parte può fare riferimento a un singolo materiale.
* `numFaces`: il numero totale di _triangoli_ nell'intero modello. Si noti che la mesh viene triangolata durante la conversione.
* `numVertices`: il numero totale di vertici nell'intero modello.
* `numMaterial`: il numero totale di materiali nell'intero modello.
* `numFacesSmallestMesh`: il numero di triangoli nella mesh più piccola del modello.
* `numFacesBiggestMesh`: il numero di triangoli nella mesh più grande del modello.
* `numNodes`: il numero di nodi nel grafico della scena del modello.
* `numMeshUsagesInScene`: il numero di volte in cui i nodi fanno riferimento alle maglie. Più di un nodo può fare riferimento alla stessa mesh.
* `maxNodeDepth`: profondità massima dei nodi all'interno del grafico della scena.

### <a name="the-outputinfo-section"></a>La sezione *outputInfo*

In questa sezione vengono riportate informazioni generali sull'output generato.

* `conversionToolVersion`: versione del convertitore di modelli.
* `conversionHash`: hash dei dati all'interno dell'assetAsset che possono contribuire al rendering. Può essere utilizzato per capire se il servizio di conversione ha prodotto un risultato diverso quando viene rieseguito sullo stesso file.

### <a name="the-outputstatistics-section"></a>La sezione *outputStatistics*

In questa sezione vengono riportate le informazioni calcolate dal cespite convertito.

* `numMeshPartsCreated`: il numero di maglie nell'arrAsset. Può differire `numMeshes` `inputStatistics` dalla sezione, perché l'istanza è influenzata dal processo di conversione.
* `numMeshPartsInstanced`: il numero di maglie che vengono riutilizzate nell'asset.
* `recenteringOffset`: quando `recenterToOrigin` l'opzione in [ConversionSettings](configure-model-conversion.md) è abilitata, questo valore è la traslazione che risposterebbe il modello convertito nella posizione originale.
* `boundingBox`: i limiti del modello.

## <a name="next-steps"></a>Passaggi successivi

* [Conversione del modello](model-conversion.md)
* [Configurare la conversione del modello](configure-model-conversion.md)
