---
title: "Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Java | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca dall'istanza di Ricerca personalizzata Bing in Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 6a470979ce4276d510f1a89bc9bc192d609c2083
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973510"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando Java

Usare questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca dall'istanza di Ricerca personalizzata Bing. L'applicazione è scritta in Java, ma l'API Ricerca personalizzata Bing è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md) per altre informazioni.

- La versione più recente di [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html)  

- [Libreria Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo progetto Java nell'ambiente di sviluppo integrato o nell'editor preferito e importare le librerie seguenti.

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Creare una classe denominata `CustomSrchJava` e creare le variabili per la chiave di sottoscrizione, l'endpoint ricerca personalizzata e l'ID di configurazione personalizzata dell'istanza di ricerca. È possibile usare l'endpoint globale seguente o l'endpoint [sottodominio personalizzato](../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Creare un'altra classe denominata `SearchResults` per contenere la risposta dall'istanza di Ricerca personalizzata Bing.

    ```csharp
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Creare una funzione denominata `prettify()` per formattare la risposta JSON dall'API Ricerca personalizzata Bing.

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>Inviare e ricevere una richiesta di ricerca 

1. Creare una funzione denominata `SearchWeb()` che invia una richiesta e restituisce un oggetto `SearchResults`. Creare l'URL richiesta combinando le informazioni di ID di configurazione personalizzato, query ed endpoint. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. Creare un flusso e archiviare la risposta JSON in un oggetto `SearchResults`.

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. Nel metodo main dell'applicazione chiamare `SearchWeb()` con il termine di ricerca, 

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. Eseguire il programma.
    
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](./tutorials/custom-search-web-page.md)
