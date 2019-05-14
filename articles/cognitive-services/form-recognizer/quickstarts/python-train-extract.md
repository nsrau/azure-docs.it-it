---
title: "Avvio rapido: Eseguire il training di un modello ed estrarre dati dai moduli usando l'API REST con Python - Riconoscimento modulo"
titleSuffix: Azure Cognitive Services
description: In questa guida di avvio rapido si userà l'API REST di Riconoscimento modulo con Python per eseguire il training di un modello ed estrarre dati dai moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: pafarley
ms.openlocfilehash: 98d1870105038c4314a6b038ec198342bb2ca1d0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025558"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-python"></a>Avvio rapido: Eseguire il training di un modello di Riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con Python

In questa guida di avvio rapido si userà l'API REST di Riconoscimento modulo con Python per eseguire il training e assegnare punteggi ai moduli in modo da estrarre coppie chiave-valore e tabelle.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

-  È necessario avere accesso all'anteprima di Riconoscimento modulo ad accesso limitato. Per ottenere accesso all'anteprima, compilare e inviare il modulo di [richiesta di accesso a Riconoscimento modulo di Servizi cognitivi](https://aka.ms/FormRecognizerRequestAccess). 
- È necessario installare [Python](https://www.python.org/downloads/) se si vuole eseguire l'esempio in locale.
- È necessario avere una chiave di sottoscrizione per Riconoscimento modulo. Per ottenere una chiave di sottoscrizione, vedere [Come ottenere chiavi di sottoscrizione](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
- È necessario avere un set minimo di cinque moduli dello stesso tipo. Per questa guida di avvio rapido, è possibile usare un [set di dati](https://go.microsoft.com/fwlink/?linkid=2090451) di esempio.

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, apportare le modifiche seguenti al frammento di codice seguente:

1. Sostituire il valore di `<subscription_key>` con la chiave di sottoscrizione.
1. Sostituire il valore di `<Endpoint>` con l'URL endpoint per la risorsa Riconoscimento modulo nell'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
1. Sostituire `<SAS URL>` con l'URL della firma di accesso condiviso del contenitore di archiviazione BLOB di Azure in cui si trovano i dati di training.  

    ```python
    ########### Python Form Recognizer Train #############
    from requests import post as http_post

    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    source = r"<SAS URL>"
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<Subscription Key>',
    }
    url = base_url + "/train" 
    body = {"source": source}
    try:
        resp = http_post(url = url, json = body, headers = headers)
        print("Response status code: %d" % resp.status_code)
        print("Response body: %s" % resp.json())
    except Exception as e:
        print(str(e))
    ```
1. Salvare il codice in un file con estensione `.py`. Ad esempio: `form-recognize-train.py`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognize-train.py`.

Si riceverà una risposta `200 (Success)` con l'output JSON seguente:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Prendere nota del valore di `"modelId"`, perché sarà necessario nei passaggi successivi.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Estrarre le coppie chiave-valore e le tabelle dai moduli

A questo punto, analizzare un documento ed estrarre le coppie chiave-valore e le tabelle. Chiamare l'API **Model - Analyze** eseguendo lo script Python seguente. Prima di eseguire il comando, apportare le modifiche seguenti:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la chiave di sottoscrizione di Riconoscimento modulo, disponibile nella scheda di panoramica della risorsa Riconoscimento modulo.
1. Sostituire `<File Path>` con il percorso o l'URL dei file in cui si trova il modulo per l'estrazione dei dati.
1. Sostituire `<modelID>` con l'ID modello ricevuto nel precedente passaggio di training del modello.
1. Sostituire `<file type>` con il tipo di file. I tipi supportati sono pdf, immagine/jpeg, immagine/png.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<File Path>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': 'application/<file type>',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        url = base_url + "/models/" + model_id + "/analyze" 
        with open(file_path, "rb") as f:
            data_bytes = f.read()  
        resp = http_post(url = url, data = data_bytes, headers = headers)
        print("Response status code: %d" % resp.status_code)    
        print("Response body:\n%s" % resp.json())   
    except Exception as e:
        print(str(e))
    ```

1. Salvare il codice in un file con estensione `.py`. Ad esempio: `form-recognize-analyze.py`.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Esaminare i risultati

In JSON viene restituita una risposta di operazione riuscita, che rappresenta le coppie chiave-valore e le tabelle estratte dal modulo.

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida sono state usate le API REST di Riconoscimento modulo con Python per eseguire il training di un modello ed eseguirlo in un caso di esempio. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://aka.ms/form-recognizer/api)
