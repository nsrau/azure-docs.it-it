---
title: "Guida introduttiva: Eseguire il training con le etichette usando l'API REST e Python - Riconoscimento modulo"
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la funzionalità dei dati etichettati di Riconoscimento modulo con l'API REST e Python per eseguire il training di un modello personalizzato.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 36ded3bd85cd7acdffbfe46b9e931a811994fa30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531101"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Eseguire il training di un modello di Riconoscimento modulo con le etichette usando l'API REST e Python

In questo argomento di avvio rapido si userà l'API REST Riconoscimento modulo con Python per eseguire il training di un modello personalizzato con dati etichettati manualmente. Per altre informazioni su questa funzionalità, vedere la sezione [Eseguire il training con le etichette](../overview.md#train-with-labels) della panoramica.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- [Python](https://www.python.org/downloads/) installato, se si vuole eseguire l'esempio in locale.
- Un set di almeno sei moduli dello stesso tipo. Questi dati verranno usati per eseguire il training del modello e testare un modulo. Per questa guida di avvio rapido, è possibile usare un [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451). Caricare i file di training nella radice di un contenitore di archiviazione BLOB in un account di archiviazione di Azure.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Configurare i dati di training

È quindi necessario configurare i dati di input necessari. La funzionalità dei dati etichettati prevede requisiti di input specifici oltre a quelli necessari per eseguire il training di un modello personalizzato. 

Assicurarsi che tutti i documenti di training abbiano lo stesso formato. Se si usano moduli in più formati, organizzarli in sottocartelle basate sul formato comune. Quando si esegue il training, è necessario indirizzare l'API a una sottocartella.

Per eseguire il training di un modello usando i dati etichettati, saranno necessari i file seguenti come input nella sottocartella. Verrà descritto come creare questi file.

* **Moduli di origine**: moduli da cui estrarre i dati. I tipi supportati sono JPEG, PNG, PDF o TIFF.
* **File di layout OCR**: file JSON che descrivono le dimensioni e le posizioni di tutto il testo leggibile in ogni modulo di origine. Per generare questi dati verrà usata l'API di layout di Riconoscimento modulo. 
* **File di etichette**: file JSON che descrivono le etichette dati immesse manualmente da un utente.

Tutti questi file devono trovarsi nella stessa sottocartella e avere il formato seguente:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Quando si etichettano i moduli con lo [strumento di etichettatura campioni](./label-tool.md) di Riconoscimento modulo, questi file vengono creati automaticamente.

### <a name="create-the-ocr-output-files"></a>Creare i file di output OCR

I file dei risultati dell'OCR sono necessari perché il servizio consideri i file di input corrispondenti per il training con etichette. Per ottenere i risultati dell'OCR per un modulo di origine specificato, seguire questa procedura:

1. Chiamare l'API **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** nel contenitore di layout di lettura con il file di input come parte del corpo della richiesta. Salvare l'ID trovato nell'intestazione **Operation-location** della risposta.
1. Chiamare l'API **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** usando l'ID operazione del passaggio precedente.
1. Ottenere la risposta e scrivere il contenuto in un file. Per ogni modulo di origine, il file OCR corrispondente deve avere il nome file originale con l'aggiunta di `.ocr.json`. L'output JSON dell'OCR dovrà avere il formato seguente. Per un esempio completo, vedere il [file OCR di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json). 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>Creare i file di etichette

I file di etichette contengono associazioni chiave-valore immesse manualmente da un utente. Sono necessari per il training dei dati etichettati, ma non è necessario che ogni file di origine abbia un file di etichette corrispondente. I file di origine senza etichette verranno considerati come normali documenti di training. Per un training affidabile, è consigliabile usare almeno cinque file di etichette.

Quando si crea un file di etichette, è possibile specificare facoltativamente le aree, ovvero le posizioni esatte dei valori nel documento. In questo modo si otterrà una maggiore accuratezza del training. Le aree vengono formattate come un set di otto valori corrispondenti a quattro coordinate X,Y: in alto a sinistra, in alto a destra, in basso a destra e in basso a sinistra. I valori delle coordinate sono compresi tra zero e uno, rapportati alle dimensioni della pagina.

Per ogni modulo di origine, il file di etichette corrispondente deve avere il nome file originale con l'aggiunta di `.labels.json`. Il file di etichette dovrà avere il formato seguente. Per un esempio completo, vedere il [file di etichette di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json).

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!NOTE]
> È possibile applicare un'unica etichetta a ogni elemento di testo e ogni etichetta può essere applicata solo una volta per pagina. Attualmente non è possibile applicare un'etichetta tra più pagine.


## <a name="train-a-model-using-labeled-data"></a>Eseguire il training di un modello usando dati etichettati

Per eseguire il training di un modello con dati etichettati, chiamare l'API **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** eseguendo il codice Python seguente. Prima di eseguire il codice, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'URL dell'endpoint relativo alla risorsa di Riconoscimento modulo.
1. Sostituire `<SAS URL>` con l'URL della firma di accesso condiviso (SAS) del contenitore di archiviazione BLOB di Azure. Per recuperare l'URL SAS, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. Sostituire `<Blob folder name>` con il nome della cartella nel contenitore BLOB in cui si trovano i dati di input. In alternativa, se i dati si trovano nella directory radice, lasciarlo vuoto e rimuovere il campo `"prefix"` dal corpo della richiesta HTTP.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```

## <a name="get-training-results"></a>Ottenere i risultati del training

Dopo aver avviato il training, usare l'ID restituito per ottenere lo stato dell'operazione. Aggiungere il codice seguente in fondo allo script Python. Viene usato il valore ID della chiamata di training in una nuova chiamata API. L'operazione di training è asincrona, per cui questo script chiama l'API a intervalli regolari finché lo stato del training non viene completato. Si consiglia un intervallo di almeno un secondo.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Al termine del processo di training, si riceverà una risposta `201 (Success)` con contenuto JSON simile al seguente. La risposta è stata abbreviata per semplicità.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

Copiare il valore di `"modelId"` per usarlo nei passaggi seguenti.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Al termine del processo, si riceverà una risposta `202 (Success)` con contenuto JSON nel formato seguente. La risposta è stata abbreviata per semplicità. Le associazioni chiave-valore principali si trovano nel nodo `"documentResults"`. I risultati dell'API di layout (il contenuto e le posizioni di tutto il testo nel documento) si trovano nel nodo `"readResults"`.

```json
{ 
    "analyzeResult":{ 
      "version":"2.0.0",
      "readResults":[ 
        { 
          "page":1,
          "language":"en",
          "angle":0,
          "width":8.5,
          "height":11,
          "unit":"inch",
          "lines":[ 
            { 
              "language":"en",
              "boundingBox":[ 
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
          ]
        }
      ],
      "pageResults":[ 
        { 
          "page":1,
          "tables":[ 
            { 
              "rows":2,
              "columns":6,
              "cells":[ 
                { 
                  "rowIndex":0,
                  "columnIndex":0,
                  "text":"Invoice Number",
                  "boundingBox":[ 
                    0.5075,
                    2.8088,
                    1.9061,
                    2.8088,
                    1.9061,
                    3.3219,
                    0.5075,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
                    3.3219
                  ],
                  "elements":[ 
                    "#/readResults/0/lines/8/words/0",
                    "#/readResults/0/lines/8/words/1"
                  ]
                },
                ...        
              ]
            }
          ]
        }
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/15/words/0"
                }
              ],
              "fieldName":"total"
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/12/words/0"
                }
              ],
              "fieldName":"invoice #"
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/16/words/0"
                }
              ],
              "fieldName":"vat"
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Migliorare i risultati

Esaminare i valori di `"confidence"` per ogni risultato di chiave-valore nel nodo `"documentResults"`. È inoltre consigliabile esaminare i punteggi di attendibilità nel nodo `"readResults"`, che corrispondono all'operazione di layout. L'attendibilità dei risultati del layout non influisce su quella dei risultati dell'estrazione di coppie chiave-valore, quindi è necessario controllare entrambi.
* Se i punteggi di attendibilità dell'operazione di layout sono bassi, provare a migliorare la qualità dei documenti di input. Vedere [Requisiti di input](../overview.md#input-requirements).
* Se i punteggi di attendibilità per l'operazione di estrazione di coppie chiave-valore sono bassi, assicurarsi che i documenti da analizzare siano dello stesso tipo di quelli usati nel set di training. Se i documenti del set di training hanno aspetti diversi, è consigliabile dividerli in cartelle diverse ed eseguire il training di un modello per ogni variante.

### <a name="avoid-cluttered-labels"></a>Evitare etichette con molto contenuto

In alcuni casi, quando si applicano etichette diverse all'interno della stessa riga di testo, il servizio può unirle in un unico campo. Ad esempio, in un indirizzo è possibile etichettare la città, la provincia e il codice postale come campi diversi, ma durante la previsione questi campi non vengono riconosciuti separatamente.

Questo scenario è essenziale per i nostri clienti e stiamo lavorando per migliorarlo in futuro. Attualmente, è consigliabile etichettare più campi con molto contenuto come un unico campo e quindi separare i termini in una post-elaborazione dei risultati dell'estrazione.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come usare l'API REST Riconoscimento modulo con Python per eseguire il training di un modello personalizzato con dati etichettati manualmente. Successivamente, vedere la [documentazione di riferimento delle API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) per esplorare l'API Riconoscimento modulo in maggior dettaglio.
