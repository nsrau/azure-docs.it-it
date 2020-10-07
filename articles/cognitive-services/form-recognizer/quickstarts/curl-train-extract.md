---
title: 'Guida introduttiva: Eseguire il training di un modello ed estrarre dati dai moduli con cURL - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà l'API REST di riconoscimento modulo con cURL per eseguire il training di un modello ed estrarre dati dai moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: b0532007ff03cd9dcf253824a1158fd0b8661120
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760462"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Guida introduttiva: Eseguire il training di un modello di riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con cURL

In questo argomento di avvio rapido si userà l'API REST di riconoscimento modulo di Azure con cURL per eseguire il training e assegnare punteggi ai moduli in modo da estrarre coppie chiave-valore e tabelle.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- [cURL](https://curl.haxx.se/windows/) installato.
- Un set di almeno sei moduli dello stesso tipo. Cinque di questi moduli verranno usati per eseguire il training del modello, che quindi verrà testato con il sesto. I moduli possono essere di tipi di file diversi, ma devono essere dello stesso tipo di documento. Per questa guida di avvio rapido, è possibile usare un [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451). Caricare i file di training nella radice di un contenitore di archiviazione BLOB in un account di archiviazione di Azure con livello di prestazioni Standard. È possibile inserire i file di test in una cartella separata.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Eseguire il training di un modello di Riconoscimento modulo

È prima di tutto necessario un set di dati di training in un BLOB del servizio di archiviazione di Azure. È necessario avere almeno cinque moduli compilati (documenti PDF e/o immagini) dello stesso tipo/struttura dei dati di input principali. In alternativa, è possibile usare un singolo modulo vuoto con due moduli compilati. Il nome file del modulo vuoto deve includere la parola "empty". Consultare [Compilare un training set per un modello personalizzato](../build-training-data-set.md) per suggerimenti e opzioni per la creazione di dati di training.

> [!NOTE]
> È possibile usare la funzionalità dei dati etichettati per applicare manualmente le etichette ad alcuni o a tutti i dati di training in anticipo. Si tratta di una procedura più complessa, ma il risultato è un training del modello più efficace. Per altre informazioni su questa funzionalità, vedere la sezione [Eseguire il training con le etichette](../overview.md#train-with-labels) della panoramica.



Per eseguire il training di un modello di Riconoscimento modulo con i documenti del contenitore BLOB di Azure, chiamare l'API **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `<SAS URL>` con l'URL della firma di accesso condiviso (SAS) del contenitore di archiviazione BLOB di Azure. Per recuperare l'URL SAS, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



Si riceverà una risposta `201 (Success)` con l'intestazione **Location**. Il valore di questa intestazione corrisponde all'ID del nuovo modello da sottoporre a training.

## <a name="get-training-results"></a>Ottenere i risultati del training

Dopo aver avviato il training, usare una nuova operazione, **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** , per verificare lo stato dell'operazione. Passare l'ID modello in questa chiamata API per controllare lo stato del training:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la chiave di sottoscrizione di riconoscimento modulo,
1. Sostituire `<subscription key>` con la chiave di sottoscrizione
1. Sostituire `<model ID>` con l'ID modello ricevuto nel passaggio precedente



# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
```
    
---

Si riceverà una risposta `200 (Success)` con un corpo JSON nel formato seguente. Osservare il campo `"status"`. Al termine del training, avrà il valore `"ready"`. Se il training del modello non è stato completato, è necessario eseguire di nuovo una query sul servizio ripetendo il comando. Si consiglia di attendere almeno un secondo tra le chiamate.

Il campo `"modelId"` contiene l'ID del modello da sottoporre a training. Servirà per il passaggio successivo.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analizzare i moduli per trovare le coppie chiave-valore e le tabelle

Quindi, usare il modello appena sottoposto a training per analizzare un documento da cui estrarre le coppie chiave-valore e le tabelle. Chiamare l'API **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto dalla chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `<model ID>` con l'ID modello ricevuto nella sezione precedente.
1. Sostituire `<SAS URL>` con un URL di firma di accesso condiviso per il file in archiviazione di Azure. Seguire la procedura decritta nella sezione Training, ma invece di ottenere un URL di firma di accesso condiviso per l'intero contenitore BLOB ottenerne uno per il file specifico da analizzare.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



Si riceverà una risposta `202 (Success)` che include l'intestazione **Operation-Location**. Il valore di questa intestazione include un ID risultati da usare per tenere traccia dei risultati dell'operazione di analisi. Salvare questo ID risultati per il passaggio successivo.

## <a name="get-the-analyze-results"></a>Ottenere i risultati dell'analisi

Usare l'API seguente per eseguire una query sui risultati dell'operazione di analisi.

1. Sostituire `<Endpoint>` con l'endpoint ottenuto dalla chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `<result ID>` con l'ID ricevuto nella sezione precedente.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
    
---

Si riceverà una risposta `200 (Success)` con un corpo JSON nel formato seguente. L'output è stato abbreviato per semplicità. Osservare il campo `"status"` verso la fine. Al termine dell'operazione di analisi, avrà il valore `"succeeded"`. Se l'operazione di analisi non è stata completata, è necessario eseguire di nuovo una query sul servizio ripetendo il comando. Si consiglia di attendere almeno un secondo tra le chiamate.

Le tabelle e le associazioni chiave-valore principali si trovano nel nodo `"pageResults"`. Se è stata specificata anche l'estrazione di testo normale tramite il parametro URL *includeTextDetails*, il nodo `"readResults"` mostrerà il contenuto e le posizioni di tutto il testo nel documento.

Questo output JSON di esempio è stato abbreviato per semplicità.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v2.1.preview](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 

---


## <a name="improve-results"></a>Migliorare i risultati

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata l'API REST di riconoscimento modulo con cURL per eseguire il training di un modello e quindi eseguire il modello in uno scenario di esempio. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
