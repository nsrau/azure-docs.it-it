---
title: 'Avvio rapido: Eseguire il training di un modello ed estrarre dati dai moduli con cURL - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questa guida di avvio rapido si userà l'API REST di Riconoscimento modulo con cURL per eseguire il training di un modello ed estrarre dati dai moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 36f98a8dea2a732a7f8504b160da895637366fc8
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471894"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>Avvio rapido: Eseguire il training di un modello di Riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con cURL

In questa guida di avvio rapido si userà l'API REST di Riconoscimento modulo con cURL per eseguire il training e assegnare punteggi ai moduli in modo da estrarre coppie chiave-valore e tabelle.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* È necessario avere accesso all'anteprima di Riconoscimento modulo ad accesso limitato. Per ottenere accesso all'anteprima, compilare e inviare il modulo di [richiesta di accesso a Riconoscimento modulo di Servizi cognitivi](https://aka.ms/FormRecognizerRequestAccess). 
* È necessario avere [cURL](https://curl.haxx.se/windows/).
* È necessario avere una chiave di sottoscrizione per Riconoscimento modulo. Seguire le istruzioni riportate in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere Riconoscimento modulo e ottenere la chiave.
* È necessario avere un set minimo di cinque moduli dello stesso tipo. Per questo argomento di avvio rapido, è possibile usare un [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451).

## <a name="train-a-form-recognizer-model"></a>Eseguire il training di un modello di Riconoscimento modulo

Prima di tutto, è necessario avere un set di dati di training. È possibile usare i dati di un BLOB di Azure oppure dati di training locali. È necessario avere almeno cinque moduli di esempio (documenti PDF e/o immagini) dello stesso tipo/struttura dei dati di input principali. In alternativa, è possibile usare un singolo modulo vuoto. Il nome file del modulo include il termine "vuoto".

Per eseguire il training di un modello di Riconoscimento modulo usando i documenti del contenitore BLOB di Azure, chiamare l'API **Train** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare le modifiche seguenti:

* Sostituire `<Endpoint>` con l'endpoint ottenuto dalla chiave di sottoscrizione di Riconoscimento modulo, disponibile nella scheda di panoramica della risorsa Riconoscimento modulo.
* Sostituire `<SAS URL>` con l'URL della firma di accesso condiviso del contenitore di archiviazione BLOB di Azure in cui si trovano i dati di training.  
* Sostituire `<subscription key>` con la chiave di sottoscrizione.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

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

Analizzare un documento ed estrarre le coppie chiave-valore e le tabelle. Chiamare l'API **Model - Analyze** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare le modifiche seguenti:

* Sostituire `<Endpoint>` con l'endpoint ottenuto dalla chiave di sottoscrizione di Riconoscimento modulo, disponibile nella scheda **Overview** (panoramica) della risorsa Riconoscimento modulo.
* Sostituire `<modelID>` con l'ID modello ricevuto nel precedente passaggio di training del modello.
* Sostituire `<path to your form>` con il percorso del file del modulo.
* Sostituire `<subscription key>` con la chiave di sottoscrizione.
* Sostituire `<file type>` con il tipo di file. I tipi supportati sono pdf, immagine/jpeg, immagine/png.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

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

In questa guida sono state usate le API REST di Riconoscimento modulo con cURL per eseguire il training di un modello ed eseguirlo in un caso di esempio. A questo punto, vedere la documentazione di riferimento per esplorare l'API Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://aka.ms/form-recognizer/api)
