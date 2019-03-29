---
title: "Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST di rilevatore di anomalie e Java | Microsoft Docs"
description: Usare l'API rilevatore di anomalie per rilevare le anomalie nella serie di dati come un batch o nel flusso di dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 06cb4d32359014f3cbc67ed1f75988c794e6599e
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619512"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST di rilevatore di anomalie e Java

Usare questa Guida introduttiva per iniziare a usare due modalità di rilevamento dell'API di rilevamento delle anomalie per rilevare le anomalie nei dati delle serie temporali. L'applicazione Java invia due richieste di API che contiene dati delle serie temporali in formato JSON e ottiene le risposte.

| Richiesta dell'API                                        | Output dell'applicazione                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare le anomalie in batch                        | La risposta JSON che contiene lo stato delle anomalie (e altri dati) per ogni punto dati in dati della serie temporale e le posizioni di tutte le anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | La risposta JSON che contiene lo stato delle anomalie (e altri dati) per il punto dati più recente nei dati di serie temporali.                                                                                                                                         |

 L'applicazione è scritta in Java, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

- Il [Java&trade; sviluppo Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva.

- Queste librerie di importazione dal Repository Maven
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json) pacchetto
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pacchetto

- Fa riferimento un JSON file contenitore serie temporale. I dati di esempio per questa Guida introduttiva sono disponibili nella [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. Creare un nuovo progetto Java nell'ambiente di sviluppo integrato o nell'editor preferito e importare le librerie seguenti.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Creare variabili per la chiave di sottoscrizione e l'endpoint. Di seguito sono riportati gli URI è possibile usare per il rilevamento anomalie. Queste verranno aggiunte all'endpoint di servizio in un secondo momento per creare l'API URL delle richieste.

    |Metodo di rilevamento  |URI  |
    |---------|---------|
    |Rilevamento di batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Rilevamento sul punto dati più recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Leggere il file di dati JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per l'invio di richieste

1. Creare una nuova funzione denominata `sendRequest()` che accetta le variabili create in precedenza. Quindi, eseguire i passaggi seguenti.

2. Creare un `CloseableHttpClient` oggetto che può inviare richieste all'API. Invia la richiesta a un `HttpPost` oggetto richiesta combinando l'endpoint e un URL di rilevamento delle anomalie.

3. Usare la richiesta `setHeader()` funzione per impostare il `Content-Type` intestazione `application/json`e aggiungere la chiave di sottoscrizione per il `Ocp-Apim-Subscription-Key` intestazione.

4. Usare la richiesta `setEntity()` funzione per i dati da inviare.   

5. Usare il client `execute()` funzione per inviare la richiesta e salvarlo in un `CloseableHttpResponse` oggetto. 

6. Creare un `HttpEntity` oggetto usato per archiviare il contenuto della risposta. Ottenere il contenuto con `getEntity()`. Se la risposta non è vuota, restituirlo.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Rilevare le anomalie in batch

1. Creare un metodo denominato `detectAnomaliesBatch()` per rilevare le anomalie in tutto i dati come batch. Chiamare il `sendRequest()` metodo creato in precedenza con l'endpoint, url, la chiave di sottoscrizione e i dati json. Ottenere il risultato ed eseguire la stampa nella console.

2. Trovare le posizioni delle anomalie nel set di dati. La risposta `isAnomaly` campo contiene un valore booleano relative al fatto che un punto dati specificato è un'anomalia. Ottenere la matrice JSON ed eseguire l'iterazione attraverso di esso, stampa l'indice di qualsiasi `true` valori. Questi valori corrispondono all'indice di punti dati anomali, se sono stati trovati.

    
    ```java
    static void detectAnomaliesBatch(String requestData) {
        System.out.println("Detecting anomalies as a batch");
        String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
        if (result != null) {
            System.out.println(result);
            JSONObject jsonObj = new JSONObject(result);
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
    ```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

* Creare un metodo denominato `detectAnomaliesLatest()` per rilevare lo stato di anomalie dell'ultimo punto dati nel set di dati. Chiamare il `sendRequest()` metodo creato in precedenza con l'endpoint, url, la chiave di sottoscrizione e i dati json. Ottenere il risultato ed eseguire la stampa nella console.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Caricare i dati delle serie temporali e inviare la richiesta

1. Nel metodo main dell'applicazione, leggere il file JSON che contiene i dati che verranno aggiunti alle richieste.

2. Chiamare le funzioni di rilevamento due anomalie create in precedenza.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        detectAnomaliesBatch(requestData);
        detectAnomaliesLatest(requestData);
    }
    ```

### <a name="example-response"></a>Risposta di esempio

Una risposta con esito positivo viene restituita in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Risposta di rilevamento di batch di esempio](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Risposta di esempio più recente punto di rilevamento](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)