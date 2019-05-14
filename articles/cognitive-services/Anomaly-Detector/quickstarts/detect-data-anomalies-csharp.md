---
title: "Avvio rapido: Rilevare le anomalie nei dati delle serie temporali utilizzando l'API REST di rilevatore di anomalie e C# | Microsoft Docs"
description: Usare l'API rilevatore di anomalie per rilevare le anomalie nella serie di dati come un batch o nel flusso di dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: ffa42eb2247d163dd6a146ffb26c1f8cc5cf6d82
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922529"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Avvio rapido: Rilevare le anomalie nei dati delle serie temporali utilizzando l'API REST di rilevatore di anomalie eC# 

Usare questa Guida introduttiva per iniziare a usare due modalità di rilevamento dell'API di rilevamento delle anomalie per rilevare le anomalie nei dati delle serie temporali. In questo C# applicazione invia due richieste di API che contiene dati delle serie temporali in formato JSON e ottiene le risposte.

| Richiesta dell'API                                        | Output dell'applicazione                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Rilevare le anomalie in batch                        | La risposta JSON che contiene lo stato delle anomalie (e altri dati) per ogni punto dati in dati della serie temporale e le posizioni di tutte le anomalie rilevate. |
| Rilevare lo stato delle anomalie del punto dati più recente | La risposta JSON che contiene lo stato delle anomalie (e altri dati) per il punto dati più recente nei dati di serie temporali.                                                                                                                                         |

 L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Per installare newtonsoft. JSON come pacchetto NuGet in Visual studio:
        1. Fare clic con il pulsante destro sul **Solution Manager**
        2. Scegliere **Gestisci pacchetti NuGet...**
        3. Cercare `Newtonsoft.Json` e installare il pacchetto
- Se si usa Linux/MacOS, questa applicazione può essere eseguito usando [Mono](https://www.mono-project.com/).

- Fa riferimento un JSON file contenitore serie temporale. I dati di esempio per questa Guida introduttiva sono disponibili nella [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. In Visual Studio, creare una nuova soluzione di console e aggiungere i pacchetti seguenti. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Creare variabili per la chiave di sottoscrizione e l'endpoint. Di seguito sono riportati gli URI è possibile usare per il rilevamento anomalie. Queste verranno aggiunte all'endpoint di servizio in un secondo momento per creare l'API URL delle richieste.

    |Metodo di rilevamento  |URI  |
    |---------|---------|
    |Rilevamento di batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Rilevamento sul punto dati più recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
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

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per l'invio di richieste

1. Creare una nuova funzione async chiamata `Request` che accetta le variabili create in precedenza.

2. Impostare il protocollo di sicurezza del client e informazioni di intestazione usando un `HttpClient` oggetto. Assicurarsi di aggiungere la chiave di sottoscrizione per il `Ocp-Apim-Subscription-Key` intestazione. Creare quindi un `StringContent` oggetto per la richiesta.

3. Inviare la richiesta con `PostAsync()`e quindi restituire la risposta.

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

## <a name="detect-anomalies-as-a-batch"></a>Rilevare le anomalie in batch

1. Creare una nuova funzione denominata `detectAnomaliesBatch()`. Costruire la richiesta e inviarlo tramite una chiamata di `Request()` canonica con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento anomalie di batch e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e scriverlo nella console.

3. Se la risposta contiene `code` campo, il codice di errore e il messaggio di errore di stampa. 

4. In caso contrario, è possibile trovare le posizioni delle anomalie nel set di dati. La risposta `isAnomaly` campo contiene una matrice di valori booleani, ognuno dei quali indica se un punto dati è un'anomalia. Vuoi convertire in una matrice di stringhe con l'oggetto di risposta `ToObject<bool[]>()` (funzione). Scorrere la matrice e stampa l'indice di qualsiasi `true` valori. Questi valori corrispondono all'indice di punti dati anomali, se sono stati trovati.

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

1. Creare una nuova funzione denominata `detectAnomaliesLatest()`. Costruire la richiesta e inviarlo tramite una chiamata di `Request()` canonica con l'endpoint, la chiave di sottoscrizione, l'URL per il rilevamento di anomalie punto più recente e i dati delle serie temporali.

2. Deserializzare l'oggetto JSON e scriverlo nella console.

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

1. Nel metodo main dell'applicazione, caricare i dati delle serie temporali JSON con `File.ReadAllText()`. 

2. Chiamare le funzioni di rilevamento delle anomalie create in precedenza. Usare `System.Console.ReadKey()` tenere aperta la finestra della console dopo l'esecuzione dell'applicazione.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Risposta di esempio

Una risposta con esito positivo viene restituita in formato JSON. Fare clic sui collegamenti seguenti per visualizzare la risposta JSON in GitHub:
* [Risposta di rilevamento di batch di esempio](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Risposta di esempio più recente punto di rilevamento](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
