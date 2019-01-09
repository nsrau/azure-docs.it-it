---
title: "Avvio rapido: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Java"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 148d145dae01fffdf7a4c650a0e2b20f8387295a
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742185"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Avvio rapido: Ottenere informazioni dettagliate sulle immagini usando l'API REST Ricerca visiva Bing e Java

Usare questo argomento di avvio rapido per eseguire la prima chiamata all'API Ricerca visiva Bing e visualizzare i risultati della ricerca. Questa semplice applicazione C# carica un'immagine nell'API e visualizza le informazioni restituite. L'applicazione è scritta in Java, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

Quando si carica un'immagine locale, i dati del modulo devono includere l'intestazione Content-Disposition. Il parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Prerequisiti

* [Java Development Kit (JDK) 7 o 8](https://aka.ms/azure-jdks)
* [Libreria Gson](https://github.com/google/gson)
* [Apache HttpComponents](http://hc.apache.org/downloads.cgi)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. Creare un nuovo progetto Java nell'ambiente di sviluppo integrato o nell'editor preferito e importare le librerie seguenti.

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Creare variabili per l'endpoint dell'API, la chiave di sottoscrizione e il percorso dell'immagine. 

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

## <a name="create-the-json-parser"></a>Creare il parser JSON

Creare un metodo per rendere più leggibile la risposta JSON dall'API usando `JsonParser`.

    ```java
    public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="construct-the-search-request-and-query"></a>Costruire la richiesta di ricerca e la query

1. Nel metodo main dell'applicazione, creare un client HTTP usando `HttpClientBuilder.create().build();`.

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Creare un oggetto `HttpEntity` per caricare l'immagine nell'API.

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Creare un oggetto `httpPost` con l'endpoint e impostare l'intestazione in modo da usare la propria chiave di sottoscrizione.

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>Ricevere ed elaborare la risposta JSON

1. Usare `HttpClient.execute()` per inviare una richiesta all'API e archiviare la risposta in un oggetto `InputStream`.
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Archiviare la stringa JSON e stampare la risposta.

```java
String json = new Scanner(stream).useDelimiter("\\A").next();
System.out.println("\nJSON Response:\n");
System.out.println(prettify(json));
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](../tutorial-bing-visual-search-single-page-app.md)