---
title: Ottenere informazioni su un modello convertito
description: Descrizione di tutti i parametri per la conversione dei modelli
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: f5c38ac88503416b37b720a091c9e46d819a3146
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509298"
---
# <a name="get-information-about-a-converted-model"></a>Ottenere informazioni su un modello convertito

Il file arrAsset prodotto dal servizio di conversione è destinato esclusivamente all'utilizzo da parte del servizio di rendering. In alcuni casi, tuttavia, è possibile che si desideri accedere alle informazioni su un modello senza avviare una sessione di rendering. Pertanto, il servizio di conversione inserisce un file JSON accanto al file arrAsset nel contenitore di output. Se, ad esempio, un file `buggy.gltf` viene convertito, il contenitore di output conterrà un file denominato `buggy.info.json` accanto all'asset convertito `buggy.arrAsset` . Contiene informazioni sul modello di origine, sul modello convertito e sulla conversione stessa.

## <a name="example-info-file"></a>File di *informazioni* di esempio

Di seguito è riportato un esempio di file di *informazioni* prodotto dalla conversione di un file denominato `buggy.gltf` :

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

### <a name="the-files-section"></a>Sezione *file*

Questa sezione contiene i nomi file specificati.

* `input`: Nome del file di origine.
* `output`: Nome del file di output, quando l'utente ha specificato un nome non predefinito.

### <a name="the-conversionsettings-section"></a>Sezione *conversionSettings*

Questa sezione include una copia di [ConversionSettings](configure-model-conversion.md#settings-file) specificata al momento della conversione del modello.

### <a name="the-inputinfo-section"></a>Sezione *inputInfo*

In questa sezione vengono registrate le informazioni sul formato del file di origine.

* `sourceAssetExtension`: Estensione del file di origine.
* `sourceAssetFormat`: Descrizione del formato del file di origine.
* `sourceAssetFormatVersion`: Versione del formato del file di origine.
* `sourceAssetGenerator`: Nome dello strumento che ha generato il file di origine, se disponibile.

### <a name="the-inputstatistics-section"></a>Sezione *inputStatistics*

In questa sezione vengono fornite informazioni sulla scena di origine. Spesso si verificano discrepanze tra i valori di questa sezione e i valori equivalenti nello strumento che ha creato il modello di origine. Queste differenze sono previste perché il modello viene modificato durante i passaggi di esportazione e conversione.

* `numMeshes`: Numero di parti mesh, in cui ogni parte può fare riferimento a un singolo materiale.
* `numFaces`: Numero totale di _triangoli_ nell'intero modello. Si noti che la mesh viene triangolata durante la conversione. Questo numero contribuisce al limite del poligono nella [dimensione del server di rendering standard](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
* `numVertices`: Numero totale di vertici nell'intero modello.
* `numMaterial`: Numero totale di materiali nell'intero modello.
* `numFacesSmallestMesh`: Numero di triangoli nella mesh più piccola del modello.
* `numFacesBiggestMesh`: Numero di triangoli nella mesh più grande del modello.
* `numNodes`: Numero di nodi nel grafico della scena del modello.
* `numMeshUsagesInScene`: Numero di volte in cui i nodi fanno riferimento a mesh. Più di un nodo può fare riferimento alla stessa rete.
* `maxNodeDepth`: La profondità massima dei nodi all'interno del grafico della scena.

### <a name="the-outputinfo-section"></a>Sezione *outputInfo*

In questa sezione vengono registrate le informazioni generali sull'output generato.

* `conversionToolVersion`: Versione del convertitore di modelli.
* `conversionHash`: Hash dei dati all'interno di arrAsset che possono contribuire al rendering. Può essere usato per capire se il servizio di conversione ha prodotto un risultato diverso quando viene rieseguito nello stesso file.

### <a name="the-outputstatistics-section"></a>Sezione *outputStatistics*

Questa sezione registra le informazioni calcolate dall'asset convertito.

* `numMeshPartsCreated`: Numero di mesh in arrAsset. Può essere diverso da `numMeshes` nella `inputStatistics` sezione, perché il processo di conversione influisce sulle istanze.
* `numMeshPartsInstanced`: Numero di mesh riutilizzate in arrAsset.
* `recenteringOffset`: Quando l' `recenterToOrigin` opzione in [ConversionSettings](configure-model-conversion.md) è abilitata, questo valore è la traduzione che sposta il modello convertito nella posizione originale.
* `boundingBox`: Limiti del modello.

## <a name="next-steps"></a>Passaggi successivi

* [Conversione di modelli](model-conversion.md)
* [Configurare la conversione di modelli](configure-model-conversion.md)
