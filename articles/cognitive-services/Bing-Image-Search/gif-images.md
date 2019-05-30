---
title: Cercare immagini .gif usando l'API Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
description: Usare l'API Ricerca immagini Bing per cercare immagini .gif nel Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 0a2040424aa70f30831e214ce0b05d21414ff45c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389400"
---
# <a name="search-for-gif-images"></a>Cercare immagini GIF 

L'API Ricerca immagini Bing consente anche di cercare nell'intero Web le immagini più rilevanti con estensione gif.  Gli sviluppatori possono integrare coinvolgenti immagini con estensione gif in vari scenari di conversazione. 

L'URL seguente è una query per cercare immagini animate con estensione gif.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
Il parametro [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) specifica i termini di ricerca.  La query precedente specifica anche `animatedGif` usando il parametro di filtro [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).

Per vedere esempi di risultati, usare l'URL seguente per cercare su bing.com.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Parametri di query

Per altre informazioni sulle opzioni e sui parametri di query, vedere [Image Search API reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters) (Riferimento sull'API Ricerca immagini). Più avanti, in [Esempio di ricerca di immagini animate con estensione gif attraverso Java](#gifExample), è riportato un esempio.

## <a name="tips-and-suggestions"></a>Suggerimenti

- È possibile specificare i parametri [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) e [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize). È consigliabile impostare maxFileSize = 2000000 perché la maggior parte delle immagini con estensione gif presenti nell'indice sono di dimensioni inferiori a 2 MB.  In questo modo è possibile controllare le dimensioni dei dati se la larghezza di banda costituisce un problema, ad esempio negli scenari di cellulari.
- Per contribuire al miglioramento delle prestazioni percepite, innanzitutto caricare l'anteprima, quindi caricare l'URL di origine.  
- Per la prima esecuzione o l'esperienza di pagina di destinazione in cui non ci sono ancora query dell'utente, provare a usare le ricerche di immagini con estensione gif di tendenza dall'[API delle immagini di tendenza](trending-images.md).
- Ci sono tre impostazioni per il parametro [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch).  L'opzione `strict` blocca il contenuto per adulti.
- Per un elenco completo delle lingue e delle località supportate, vedere [mkt](supported-countries-markets.md).
- *AnimatedGifHttps* restituisce solo immagini animate con estensione gif provenienti da un indirizzo HTTPS. Per sicurezza, molte applicazioni richiedono la connessione a collegamenti Web esterni tramite HTTPS. Ad esempio, l'Apple App Store richiede la connessione ai servizi Web su HTTPS, che crittografa i dati sicuri dell'utente durante il transito.

<a name="gifExample" />

## <a name="example-search-for-animated-gif-using-java"></a>Esempio di ricerca di immagini animate con estensione gif attraverso Java

L'URL seguente cerca immagini animate con estensione gif: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Come illustrato nell'esempio seguente, la query tramite URL richiede l'intestazione [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers).

L'esempio Java seguente compila e invia la richiesta.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Risultati
Il codice ottiene i risultati seguenti come oggetti JSON:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](quickstarts/csharp.md)
- [Tutorial Image Search single-page application](tutorial-bing-image-search-single-page-app.md) (Esercitazione sull'applicazione a pagina singola di Ricerca immagini)
