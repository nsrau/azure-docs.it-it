---
title: "Avvio rapido: Controllare l'ortografia con l'API REST e Java - Controllo ortografico Bing"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come usare l'API REST Controllo ortografico Bing e Java per eseguire il controllo ortografico e grammaticale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: e74954e33be1b2d24219ca61762dd43941415306
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366979"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Avvio rapido: Controllare l'ortografia con l'API REST Controllo ortografico Bing e Java

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020** , è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](https://aka.ms/cogsvcs/bingmove).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](https://aka.ms/cogsvcs/bingmigration).

Usare questa guida introduttiva per effettuare la prima chiamata all'API REST Controllo ortografico Bing. Questa semplice applicazione Java invia una richiesta all'API e restituisce un elenco di correzioni suggerite. 

Anche se l'applicazione è scritta in Java, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Prerequisiti

* Java Development Kit (JDK) 7 versione successiva.

* Importare il file [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) o la versione di [Gson](https://github.com/google/gson) più recente. Per l'esecuzione da riga di comando, aggiungere `.jar` alla cartella Java con la classe principale.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Creare e inizializzare un'applicazione

1. Creare un nuovo progetto Java nell'IDE o nell'editor preferito con il nome di classe scelto, quindi importare i pacchetti seguenti:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Creare variabili per l'host dell'endpoint API, il percorso e la chiave di sottoscrizione. Creare quindi le variabili per il mercato, il testo da sottoporre a controllo ortografico e una stringa per la modalità di controllo ortografico. È possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Creare e inviare una richiesta di API

1. Creare una funzione denominata `check()` per creare e inviare la richiesta all'API. All'interno di questa funzione aggiungere il codice specificato nei passaggi successivi. Creare una stringa per i parametri della richiesta:

   1. Assegnare il codice di mercato al parametro `mkt` con l'operatore `=`. 

   1. Aggiungere il parametro `mode` con l'operatore `&` e quindi assegnare la modalità di controllo ortografico. 

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

3. Aprire una connessione all'URL. Impostare il metodo di richiesta su `POST` e aggiungere i parametri della richiesta. Assicurarsi di aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

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

1. Aggiungere alla classe il metodo `prettify()`, che formatta il codice JSON per un output più leggibile.

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
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Chiamare l'API

Nella funzione main dell'applicazione chiamare il metodo `check()` creato sopra.
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

Compilare ed eseguire il progetto. Se si usa la riga di comando, usare i comandi seguenti per creare ed eseguire l'applicazione:

1. Compilare l'applicazione:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Eseguire l'applicazione:

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
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)