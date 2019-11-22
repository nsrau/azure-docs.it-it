---
title: 'Guida introduttiva: Estrarre i dati delle ricevute usando cURL - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà l'API REST di Riconoscimento modulo con cURL per estrarre i dati dalle immagini delle ricevute di vendita.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: c533949cf0ce69ddc5237dd893dd75e43447c4a9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931593"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Guida introduttiva: Estrarre i dati delle ricevute usando l'API REST di Riconoscimento modulo con cURL

In questo argomento di avvio rapido si userà l'API REST di Riconoscimento modulo di Azure con cURL per estrarre e identificare informazioni rilevanti nelle ricevute di vendita.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- Accesso all'anteprima dell'API di riconoscimento modulo ad accesso limitato. Per avere accesso all'anteprima, completare e inviare il modulo di [richiesta di accesso al riconoscimento modulo](https://aka.ms/FormRecognizerRequestAccess).
- [cURL](https://curl.haxx.se/windows/) installato.
- Un URL di un'immagine di una ricevuta. Per questo argomento di avvio rapido è possibile usare un'[immagine di esempio](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true).

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analizzare una ricevuta

Per iniziare ad analizzare una ricevuta, chiamare l'API **Analyze Receipt** (Analisi ricevuta) usando il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<your receipt URL>` con l'indirizzo URL dell'immagine di una ricevuta.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Si riceverà una risposta `202 (Success)` che include un'intestazione **Operation-Location**. Il valore di questa intestazione contiene un ID operazione che è possibile usare per eseguire una query sullo stato dell'operazione e ottenere i risultati. Nell'esempio seguente, la stringa dopo `operations/` è l'ID operazione.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Ottenere i risultati della ricevuta

Dopo aver chiamato l'API **Analyze Receipt** (Analisi ricevuta), chiamare l'API **Get Receipt Result** (Acquisizione risultato ricevuta) per ottenere lo stato dell'operazione e i dati estratti.

1. Sostituire `<operationId>` con l'ID operazione del passaggio precedente.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Esaminare i risultati

Si riceverà una risposta `200 (Success)` con un output JSON. Il primo campo, `"status"`, indica lo stato dell'operazione. Se l'operazione è stata completata, il campo `"recognitionResults"` contiene tutte le righe di testo estratte dalla ricevuta e il campo `"understandingResults"` contiene le informazioni chiave/valore relative alle sezioni più rilevanti della ricevuta. Se l'operazione non è stata completata, il valore di `"status"` sarà `"Running"` o `"NotStarted"` e si dovrà chiamare nuovamente l'API, manualmente o tramite uno script. Si consiglia di attendere almeno un secondo tra le chiamate.

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

In questo argomento di avvio rapido è stata usata l'API REST di Riconoscimento modulo con cURL per estrarre il contenuto di una ricevuta di vendita. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
