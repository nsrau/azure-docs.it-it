---
title: Ottenere informazioni sulle conversioni
description: Ottenere informazioni sulle conversioni
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 529bfb61b3af7040f3656c04071683841f5abe86
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870290"
---
# <a name="get-information-about-conversions"></a>Ottenere informazioni sulle conversioni

## <a name="information-about-a-conversion-the-result-file"></a>Informazioni su una conversione: il file di risultati

Quando il servizio di conversione converte un asset, scrive un riepilogo dei problemi in un "file di risultati". Se, ad esempio, un file `buggy.gltf` viene convertito, il contenitore di output conterrà un file denominato `buggy.result.json` .

Il file dei risultati elenca tutti gli errori e gli avvisi che si sono verificati durante la conversione e fornisce un riepilogo dei risultati, ovvero uno tra `succeeded` , `failed` o `succeeded with warnings` .
Il file dei risultati è strutturato come matrice JSON di oggetti, ognuno dei quali ha una proprietà di stringa che è una delle proprietà `warning` ,, `error` `internal warning` , `internal error` e `result` . Ci sarà al massimo un errore ( `error` o `internal error` ) e ne sarà sempre presente uno `result` .

## <a name="example-result-file"></a>Esempio di file di *risultati*

Nell'esempio seguente viene descritta una conversione che ha generato correttamente un arrAsset. Tuttavia, poiché c'era una trama mancante, il arrAsset risultante potrebbe non essere quello previsto.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>Informazioni su un modello convertito: il file di informazioni

Il file arrAsset prodotto dal servizio di conversione è destinato esclusivamente all'utilizzo da parte del servizio di rendering. In alcuni casi, tuttavia, è possibile che si desideri accedere alle informazioni su un modello senza avviare una sessione di rendering. Per supportare questo flusso di lavoro, il servizio di conversione inserisce un file JSON accanto al file arrAsset nel contenitore di output. Se, ad esempio, un file `buggy.gltf` viene convertito, il contenitore di output conterrà un file denominato `buggy.info.json` accanto all'asset convertito `buggy.arrAsset` . Contiene informazioni sul modello di origine, sul modello convertito e sulla conversione stessa.

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

## <a name="deprecated-features"></a>Funzionalità deprecate

Il servizio di conversione scrive i file `stdout.txt` e nel `stderr.txt` contenitore di output e questi sono stati l'unica fonte di avvisi ed errori.
Questi file sono ora deprecati. Usare invece [i file dei risultati](#information-about-a-conversion-the-result-file) a questo scopo.

## <a name="next-steps"></a>Passaggi successivi

* [Conversione di modelli](model-conversion.md)
* [Configurare la conversione di modelli](configure-model-conversion.md)
