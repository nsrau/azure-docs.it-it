---
title: 'Guida introduttiva: Trovare traduzioni alternative - Traduzione testuale, Java'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si trovano traduzioni alternative ed esempi di termini in contesto usando l'API Traduzione testuale con Java.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: c0e7fc3d51772df40bd5da7c762c15c40ad5744f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127075"
---
# <a name="quickstart-find-alternate-translations-and-usage-with-java"></a>Guida introduttiva: Trovare traduzioni alternative ed esempi d'uso con Java

In questa guida introduttiva si trovano i dettagli relativi a possibili traduzioni alternative per un termine ed esempi d'uso di tali traduzioni usando l'API Traduzione testuale.

## <a name="prerequisites"></a>Prerequisiti

Per compilare ed eseguire il codice è necessario [JDK 7 o 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). È possibile usare l'ambiente di sviluppo integrato Java preferito, ma è sufficiente anche un editor di testo.

Per usare l'API Traduzione testuale, è necessario avere anche una chiave di sottoscrizione. Per informazioni, vedere [Come registrarsi all'API Traduzione testuale](translator-text-how-to-signup.md).

## <a name="dictionary-lookup-request"></a>Richiesta di ricerca nel dizionario

Il codice seguente ottiene le traduzioni alternative per un termine tramite il metodo [Dictionary Lookup](./reference/v3-0-dictionary-lookup.md).

1. Creare un nuovo progetto Java nell'editor di codice preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `subscriptionKey` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/* NOTE: To compile and run this code:
1. Save this file as DictionaryLookup.java.
2. Run:
    javac DictionaryLookup.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar DictionaryLookup
*/

public class DictionaryLookup {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsofttranslator.com";
    static String path = "/dictionary/lookup?api-version=3.0";

    static String params = "&from=en&to=fr";

    static String text = "great";

    public static class RequestBody {
        String Text;

        public RequestBody(String text) {
            this.Text = text;
        }
    }

    public static String Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setRequestProperty("X-ClientTraceId", java.util.UUID.randomUUID().toString());
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String DictionaryLookup () throws Exception {
        URL url = new URL (host + path + params);

        List<RequestBody> objList = new ArrayList<RequestBody>();
        objList.add(new RequestBody(text));
        String content = new Gson().toJson(objList);

        return Post(url, content);
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = DictionaryLookup ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

## <a name="dictionary-lookup-response"></a>Risposta alla richiesta di ricerca nel dizionario

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>Richiesta di esempi nel dizionario

Il codice seguente ottiene esempi contestuali dell'uso di un termine presente nel dizionario tramite il metodo [Dictionary Examples](./reference/v3-0-dictionary-examples.md).

1. Creare un nuovo progetto Java nell'editor di codice preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `subscriptionKey` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/* NOTE: To compile and run this code:
1. Save this file as DictionaryExamples.java.
2. Run:
    javac DictionaryExamples.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar DictionaryExamples
*/

public class DictionaryExamples {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsofttranslator.com";
    static String path = "/dictionary/examples?api-version=3.0";

    static String params = "&from=en&to=fr";

    static String text = "great";
    static String translation = "formidable";

    public static class RequestBody {
        String Text;
        String Translation;

        public RequestBody(String text, String translation) {
            this.Text = text;
            this.Translation = translation;
        }
    }

    public static String Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setRequestProperty("X-ClientTraceId", java.util.UUID.randomUUID().toString());
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String DictionaryExamples () throws Exception {
        URL url = new URL (host + path + params);

        List<RequestBody> objList = new ArrayList<RequestBody>();
        objList.add(new RequestBody(text, translation));
        String content = new Gson().toJson(objList);

        return Post(url, content);
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = DictionaryExamples ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

## <a name="dictionary-examples-response"></a>Risposta alla richiesta di esempi nel dizionario

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare il codice di esempio per questa guida introduttiva e per altre, incluse quelle relative alla traduzione e alla traslitterazione, e anche altri progetti di esempio di Traduzione testuale su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)
