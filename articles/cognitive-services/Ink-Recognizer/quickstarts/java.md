---
title: "Avvio rapido: Riconoscere l'input penna digitale con l'API REST per il riconoscimento di input penna e Java"
description: Usare l'API di riconoscimento input penna per avviare tratti input penna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6237253922544dc47bb11aec4dd58139f99eb0da
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518621"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Avvio rapido: Riconoscere l'input penna digitale con l'API REST per il riconoscimento di input penna e Java

Usare questa Guida introduttiva per iniziare a usare l'API di riconoscimento input penna su tratti input penna digitale. L'applicazione Java invia una richiesta di API che contiene i dati tratti input penna in formato JSON e ottiene la risposta.

L'applicazione è scritta in Java, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

In genere è necessario chiamare l'API da un'app di input penna digitale. Questa Guida introduttiva invia i dati di tratti input penna per il seguente codice di esempio scritto a mano da un file JSON.

![un'immagine di testo scritto a mano](../media/handwriting-sample.jpg)

Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Prerequisiti

- Il [Java&trade; sviluppo Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva.

- Queste librerie di importazione dal Repository Maven
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json) pacchetto
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pacchetto

- Dati tratti input penna di esempio per questa Guida introduttiva sono disponibili nella [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Creare variabili per la chiave di sottoscrizione e l'endpoint. Di seguito è l'URI è possibile usare per il riconoscimento di input penna. Si verrà aggiunto all'endpoint di servizio in un secondo momento per creare l'URL della richiesta API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Creare una funzione per l'invio di richieste

1. Creare una nuova funzione denominata `sendRequest()` che accetta le variabili create in precedenza. Quindi, eseguire i passaggi seguenti.

2. Creare un `CloseableHttpClient` oggetto che può inviare richieste all'API. Invia la richiesta a un `HttpPut` oggetto richiesta combinando l'endpoint e l'URL per il riconoscimento di input penna.

3. Usare la richiesta `setHeader()` funzione per impostare il `Content-Type` intestazione `application/json`e aggiungere la chiave di sottoscrizione per il `Ocp-Apim-Subscription-Key` intestazione.

4. Usare la richiesta `setEntity()` funzione per i dati da inviare.   

5. Usare il client `execute()` funzione per inviare la richiesta e salvarlo in un `CloseableHttpResponse` oggetto. 

6. Creare un `HttpEntity` oggetto usato per archiviare il contenuto della risposta. Ottenere il contenuto con `getEntity()`. Se la risposta non è vuota, restituirlo.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Inviare una richiesta di riconoscimento input penna

Creare un metodo denominato `recognizeInk()` per riconoscere i dati di traccia di input penna. Chiamare il `sendRequest()` metodo creato in precedenza con l'endpoint, url, la chiave di sottoscrizione e i dati json. Ottenere il risultato ed eseguire la stampa nella console.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Caricare i dati di input penna e inviare la richiesta

1. Nel metodo main dell'applicazione, leggere il file JSON che contiene i dati che verranno aggiunti alle richieste.

2. Chiamare la funzione di riconoscimento input penna creata in precedenza.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Eseguire l'applicazione e visualizzare la risposta

Eseguire l'applicazione. Una risposta con esito positivo viene restituita in formato JSON. È anche possibile trovare la risposta JSON sul [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Per vedere come funziona l'API Riconoscimento input penna in un'app di input penna, esaminare le applicazioni di esempio seguenti in GitHub:
* [C# e piattaforma UWP (Universal Windows Platform)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [App Javascript per Web browser](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [App Java e per dispositivi mobili Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [App Swift e per dispositivi mobili iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
