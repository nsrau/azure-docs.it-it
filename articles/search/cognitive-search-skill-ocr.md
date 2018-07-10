---
title: Competenza OCR della ricerca cognitiva (Ricerca di Azure) | Microsoft Docs
description: Estrarre il testo dai file di immagine in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 478afe81ed739b98487973eb092ee9cad0aa17fd
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055686"
---
# <a name="ocr-cognitive-skill"></a>Competenza cognitiva OCR

La competenza **OCR** estrae il testo dai file di immagine. I formati di file supportati comprendono:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF


## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| detectOrientation | Abilita il rilevamento automatico dell'orientamento dell'immagine. <br/> Valori validi: true / false.|
|defaultLanguageCode | <p>  Codice lingua del testo di input. Le lingue supportate comprendono: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (ceco) <br/>da (danese) <br/>nl (olandese) <br/>en (inglese) <br/>fi (finlandese)  <br/>fr (francese) <br/>  de (tedesco) <br/>el (greco) <br/> hu (ungherese) <br/> it (italiano) <br/>  ja (giapponese) <br/> ko (coreano) <br/> nb (norvegese) <br/>   pl (polacco) <br/> pt (portoghese) <br/>  ru (russo) <br/>  es (spagnolo) <br/>  sv (svedese) <br/>  tr (turco) <br/> ar (arabo) <br/> ro (romeno) <br/> sr-Cyrl (SerbianCyrillic) <br/> sr-Latn (SerbianLatin) <br/>  sk (slovacco). <br/>  unk (sconosciuto) <br/><br/> Se il codice lingua non è specificato o è Null, la lingua viene rilevata automaticamente. </p> |
| textExtractionAlgorithm | "stampato" o "scritto a mano". L'algoritmo OCR per il riconoscimento del testo "scritto a mano" è attualmente in anteprima e supportato solo in inglese. |

## <a name="skill-inputs"></a>Input competenze

| Nome input      | DESCRIZIONE                                          |
|---------------|------------------------------------------------------|
| immagine         | Tipo complesso. Attualmente funziona solo con il campo "/document/normalized_images", prodotto dall'indicizzatore di BLOB di Azure quando ```imageAction``` è impostato su ```generateNormalizedImages```. Per altre informazioni, vedere [esempio](#sample-output).|


## <a name="skill-outputs"></a>Output competenze
| Nome output     | DESCRIZIONE                   |
|---------------|-------------------------------|
| text          | Testo normale estratto dall'immagine.   |
| layoutText    | Tipo complesso che descrive il testo estratto, nonché la posizione in cui è stato trovato il testo.|


## <a name="sample-definition"></a>Definizione di esempio

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Esempio di output di testo e layoutText

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Esempio: unione del testo estratto da immagini incorporate con il contenuto del documento.

Un caso d'uso comune per unione testi è la capacità di unire la rappresentazione testuale di immagini (testo di una competenza OCR o la didascalia di un'immagine) nel campo del contenuto di un documento. 

L'insieme di competenze di esempio seguente crea un campo *merged_text* per contenere il contenuto testuale del documento, nonché il testo OCR di ciascuna delle immagini incorporate in quel documento. 

#### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta
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
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
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
Nell'esempio dell'insieme di competenze precedente si suppone l'esistenza di un campo di immagini normalizzate. Per generare questo campo, impostare la configurazione *imageAction* nella definizione di indicizzatore su *generateNormalizedImages* come illustrato di seguito:

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
+ [Competenza TextMerger](cognitive-search-skill-textmerger.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Create indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) (Creare un indicizzatore - REST)
