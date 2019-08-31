---
title: Competenza di ricerca cognitiva Analisi delle immagini - Ricerca di Azure
description: Estrarre il testo semantico tramite l'analisi delle immagini usando la competenza cognitiva di analisi delle immagini in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 07969a92286837f8f72b533173ac8d170378d0ea
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183393"
---
#   <a name="image-analysis-cognitive-skill"></a>Competenza cognitiva di Analisi delle immagini

La competenza di **Analisi delle immagini** estrae una vasta gamma di caratteristiche visive in base al contenuto delle immagini. Ad esempio, si può generare una didascalia da un'immagine, generare tag o identificare personaggi famosi e luoghi di interesse. Questa competenza usa i modelli di Machine Learning forniti da [Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) in Servizi cognitivi. 

> [!NOTE]
> In ricerca di Azure è possibile eseguire gratuitamente volumi piccoli (in 20 transazioni), ma i carichi di lavoro di dimensioni maggiori richiedono [il fissaggio di una risorsa di servizi cognitivi fatturabile](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| defaultLanguageCode   |  Stringa che indica la lingua di restituzione. Il servizio restituisce i risultati di riconoscimento nella lingua specificata. Se questo parametro non è specificato, il valore predefinito è "en". <br/><br/>Le lingue supportate sono: <br/>*en* - Inglese (predefinito) <br/> *zh* - Cinese semplificato|
|visualFeatures |   Matrice di stringhe che indica i tipi di caratteristiche visive da restituire. I tipi di caratteristiche visive valide comprendono:  <ul><li> *categorie* : Categorizza il contenuto dell'immagine in base a una tassonomia definita nella documentazione dei servizi cognitivi [visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy). </li><li> *tag* - contrassegna l'immagine con un elenco dettagliato delle parole correlate con il contenuto dell'immagine.</li><li>*Descrizione* : descrive il contenuto dell'immagine con una frase inglese completa.</li><li>*visi* : rileva se sono presenti visi. Se presenti, genera le coordinate, il genere e l'età.</li><li>    *ImageType* : rileva se image è clip art o un disegno a linee.</li><li>  *colore* : determina il colore dell'accento, il colore dominante e se un'immagine è nera & bianco.</li><li>*Adult* : rileva se l'immagine è di natura pornografica (raffigurato nudità o un sex Act). Viene rilevato anche il contenuto che presenta riferimenti sessuali.</li></ul> I nomi delle caratteristiche visive fanno distinzione tra maiuscole e minuscole.|
| details   | Matrice di stringhe che indica quali dettagli specifici del dominio restituire. I tipi di caratteristiche visive valide comprendono: <ul><li>*celebrità* : identifica le celebrità se rilevate nell'immagine.</li><li>*punti di riferimento* : identifica i punti di riferimento se vengono rilevati nell'immagine. </li></ul> |

## <a name="skill-inputs"></a>Input competenze

| Nome di input      | Descrizione                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complesso. Attualmente funziona solo con il campo "/document/normalized_images", prodotto dall'indicizzatore di BLOB di Azure quando ```imageAction``` è impostato su un valore diverso da ```none```. Per altre informazioni, vedere [esempio](#sample-output).|



##  <a name="sample-skill-definition"></a>Definizione di competenze di esempio

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "Tags",
                "Categories",
                "Description",
                "Faces"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Indice di esempio (solo per le categorie, i campi Descrizione, visi e tag)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Esempio di mapping dei campi di output (per l'indice precedente)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Variazione nei mapping dei campi di output (proprietà nidificate)

È possibile definire i mapping dei campi di output a proprietà di basso livello, ad esempio solo punti di riferimento o celebrità. In questo caso, assicurarsi che lo schema dell'indice includa un campo che contenga i punti di riferimento in modo specifico.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": /document/normalized_images/*/categories/details/landmarks/*",
            "targetFieldName": "landmarks"
        }
```
##  <a name="sample-input"></a>Input di esempio

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Esempio di output

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
                                    "faceBoundingBox": [
                                        {
                                            "x": 273,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 431
                                        },
                                        {
                                            "x": 273,
                                            "y": 431
                                        }
                                    ],
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
                        "faceBoundingBox": [
                            {
                                "x": 1601,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 447
                            },
                            {
                                "x": 1601,
                                "y": 447
                            }
                        ]
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

| Codice di errore | Descrizione |
|------------|-------------|
| NotSupportedLanguage | La lingua fornita non è supportata. |
| InvalidImageUrl | L'URL dell'immagine è formattato in modo errato o non è accessibile.|
| InvalidImageFormat | I dati di input non sono un'immagine valida. |
| InvalidImageSize | L'immagine di input è troppo grande. |
| NotSupportedVisualFeature  | Il tipo di caratteristica specificato non è valido. |
| NotSupportedImage | Immagine non supportata, ad esempio, contenuti pedopornografici. |
| InvalidDetails | Modello specifico del dominio non supportato. |

Se si ottiene l'errore simile a `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`, controllare il percorso. Sia le celebrità che i punti di riferimento `detail`sono proprietà in.

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Create indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) (Creare un indicizzatore - REST)
