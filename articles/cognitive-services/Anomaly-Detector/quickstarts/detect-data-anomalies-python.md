---
title: "Guida introduttiva: Rilevare le anomalie come batch con l'API REST Rilevamento anomalie e Python"
titleSuffix: Azure Cognitive Services
description: Usare l'API Rilevamento anomalie per rilevare le anomalie nelle serie di dati sia come batch che nei dati in streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: aahi
ms.openlocfilehash: 571626da0f3f43c8c2a2e33e1147418158c5473b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754209"
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

- [Libreria Requests](https://pypi.org/project/requests/) per Python

- File JSON contenente i punti dati delle serie temporali. I dati di esempio per questa guida di avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Creare una risorsa di Rilevamento anomalie

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. Creare un nuovo file Python e aggiungere le importazioni seguenti.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Creare variabili per la chiave di sottoscrizione e per l'endpoint. Di seguito sono riportati gli URI che è possibile usare per il rilevamento anomalie. Questi verranno aggiunti all'endpoint di servizio in un secondo momento per creare gli URL delle richieste API.

    |Metodo di rilevamento  |URI  |
    |---------|---------|
    |Rilevamento in batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Rilevamento nel punto dati più recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Leggere il file di dati JSON aprendolo e usando `json.load()`.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione denominata `send_request()` che accetta le variabili create in precedenza. Quindi, eseguire i passaggi seguenti.

2. Creare un dizionario per le intestazioni della richiesta. Impostare `Content-Type` su `application/json` e aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

3. Inviare la richiesta usando `requests.post()`. Combinare l'endpoint e URL di rilevamento delle anomalie per l'URL completo della richiesta e includere le intestazioni e i dati della richiesta JSON. Restituire quindi la risposta.

[!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Rilevare anomalie come batch

1. Creare un metodo denominato `detect_batch()` per rilevare le anomalie in tutti i dati come batch. Chiamare il metodo `send_request()` creato in precedenza con l'endpoint, l'URL, la chiave di sottoscrizione e i dati json.

2. Chiamare `json.dumps()` sul risultato per formattarlo e stamparlo nella console.

3. Se la risposta contiene il campo `code`, viene visualizzato il codice errore e il messaggio di errore.

4. In caso contrario, trovare le posizioni delle anomalie nel set di dati. Il campo `isAnomaly` della risposta contiene un valore booleano che indica se il punto dati specificato è un'anomalia. Scorrere l'elenco e stampare l'indice dei valori `True`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

[!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

1. Creare un metodo denominato `detect_latest()` per determinare se l'ultimo punto dati della serie temporale è un'anomalia. Chiamare il metodo `send_request()` creato in precedenza con l'endpoint, l'URL, la chiave di sottoscrizione e i dati JSON. 

2. Chiamare `json.dumps()` sul risultato per formattarlo e stamparlo nella console.

[!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Inviare la richiesta

1. Chiamare quindi i metodi di rilevamento anomalie creati in precedenza.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Risposta di esempio

Viene restituita una risposta con esito positivo in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Esempio di risposta di rilevamento in batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Esempio di risposta di rilevamento nel punto dati più recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Rilevamento anomalie nei dati di streaming con Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* [Cos'è l'API Rilevamento anomalie?](../overview.md)
* [Procedure consigliate](../concepts/anomaly-detection-best-practices.md) per l'API Rilevamento anomalie.
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
