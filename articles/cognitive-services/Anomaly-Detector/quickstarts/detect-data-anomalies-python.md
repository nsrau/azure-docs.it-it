---
title: "Guida introduttiva: Rilevare le anomalie come batch con l'API REST Rilevamento anomalie e Python | Microsoft Docs"
description: Usare l'API Rilevamento anomalie per rilevare le anomalie nelle serie di dati sia come batch che nei dati in streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 0ab39a7bb86671efb6258e3171e3bf7847aa241f
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341731"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e Python

Usare questa guida di avvio rapido per iniziare a usare le due modalità di rilevamento dell'API Rilevamento anomalie per rilevare le anomalie nei dati delle serie temporali. Questa applicazione Python invia due richieste API contenenti i dati delle serie temporali in formato JSON e riceve le risposte.

| Richiesta API                                        | Output dell'applicazione                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare anomalie come batch                        | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per ogni punto dati nei dati di serie temporali e le posizioni delle anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per il punto dati più recente nei dati di serie temporali.                                                                                                                                         |

 L'applicazione è scritta in Python, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

- [Python 2.x o 3.x](https://www.python.org/downloads/)

- [Libreria Requests](http://docs.python-requests.org) per Python

- File JSON contenente i punti dati delle serie temporali. I dati di esempio per questa guida di avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. Creare un nuovo file Python nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le importazioni seguenti.

    ```python
    import requests
    import json
    ```

2. Creare variabili per la chiave di sottoscrizione e per l'endpoint. Di seguito sono riportati gli URI che è possibile usare per il rilevamento anomalie. Questi verranno aggiunti all'endpoint di servizio in un secondo momento per creare gli URL delle richieste API.

    |Metodo di rilevamento  |URI  |
    |---------|---------|
    |Rilevamento in batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Rilevamento nel punto dati più recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Leggere il file di dati JSON aprendolo e usando `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione denominata `send_request()` che accetta le variabili create in precedenza. Quindi, eseguire i passaggi seguenti.

2. Creare un dizionario per le intestazioni della richiesta. Impostare `Content-Type` su `application/json` e aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

3. Inviare la richiesta usando `requests.post()`. Combinare l'endpoint e URL di rilevamento delle anomalie per l'URL completo della richiesta e includere le intestazioni e i dati della richiesta JSON. Restituire quindi la risposta.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Rilevare anomalie come batch

1. Creare un metodo denominato `detect_batch()` per rilevare le anomalie in tutti i dati come batch. Chiamare il metodo `send_request()` creato in precedenza con l'endpoint, l'URL, la chiave di sottoscrizione e i dati json.

2. Chiamare `json.dumps()` sul risultato per formattarlo e stamparlo nella console.

3. Se la risposta contiene il campo `code`, viene visualizzato il codice errore e il messaggio di errore.

4. In caso contrario, trovare le posizioni delle anomalie nel set di dati. Il campo `isAnomaly` della risposta contiene un valore booleano che indica se il punto dati specificato è un'anomalia. Scorrere l'elenco e stampare l'indice dei valori `True`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

1. Creare un metodo denominato `detect_latest()` per determinare se l'ultimo punto dati della serie temporale è un'anomalia. Chiamare il metodo `send_request()` creato in precedenza con l'endpoint, l'URL, la chiave di sottoscrizione e i dati JSON. 

2. Chiamare `json.dumps()` sul risultato per formattarlo e stamparlo nella console.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Caricare i dati delle serie temporali e inviare la richiesta

1. Caricare i dati della serie temporale JSON aprendo un gestore di file e usando `json.load()` su di esso. Chiamare quindi i metodi di rilevamento anomalie creati in precedenza.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Risposta di esempio

Viene restituita una risposta con esito positivo in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Esempio di risposta di rilevamento in batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Esempio di risposta di rilevamento nel punto dati più recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
