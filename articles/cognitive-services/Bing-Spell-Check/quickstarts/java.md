---
title: "Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Java"
titlesuffix: Azure Cognitive Services
description: Introduzione all'uso dell'API REST Controllo ortografico Bing per controllare l'ortografia e la grammatica.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: eeec278a1c9e4888aa4a63af3d9c86e7a8c1615c
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502916"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Guida introduttiva: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Java

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing. Questa semplice applicazione Java invia una richiesta all'API e restituisce un elenco di correzioni suggerite. L'applicazione è scritta in Java, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Prerequisiti

* Java Development Kit (JDK) 7 versione successiva.

* Importare il file [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) o la versione di [Gson](https://github.com/google/gson) più recente. Per l'esecuzione da riga di comando, aggiungere `.jar` alla cartella Java con la classe principale.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Creare e inizializzare un'applicazione

1. Creare un nuovo progetto Java nell'IDE o nell'editor preferito con il nome della classe scelto e quindi importare i pacchetti seguenti.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Creare variabili per l'host dell'endpoint API, il percorso e la chiave di sottoscrizione. Creare quindi variabili per il mercato, il testo da sottoporre a controllo ortografico e una stringa per la modalità di controllo ortografico.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Creare e inviare una richiesta di API

1. Creare una funzione denominata `check()` per creare e inviare la richiesta all'API. All'interno della funzione, eseguire questa procedura. Creare una stringa per i parametri della richiesta. Aggiungere il parametro `?mkt=` alla stringa del mercato e il parametro `&mode=` alla modalità di controllo ortografico.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Creare un URL combinando l'host dell'endpoint, il percorso e la stringa di parametri. Creare un nuovo oggetto `HttpsURLConnection`.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Aprire una connessione all'URL. Impostare il metodo della richiesta su `POST`. Aggiungere i parametri della richiesta. Assicurarsi di aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Creare un nuovo oggetto `DataOutputStream` e inviare la richiesta all'API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formattare e leggere la risposta dell'API

1. Aggiungere questo metodo alla classe. Formatta il codice JSON in modo che l'output sia più leggibile.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Creare un oggetto `BufferedReader` e leggere la risposta dell'API. Stamparla nella console.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line);
    }
    in.close();
    ```

## <a name="call-the-api"></a>Chiamare l'API

Nella funzione main dell'applicazione chiamare il metodo check() creato sopra.

    ```java
    public static void main(String[] args) {
        try {
            check();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

Compilare ed eseguire il progetto.

Se si usa la riga di comando, usare i comandi seguenti per creare ed eseguire l'applicazione.

**Compilare:**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Eseguire:**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../tutorials/spellcheck.md)

- [Informazioni sull'API Controllo ortografico Bing](../overview.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
