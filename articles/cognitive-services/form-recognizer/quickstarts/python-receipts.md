---
title: 'Guida introduttiva: Estrarre i dati delle ricevute usando Python - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà l'API REST di Riconoscimento modulo con Python per estrarre i dati dalle immagini delle ricevute di vendita.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: ef5c9e8d548e8acbcbdbe83f6e7c9965c798ad44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931240"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Guida introduttiva: Estrarre i dati delle ricevute usando l'API REST di Riconoscimento modulo con Python

In questo argomento di avvio rapido si userà l'API REST di Riconoscimento modulo di Azure con Python per estrarre e identificare informazioni rilevanti nelle ricevute di vendita.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- Accesso all'anteprima dell'API di riconoscimento modulo ad accesso limitato. Per avere accesso all'anteprima, completare e inviare il modulo di [richiesta di accesso al riconoscimento modulo](https://aka.ms/FormRecognizerRequestAccess).
- [Python](https://www.python.org/downloads/) installato, se si vuole eseguire l'esempio in locale.
- Un URL di un'immagine di una ricevuta. Per questo argomento di avvio rapido è possibile usare un'[immagine di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true).

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analizzare una ricevuta

Per iniziare ad analizzare una ricevuta, chiamare l'API **Analyze Receipt** (Analisi ricevuta) usando lo script Python seguente. Prima di eseguire lo script, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<your receipt URL>` con l'indirizzo URL dell'immagine di una ricevuta.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Salvare il codice in un file con estensione py. Ad esempio, *form-riconoscimento-receipts.py*.
1. Aprire una finestra del prompt dei comandi.
1. Al prompt usare il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognizer-receipts.py`.

Si riceverà una risposta `202 (Success)` contenente un'intestazione **Operation-Location**, che lo script stamperà nella console. Questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione e ottenere i risultati dell'analisi. Nel valore di esempio seguente, la stringa dopo `operations/` è l'ID operazione.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Ottenere i risultati della ricevuta

Dopo aver chiamato l'API **Analyze Receipt** (Analisi ricevuta), chiamare l'API **Get Receipt Result** (Acquisizione risultato ricevuta) per ottenere lo stato dell'operazione e i dati estratti. Aggiungere il codice seguente in fondo allo script Python. In questo modo viene estratto l'ID operazione, successivamente passato a una nuova chiamata API. Questa operazione è asincrona e, pertanto, lo script chiama l'API a intervalli regolari finché non vengono ottenuti i risultati. Si consiglia un intervallo di almeno un secondo.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Salvare lo script.
1. Usare nuovamente il comando `python` per eseguire l'esempio. Ad esempio: `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Esaminare i risultati

Lo script stamperà le risposte nella console fino al completamento dell'operazione di analisi. Stamperà quindi i dati di testo estratti in formato JSON. Il campo `"recognitionResults"` contiene tutte le righe di testo estratte dalla ricevuta e il campo `"understandingResults"` contiene le informazioni chiave/valore relative alle sezioni più rilevanti della ricevuta.

Osservare l'immagine di ricevuta seguente e il corrispondente output JSON. L'output è stato abbreviato per una migliore leggibilità.

![Ricevuta di un negozio Contoso](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata l'API REST di riconoscimento modulo con Python per eseguire il training di un modello e quindi eseguire il modello in uno scenario di esempio. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
