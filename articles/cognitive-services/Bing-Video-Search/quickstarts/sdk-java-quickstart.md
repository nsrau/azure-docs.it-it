---
title: "Avvio rapido: Cercare video con l'SDK di Ricerca video Bing per Java"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare richieste di ricerca di video con l'SDK di Ricerca video Bing per Java.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: d421abf0a3cc402cf8d040363dfe79b07b66bf95
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105710"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-java"></a>Avvio rapido: Eseguire una ricerca di video con l'SDK di Ricerca video Bing per Java

Usare questa guida introduttiva per iniziare a cercare notizie con l'SDK di Ricerca video Bing per Java. Mentre Ricerca video Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio si trova in [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), che contiene altre annotazioni e funzionalità di Ricerca video Bing.

## <a name="prerequisites"></a>Prerequisiti

* [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Libreria Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

Installare le dipendenze dell'SDK di Ricerca video Bing usando Maven, Gradle o un altro sistema di gestione delle dipendenze. Il file POM di Maven richiede la dichiarazione seguente:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto


Creare un nuovo progetto Java nell'ambiente di sviluppo integrato o nell'editor preferito e importare le librerie seguenti.

    ```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
    ```

## <a name="create-a-search-client"></a>Creare un client di ricerca

1. Implementare il client `VideoSearchAPIImpl`, che richiede l'endpoint API, e un'istanza della classe `ServiceClientCredentials`.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Per implementare `ServiceClientCredentials`, seguire questa procedura:

    1. Eseguire l'override della funzione `applyCredentialsFilter()`, con un oggetto `OkHttpClient.Builder` come parametro. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. All'interno di `applyCredentialsFilter()`, chiamare `builder.addNetworkInterceptor()`. Creare un nuovo oggetto `Interceptor` ed eseguire l'override del relativo metodo `intercept()` per acquisire un oggetto intercettore `Chain`.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. All'interno della funzione `intercept` creare le variabili per la richiesta. Usare `Request.Builder()` per creare la richiesta. Aggiungere la chiave della sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key` e restituire `chain.proceed()` per l'oggetto della richiesta.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>Inviare una richiesta di ricerca e ricevere la risposta 

1. Creare una funzione denominata `VideoSearch()` che accetta la chiave della sottoscrizione come stringa. Creare un'istanza del client di ricerca creato in precedenza.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. All'interno di `VideoSearch()` inviare una richiesta di ricerca di video usando il client, con `SwiftKey` come termine di ricerca. Se l'API Ricerca video ha restituito un risultato, ottenere il primo risultato e stampare i relativi ID, nome e URL, oltre al numero totale di video restituiti. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Chiamare il metodo search dal metodo main.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche  

* [Informazioni sull'API Ricerca video Bing](../overview.md)
* [Esempi di .NET SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)