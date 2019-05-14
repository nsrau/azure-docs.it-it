---
title: "Avvio rapido: Riconoscere l'input penna digitale con l'API REST per il riconoscimento di input penna eC#"
description: Usare l'API di riconoscimento input penna per avviare tratti input penna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 9bb9c23cc1f807cae1d0d22f1652e8f4408f1f91
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518680"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Avvio rapido: Riconoscere l'input penna digitale con l'API REST per il riconoscimento di input penna eC#

Usare questa Guida introduttiva per iniziare a inviare i tratti input penna per l'API di riconoscimento input penna. In questo C# applicazione invia una richiesta di API che contiene i dati tratti input penna in formato JSON e ottiene la risposta.

L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

In genere è necessario chiamare l'API da un'app di input penna digitale. Questa Guida introduttiva invia i dati di tratti input penna per il seguente codice di esempio scritto a mano da un file JSON.

![un'immagine di testo scritto a mano](../media/handwriting-sample.jpg)

Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Per installare newtonsoft. JSON come pacchetto NuGet in Visual studio:
        1. Fare clic con il pulsante destro sul **Solution Manager**
        2. Scegliere **Gestisci pacchetti NuGet...**
        3. Cercare `Newtonsoft.Json` e installare il pacchetto
- Se si usa Linux/MacOS, questa applicazione può essere eseguito usando [Mono](https://www.mono-project.com/).

- Dati tratti input penna di esempio per questa Guida introduttiva sono disponibili nella [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


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
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Creare variabili per la chiave di sottoscrizione e l'endpoint. Di seguito è l'URI è possibile usare per il riconoscimento di input penna. Questo verrà aggiunto all'endpoint di servizio in un secondo momento per creare l'API URl della richiesta.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per l'invio di richieste

1. Creare una nuova funzione async chiamata `Request` che accetta le variabili create in precedenza.

2. Impostare il protocollo di sicurezza del client e informazioni di intestazione usando un `HttpClient` oggetto. Assicurarsi di aggiungere la chiave di sottoscrizione per il `Ocp-Apim-Subscription-Key` intestazione. Creare quindi un `StringContent` oggetto per la richiesta.
 
3. Inviare la richiesta con `PutAsync()`. Se la richiesta ha esito positivo, restituisce la risposta.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Inviare una richiesta di riconoscimento input penna

1. Creare una nuova funzione denominata `recognizeInk()`. Costruire la richiesta e inviarlo tramite una chiamata di `Request()` canonica con l'endpoint, la chiave di sottoscrizione, l'URL per l'API e i dati del tratto input penna.

2. Deserializzare l'oggetto JSON e scriverlo nella console. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Caricare i dati di input penna digitale

Creare una funzione denominata `LoadJson()` per caricare il file JSON dei dati di input penna. Usare un `StreamReader` e `JsonTextReader` per creare un `JObject` e restituirlo.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Inviare la richiesta dell'API

1. Nel metodo main dell'applicazione, caricare i dati JSON con la funzione creata in precedenza. 

2. Chiamare il `recognizeInk()` funzione creata in precedenza. Usare `System.Console.ReadKey()` tenere aperta la finestra della console dopo l'esecuzione dell'applicazione.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Eseguire l'applicazione e visualizzare la risposta

Eseguire l'applicazione. Una risposta con esito positivo viene restituita in formato JSON. È anche possibile trovare la risposta JSON sul [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Per vedere come funziona l'API Riconoscimento input penna in un'app di input penna, esaminare le applicazioni di esempio seguenti in GitHub:
* [C# e piattaforma UWP (Universal Windows Platform)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [App Javascript per Web browser](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [App Java e per dispositivi mobili Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [App Swift e per dispositivi mobili iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
