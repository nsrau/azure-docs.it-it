---
title: Competenza di ricerca cognitiva Analisi delle immagini - Ricerca di Azure
description: Estrarre il testo semantico tramite l'analisi delle immagini usando la competenza cognitiva di analisi delle immagini in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fc8780c5b99ce98a55a6cb08cfaa6585e5a4e89a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313310"
---
#   <a name="image-analysis-cognitive-skill"></a>Competenza cognitiva di Analisi delle immagini

La competenza di **Analisi delle immagini** estrae una vasta gamma di caratteristiche visive in base al contenuto delle immagini. Ad esempio, si può generare una didascalia da un'immagine, generare tag o identificare personaggi famosi e luoghi di interesse.

> [!NOTE]
> A partire dal 21 dicembre 2018 è possibile associare una risorsa di Servizi cognitivi a un set di competenze di Ricerca di Azure. In questo modo sarà possibile iniziare ad addebitare l'esecuzione del set di competenze. Da questa data ha inizio anche l'addebito dell'estrazione di immagini come parte della fase di individuazione dei documenti. L'estrazione di testo dai documenti continuerà a essere offerta gratuitamente.
>
> L'esecuzione delle competenze predefinite verrà addebitata in base ai [prezzi con pagamento in base al consumo di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). Per l'estrazione di immagini verranno applicati i prezzi di anteprima, come illustrato nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400). [Altre informazioni](cognitive-search-attach-cognitive-services.md).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| defaultLanguageCode   |  Stringa che indica la lingua di restituzione. Il servizio restituisce i risultati di riconoscimento nella lingua specificata. Se questo parametro non è specificato, il valore predefinito è "en". <br/><br/>Le lingue supportate sono: <br/>*en* - Inglese (predefinito) <br/> *zh* - Cinese semplificato|
|visualFeatures |   Matrice di stringhe che indica i tipi di caratteristiche visive da restituire. I tipi di caratteristiche visive valide comprendono:  <ul><li> *categorie* - suddivide l'immagine in categorie in base a una tassonomia definita nella [documentazione](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy) Servizi cognitivi.</li><li> *tag* - contrassegna l'immagine con un elenco dettagliato delle parole correlate con il contenuto dell'immagine.</li><li>*Descrizione* - descrive il contenuto dell'immagine con una frase in lingua inglese completa.</li><li>*Visi* - rileva se sono presenti visi. Se presenti, genera le coordinate, il genere e l'età.</li><li> *ImageType* - rileva se l'immagine è clipart o un disegno.</li><li>   *Colore* - determina il colore principale, il colore dominante e se un'immagine è in bianco e nero.</li><li>*Adulti* - rileva se l'immagine è di natura pornografica (raffigura un'immagine di nudo o un atto sessuale). Viene rilevato anche il contenuto che presenta riferimenti sessuali.</li></ul> I nomi delle caratteristiche visive fanno distinzione tra maiuscole e minuscole.|
| informazioni dettagliate   | Matrice di stringhe che indica quali dettagli specifici del dominio restituire. I tipi di caratteristiche visive valide comprendono: <ul><li>*Personaggi famosi* - identifica eventuali personaggi famosi rilevati nell'immagine.</li><li>*Luoghi di interesse* - identifica eventuali luoghi di interesse rilevati nell'immagine.</li></ul>
 |

## <a name="skill-inputs"></a>Input competenze

| Nome input      | DESCRIZIONE                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complesso. Attualmente funziona solo con il campo "/document/normalized_images", prodotto dall'indicizzatore di BLOB di Azure quando ```imageAction``` è impostato su ```generateNormalizedImages```. Per altre informazioni, vedere [esempio](#sample-output).|



##  <a name="sample-definition"></a>Definizione di esempio
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Input di esempio

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {                
                "image":  {
                               "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                               "width": 500,
                               "height": 300,
                               "originalWidth": 5000,  
                               "originalHeight": 3000,
                               "rotationFromOriginal": 90,
                               "contentOffset": 500  
                           }
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Output di esempio

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Casi di errore
Nei seguenti casi di errore, non viene estratto nessun elemento.

| Codice di errore | DESCRIZIONE |
|------------|-------------|
| NotSupportedLanguage | La lingua fornita non è supportata. |
| InvalidImageUrl | L'URL dell'immagine è formattato in modo errato o non è accessibile.|
| InvalidImageFormat | I dati di input non sono un'immagine valida. |
| InvalidImageSize | L'immagine di input è troppo grande. |
| NotSupportedVisualFeature  | Il tipo di caratteristica specificato non è valido. |
| NotSupportedImage | Immagine non supportata, ad esempio, contenuti pedopornografici. |
| InvalidDetails | Modello specifico del dominio non supportato. |

## <a name="see-also"></a>Vedere anche 

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Create indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) (Creare un indicizzatore - REST)
