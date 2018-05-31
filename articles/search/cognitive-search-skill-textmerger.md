---
title: Competenza unione testi della ricerca cognitiva (Ricerca di Azure) | Microsoft Docs
description: Unire testo da una raccolta di campi in un unico campo consolidato. Usare questa competenza cognitiva in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: f349158873acca9d50d4d6e5fdfa3539f26207fe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362568"
---
#    <a name="text-merge-cognitive-skill"></a>Competenza cognitiva di unione testi

La competenza **unione testi** consolida il testo da una raccolta di campi in un unico campo. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.TextMerger

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| insertPreTag  | Stringa da includere prima di ogni inserimento. Il valore predefinito è `" "`. Per omettere lo spazio, impostare il valore su `""`.  |
| insertPostTag | Stringa da includere prima di ogni inserimento. Il valore predefinito è `" "`. Per omettere lo spazio, impostare il valore su `""`.  |


##  <a name="sample-input"></a>Input di esempio
Un documento JSON che fornisce input utilizzabile per questa competenza può essere:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Output di esempio
Questo esempio mostra l'output dell'input precedente assumendo che *insertPreTag* sia impostato su `" "` e che *insertPostTag* sia impostato su `""`. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "mergedText": "The quick brown fox jumps over the lazy dog" 
           }
      }
    ]
}
```

## <a name="extended-sample-skillset-definition"></a>Definizione del set di competenze di esempio estesa

Uno scenario comune per l'uso di unione testi è quello di unire la rappresentazione testuale di immagini (testo di una competenza OCR o la didascalia di un'immagine) nel campo del contenuto di un documento. 

Il set di competenze di esempio seguente usa la competenza OCR per estrarre il testo da immagini incorporate nel documento. Successivamente crea un campo *merged_text* per contenere sia il testo originale sia il testo OCR da ogni immagine. 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Util.TextMerger",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " "
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
Nell'esempio precedente si suppone l'esistenza di un campo di immagini normalizzate. Per ottenere un campo di immagini normalizzate impostare la configurazione *imageAction* nella definizione di indicizzatore su *generateNormalizedImages* come illustrato di seguito:

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>Vedere anche 

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Create indexer (REST)](ref-create-indexer.md) (Creare un indicizzatore - REST)