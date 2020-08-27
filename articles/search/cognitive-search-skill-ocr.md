---
title: Competenza cognitiva OCR
titleSuffix: Azure Cognitive Search
description: Estrarre il testo dai file di immagine usando il riconoscimento ottico dei caratteri (OCR) in una pipeline di arricchimento in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 56e3c1e48df51cd24d70517ee23d47ef27d378d7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935331"
---
# <a name="ocr-cognitive-skill"></a>Competenza cognitiva OCR

Il **riconoscimento ottico dei caratteri (OCR)** riconosce il testo stampato e scritto a mano nei file di immagine. Questa competenza usa i modelli di apprendimento automatico forniti da [visione artificiale](../cognitive-services/computer-vision/home.md) API [v 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) in Servizi cognitivi. La competenza **OCR** corrisponde alle funzionalità seguenti:

+ Per inglese, spagnolo, tedesco, francese, italiano, portoghese e olandese, viene usata la nuova API ["Read"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) .
+ Per tutti gli altri linguaggi, viene usata l'API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-api) .

La competenza **OCR** estrae il testo dai file di immagine. I formati di file supportati comprendono:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ . TIFF

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | Descrizione |
|--------------------|-------------|
| `detectOrientation`   | Abilita il rilevamento automatico dell'orientamento dell'immagine. <br/> Valori validi: true / false.|
| `defaultLanguageCode` | <p>   Codice lingua del testo di input. Le lingue supportate comprendono: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (ceco) <br/>da (danese) <br/>nl (olandese) <br/>en (inglese) <br/>fi (finlandese)  <br/>fr (francese) <br/>  de (tedesco) <br/>el (greco) <br/> hu (ungherese) <br/> it (italiano) <br/>  ja (giapponese) <br/> ko (coreano) <br/> nb (norvegese) <br/>   pl (polacco) <br/> pt (portoghese) <br/>  ru (russo) <br/>  es (spagnolo) <br/>  sv (svedese) <br/>  tr (turco) <br/> ar (arabo) <br/> ro (romeno) <br/> sr-Cyrl (SerbianCyrillic) <br/> sr-Latn (SerbianLatin) <br/>  SK (Slovacco) <br/>  unk (sconosciuto) <br/><br/> Se il codice lingua non è specificato o è Null, la lingua verrà impostata sull'inglese. Se la lingua è impostata esplicitamente su "unk", la lingua verrà rilevata automaticamente. </p> |
| `lineEnding` | Valore da utilizzare tra ogni riga rilevata. Valori possibili: "Space", "CarriageReturn", "avanzamento riga".  Il valore predefinito è "Space". |

In precedenza era presente un parametro denominato "textExtractionAlgorithm" per specificare se l'abilità deve estrarre il testo "stampato" o "scritto a mano".  Questo parametro è deprecato e non è più necessario perché l'algoritmo più recente dell'API Read è in grado di estrarre entrambi i tipi di testo in una sola volta.  Se la definizione delle competenze include già questo parametro, non è necessario rimuoverlo, ma non verrà più usato ed entrambi i tipi di testo verranno estratti in futuro indipendentemente dall'impostazione.

## <a name="skill-inputs"></a>Input competenze

| Nome input      | Descrizione                                          |
|---------------|------------------------------------------------------|
| `image`         | Tipo complesso. Attualmente funziona solo con il campo "/document/normalized_images", prodotto dall'indicizzatore di BLOB di Azure quando ```imageAction``` è impostato su un valore diverso da ```none```. Per altre informazioni, vedere [esempio](#sample-output).|


## <a name="skill-outputs"></a>Output competenze
| Nome output     | Descrizione                   |
|---------------|-------------------------------|
| `text`            | Testo normale estratto dall'immagine.   |
| `layoutText`    | Tipo complesso che descrive il testo estratto e la posizione in cui è stato trovato il testo.|


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

Il set di competenze di esempio seguente crea un campo *merged_text*. Questo campo contiene il contenuto testuale del documento e il testo sottoposto a riconoscimento dei caratteri da ognuna delle immagini incorporate in tale documento.

#### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
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
          "name":"text",
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
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
  "parameters": {
    "configuration": {
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
+ [Creare un indicizzatore (REST)](/rest/api/searchservice/create-indexer)