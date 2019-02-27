---
title: Competenza OCR della ricerca cognitiva - Ricerca di Azure
description: Estrarre il testo da file di immagine usando il riconoscimento ottico dei caratteri (OCR) in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 6fa316452b5b9f56c33de00b96c2cf57c40edfcc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429656"
---
# <a name="ocr-cognitive-skill"></a>Competenza cognitiva OCR

La competenza Riconoscimento ottico dei caratteri (OCR) riconosce testo stampato e scritto a mano nei file di immagine. Questa competenza usa i modelli di Machine Learning forniti da [Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) in Servizi cognitivi. La competenza **OCR** corrisponde alle funzionalità seguenti:

+ Quando il parametro textExtractionAlgorithm è impostato su "handwritten", viene utilizzata la funzionalità ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md).
+ Quando il parametro textExtractionAlgorithm è impostato su "printed", per le lingue diverse dall'inglese viene usata la funzionalità ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md). Per l'inglese viene usata la nuova funzionalità di ["riconoscimento del testo"](../cognitive-services/computer-vision/concept-recognizing-text.md) per il testo stampato.

La competenza **OCR** estrae il testo dai file di immagine. I formati di file supportati comprendono:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF

> [!NOTE]
> Dal 21 dicembre 2018 è possibile [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) a un set di competenze di Ricerca di Azure. Ciò consente anche di addebitare l'esecuzione del set di competenze. In questa data è iniziato anche l'addebito dell'estrazione delle immagini come parte della fase di individuazione dei documenti. L'estrazione di testo dai documenti continua a essere offerta gratuitamente.
>
> L'esecuzione delle [competenze cognitive predefinite](cognitive-search-predefined-skills.md) viene addebitata in base ai [prezzi con pagamento a consumo di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services), alla stessa tariffa che verrebbe usata se fosse stata eseguita l'attività direttamente. L'estrazione di immagini è un addebito previsto in Ricerca di Azure, attualmente offerto al prezzo di anteprima. Per informazioni dettagliate, vedere la [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400) oppure [Come funziona la fatturazione](search-sku-tier.md#how-billing-works).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| detectOrientation | Abilita il rilevamento automatico dell'orientamento dell'immagine. <br/> Valori validi: true / false.|
|defaultLanguageCode | <p>  Codice lingua del testo di input. Le lingue supportate comprendono: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (ceco) <br/>da (danese) <br/>nl (olandese) <br/>en (inglese) <br/>fi (finlandese)  <br/>fr (francese) <br/>  de (tedesco) <br/>el (greco) <br/> hu (ungherese) <br/> it (italiano) <br/>  ja (giapponese) <br/> ko (coreano) <br/> nb (norvegese) <br/>   pl (polacco) <br/> pt (portoghese) <br/>  ru (russo) <br/>  es (spagnolo) <br/>  sv (svedese) <br/>  tr (turco) <br/> ar (arabo) <br/> ro (romeno) <br/> sr-Cyrl (SerbianCyrillic) <br/> sr-Latn (SerbianLatin) <br/>  sk (slovacco). <br/>  unk (sconosciuto) <br/><br/> Se il codice lingua non è specificato o è Null, la lingua verrà impostata sull'inglese. Se la lingua è impostata esplicitamente su "unk", la lingua verrà rilevata automaticamente. </p> |
| textExtractionAlgorithm | "stampato" o "scritto a mano". L'algoritmo OCR per il riconoscimento del testo "scritto a mano" è attualmente in anteprima e supportato solo in inglese. |

## <a name="skill-inputs"></a>Input competenze

| Nome input      | DESCRIZIONE                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complesso. Attualmente funziona solo con il campo "/document/normalized_images", prodotto dall'indicizzatore di BLOB di Azure quando ```imageAction``` è impostato su un valore diverso da ```none```. Per altre informazioni, vedere [esempio](#sample-output).|


## <a name="skill-outputs"></a>Output competenze
| Nome output     | DESCRIZIONE                   |
|---------------|-------------------------------|
| text          | Testo normale estratto dall'immagine.   |
| layoutText    | Tipo complesso che descrive il testo estratto e la posizione in cui è stato trovato il testo.|


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
          "name": "mergedText", "targetName" : "merged_text"
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
