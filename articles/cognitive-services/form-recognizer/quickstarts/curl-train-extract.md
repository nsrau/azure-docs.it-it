---
title: 'Guida introduttiva: Eseguire il training di un modello ed estrarre dati dai moduli con cURL - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà l'API REST di riconoscimento modulo con cURL per eseguire il training di un modello ed estrarre dati dai moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 098dc5e2ab7d4b9533f58e03557db533eaa49a90
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931287"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Guida introduttiva: Eseguire il training di un modello di riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con cURL

In questo argomento di avvio rapido si userà l'API REST di riconoscimento modulo di Azure con cURL per eseguire il training e assegnare punteggi ai moduli in modo da estrarre coppie chiave-valore e tabelle.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- Accesso all'anteprima dell'API di riconoscimento modulo ad accesso limitato. Per avere accesso all'anteprima, completare e inviare il modulo di [richiesta di accesso al riconoscimento modulo](https://aka.ms/FormRecognizerRequestAccess).
- [cURL](https://curl.haxx.se/windows/) installato.
- Un set di almeno cinque moduli dello stesso tipo. Questi dati verranno usati per eseguire il training del modello. Per questa guida di avvio rapido, è possibile usare un [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451). Caricare i file di training nella radice di un contenitore di archiviazione BLOB in un account di archiviazione di Azure.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Eseguire il training di un modello di Riconoscimento modulo

È prima di tutto necessario un set di dati di training in un BLOB del servizio di archiviazione di Azure. È necessario avere almeno cinque moduli compilati (documenti PDF e/o immagini) dello stesso tipo/struttura dei dati di input principali. In alternativa, è possibile usare un singolo modulo vuoto con due moduli compilati. Il nome file del modulo vuoto deve includere la parola "empty". Consultare [Compilare un training set per un modello personalizzato](../build-training-data-set.md) per suggerimenti e opzioni per la creazione di dati di training.

Per eseguire il training di un modello di riconoscimento modulo con i documenti del contenitore BLOB di Azure, chiamare l'API **Train** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `<SAS URL>` con l'URL della firma di accesso condiviso (SAS) del contenitore di archiviazione BLOB di Azure. Per recuperare l'URL SAS, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

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

Prendere nota del valore di `"modelId"`. Sarà necessario nei passaggi successivi.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Estrarre le coppie chiave-valore e le tabelle dai moduli

A questo punto, si analizzerà un documento e si estrarranno le coppie chiave-valore e le tabelle. Chiamare l'API **Model - Analyze** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto dalla sottoscrizione di riconoscimento modulo.
1. Sostituire `<modelID>` con l'ID modello ricevuto nella sezione precedente.
1. Sostituire `<path to your form>` con il percorso del file del proprio modulo (ad esempio, C:\temp\file.pdf). Per questo argomento di avvio rapido, è possibile usare i file inclusi nella cartella **Test** del [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Sostituire `<file type>` con il tipo di file. Tipi supportati: `application/pdf`, `image/jpeg`, `image/png`.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Esaminare i risultati

Viene restituita una risposta con esito positivo in formato JSON. Rappresenta le coppie chiave-valore e le tabelle estratte dal modulo:

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

In questo argomento di avvio rapido è stata usata l'API REST di riconoscimento modulo con cURL per eseguire il training di un modello e quindi eseguire il modello in uno scenario di esempio. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://aka.ms/form-recognizer/api)
