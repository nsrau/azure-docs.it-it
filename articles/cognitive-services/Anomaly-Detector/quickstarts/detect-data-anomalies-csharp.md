---
title: "Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e C# | Microsoft Docs"
description: Usare l'API Rilevamento anomalie per rilevare le anomalie nelle serie di dati sia come batch che nei dati in streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 2a02b56c2fa0f99166cfde0f0089273ed2af4cb9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073204"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie e C# 

Usare questo avvio rapido per iniziare a usare le due modalità di rilevamento dell'API Rilevamento anomalie per rilevare le anomalie nei dati delle serie temporali. Questa applicazione C# invia due richieste API contenenti i dati delle serie temporali in formato JSON e riceve le risposte.

| Richiesta API                                        | Output dell'applicazione                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare anomalie come batch                        | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per ogni punto dati nei dati di serie temporali e le posizioni delle anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | Risposta JSON contenente lo stato dell'anomalia (e altri dati) per il punto dati più recente nei dati di serie temporali.                                                                                                                                         |

 L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://visualstudio.microsoft.com/downloads/)

- Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet. Per installare Newtonsoft.Json come pacchetto NuGet in Visual Studio:
    
    1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.
    2. Scegliere **Gestisci pacchetti NuGet**.
    3. Cercare *Newtonsoft.Json* e installare il pacchetto.

- Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).

- File JSON contenente i punti dati delle serie temporali. I dati di esempio per questo avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. In Visual Studio creare una nuova soluzione console e aggiungere i pacchetti seguenti. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Creare variabili per la chiave di sottoscrizione e per l'endpoint. Di seguito sono riportati gli URI che è possibile usare per il rilevamento anomalie. Questi verranno aggiunti all'endpoint di servizio in un secondo momento per creare gli URL delle richieste API.

    |Metodo di rilevamento  |URI  |
    |---------|---------|
    |Rilevamento in batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Rilevamento nel punto dati più recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per inviare richieste

1. Creare una nuova funzione asincrona denominata `Request` che accetta le variabili create in precedenza.

2. Impostare il protocollo di sicurezza del client e le informazioni di intestazione con l'oggetto `HttpClient`. Assicurarsi di aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`. Creare quindi un oggetto `StringContent` per la richiesta.

3. Inviare la richiesta con `PostAsync()` e quindi restituire la risposta.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Rilevare anomalie come batch

1. Creare una nuova funzione denominata `detectAnomaliesBatch()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento anomalie in batch e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console.

3. Se la risposta contiene il campo `code`, viene visualizzato il codice di errore e il messaggio di errore. 

4. In caso contrario, trovare le posizioni delle anomalie nel set di dati. Il campo `isAnomaly` della risposta contiene una matrice di valori booleani, ognuno dei quali indica se un punto dati è un'anomalia. Convertirla in una matrice di stringhe con la funzione `ToObject<bool[]>()` dell'oggetto risposta. Scorrere la matrice e stampare l'indice dei valori `true`. Questi valori corrispondono all'indice dei punti dati anomali, se presenti.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Rilevare lo stato delle anomalie del punto dati più recente

1. Creare una nuova funzione denominata `detectAnomaliesLatest()`. Creare la richiesta e inviarla chiamando la funzione `Request()` con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento anomalie nel punto dati più recente e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e visualizzarlo nella console.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Caricare i dati delle serie temporali e inviare la richiesta

1. Nel metodo main dell'applicazione caricare i dati delle serie temporali JSON con `File.ReadAllText()`. 

2. Chiamare le funzioni di rilevamento anomalie create in precedenza. Usare `System.Console.ReadKey()` per mantenere aperta la finestra della console dopo l'esecuzione dell'applicazione.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Risposta di esempio

Viene restituita una risposta con esito positivo in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Esempio di risposta di rilevamento in batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Esempio di risposta di rilevamento nel punto dati più recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
