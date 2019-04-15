---
title: "Guida introduttiva: Rilevare le anomalie in batch usando l'API REST di rilevatore di anomalie e Python | Microsoft Docs"
description: Usare l'API rilevatore di anomalie per rilevare le anomalie nella serie di dati come un batch o nel flusso di dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 6b4ddcadfe63f74d115c155354a276e45c6b53f9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544501"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST di rilevatore di anomalie e Python

Usare questa Guida introduttiva per iniziare a usare due modalità di rilevamento dell'API di rilevamento delle anomalie per rilevare le anomalie nei dati delle serie temporali. Questa applicazione Python invia due richieste di API che contiene dati delle serie temporali in formato JSON e ottiene le risposte.

| Richiesta dell'API                                        | Output dell'applicazione                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare le anomalie in batch                        | La risposta JSON che contiene lo stato delle anomalie (e altri dati) per ogni punto dati in dati della serie temporale e le posizioni di tutte le anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | La risposta JSON che contiene lo stato delle anomalie (e altri dati) per il punto dati più recente nei dati di serie temporali.                                                                                                                                         |

 L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

- [Python 2.x o 3.x](https://www.python.org/downloads/)

- Il [libreria di richieste](http://docs.python-requests.org) per python

- Fa riferimento un JSON file contenitore serie temporale. I dati di esempio per questa Guida introduttiva sono disponibili nella [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. L'editor di testo o IDE, creare un nuovo file di python. Aggiungere le istruzioni import seguenti.

    ```python
    import requests
    import json
    ```

2. Creare variabili per la chiave di sottoscrizione e l'endpoint. Di seguito sono riportati gli URI è possibile usare per il rilevamento anomalie. Queste verranno aggiunte all'endpoint di servizio in un secondo momento per creare l'API URL delle richieste.

    |Metodo di rilevamento  |URI  |
    |---------|---------|
    |Rilevamento di batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Rilevamento sul punto dati più recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Leggere nel file di dati JSON, aprirlo e usando `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per l'invio di richieste

1. Creare una nuova funzione denominata `send_request()` che accetta le variabili create in precedenza. Quindi, eseguire i passaggi seguenti.

2. Creare un dizionario per le intestazioni della richiesta. Impostare il `Content-Type` al `application/json`e aggiungere la chiave di sottoscrizione per il `Ocp-Apim-Subscription-Key` intestazione.

3. Inviare la richiesta tramite `requests.post()`. Combinare l'endpoint e URL di rilevamento delle anomalie per l'intero URL della richiesta e includere le intestazioni e i dati della richiesta json. E quindi restituire la risposta.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Rilevare le anomalie in batch

1. Creare un metodo denominato `detect_batch()` per rilevare le anomalie in tutto i dati come batch. Chiamare il `send_request()` metodo creato in precedenza con l'endpoint, url, la chiave di sottoscrizione e i dati json.

2. Chiamare `json.dumps()` sul risultato formattarla e stamparlo nella console.

3. Se la risposta contiene `code` campo, il codice di errore e il messaggio di errore di stampa.

4. In caso contrario, è possibile trovare le posizioni delle anomalie nel set di dati. La risposta `isAnomaly` campo contiene un valore booleano relative al fatto che un punto dati specificato è un'anomalia. Scorrere l'elenco e stampare l'indice di qualsiasi `True` valori. Questi valori corrispondono all'indice di punti dati anomali, se sono stati trovati.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

1. Creare un metodo denominato `detect_latest()` per determinare se l'ultimo punto dati nella serie temporale è un'anomalia. Chiamare il `send_request()` metodo sopra con l'endpoint, url, la chiave di sottoscrizione e i dati json. 

2. Chiamare `json.dumps()` sul risultato formattarla e stamparlo nella console.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Caricare i dati delle serie temporali e inviare la richiesta

1. Caricare i dati delle serie temporali JSON aprendo un gestore di file e usando `json.load()` su di esso. Chiamare quindi l'anomalia i metodi di rilevamento creati in precedenza.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Risposta di esempio

Una risposta con esito positivo viene restituita in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Risposta di rilevamento di batch di esempio](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Risposta di esempio più recente punto di rilevamento](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)