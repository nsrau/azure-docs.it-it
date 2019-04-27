---
title: Uso di token di informazioni dettagliate - Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Viene illustrato come usare un token di informazioni dettagliate di un'immagine con l'API Ricerca visiva Bing per ottenere informazioni dettagliate su un'immagine.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 4/05/2019
ms.author: scottwhi
ms.openlocfilehash: e42e56e6361b1fde7ab13655d3c57a90d7235938
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831929"
---
# <a name="use-an-insights-token-to-get-insights-for-an-image"></a>Usare un token di insights per ottenere informazioni dettagliate per un'immagine

L'API Ricerca visiva Bing restituisce informazioni su un'immagine fornita. È possibile fornire l'immagine usando l'URL dell'immagine, un token di informazioni dettagliate, oppure caricando l'immagine. Per informazioni su queste opzioni, vedere [Informazioni sull'API Ricerca visiva Bing](overview.md). Questo articolo illustra l'uso di un token di informazioni dettagliate. Per esempi che illustrano come caricare un'immagine per ottenere informazioni dettagliate, vedere le guide introduttive ([C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node. js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md)).

Se si invia ricerca visiva Bing un URL o il token di immagine, di seguito mostra i dati del modulo che è necessario includere nel corpo del POST. I dati del modulo devono includere il `Content-Disposition` intestazione ed è necessario impostare relativo `name` parametro "knowledgeRequest". Per informazioni dettagliate di `imageInfo` oggetti, vedere la richiesta:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Gli esempi in questo articolo illustrano come usare il token di informazioni dettagliate. Ottenere il token di insights da un' `Image` dell'oggetto in un /images/risposta dell'API di ricerca. Per informazioni su come ottenere il token di insights, vedere [qual è l'API ricerca immagini Bing?](../Bing-Image-Search/overview.md).

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```

Per esempi che usano il token di informazioni dettagliate, vedere [C#](#use-with-c) | [Java](#use-with-java) | [Node.js](#use-with-nodejs) | [Python](#use-with-python).

## <a name="use-with-c"></a>Usare conC#

### <a name="c-prerequisites"></a>C#prerequisiti

- Qualsiasi versione di [Visual Studio 2017](https://www.visualstudio.com/downloads/) per ottenere questo codice in esecuzione su Windows.
- Una sottoscrizione di Azure. Per questa Guida introduttiva, è possibile usare una [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) chiave di sottoscrizione o una chiave di sottoscrizione a pagamento.

## <a name="run-the-application"></a>Eseguire l'applicazione

Per eseguire l'applicazione, seguire questa procedura:

1. Creare una soluzione di console in Visual Studio.
2. Sostituire il contenuto di Program.cs con il codice riportato in questa Guida introduttiva.
3. Sostituire il valore di `accessKey` con la chiave di sottoscrizione.
4. Sostituire il valore `insightsToken` con un token di informazioni dettagliate da una risposta /images/search.
5. Eseguire il programma.

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

## <a name="use-with-java"></a>Uso con Java

### <a name="java-prerequisites"></a>Prerequisiti di Java

- È necessario utilizzare [JDK 7 o 8](https://aka.ms/azure-jdks) per compilare ed eseguire questo codice. È possibile usare un IDE di Java se si dispone di un preferito, ma è sufficiente un editor di testo.
- Per questa Guida introduttiva, è possibile usare una [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) chiave di sottoscrizione o una chiave di sottoscrizione a pagamento.

## <a name="run-the-java-application"></a>Eseguire l'applicazione Java

Per eseguire l'applicazione, seguire questa procedura:

1. Scaricare o installare il [libreria Gson Java](https://github.com/google/gson). È anche possibile ottenere Gson tramite Maven.
2. Creare un nuovo progetto Java nell'IDE o nell'editor preferito.
3. Aggiungere il codice fornito in un file denominato `VisualSearch.java`.
4. Sostituire il valore di `subscriptionKey` con la chiave di sottoscrizione.
5. Eseguire il programma.

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// https://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }


}
```

## <a name="use-with-nodejs"></a>Uso con Node. js

### <a name="nodejs-prerequisites"></a>Prerequisiti di Node. js

- È necessario disporre [Node. js 6](https://nodejs.org/en/download/) per eseguire questo codice.
- Per questa Guida introduttiva, è possibile usare una [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) chiave di sottoscrizione o una chiave di sottoscrizione a pagamento.

## <a name="run-the-javascript-application"></a>Eseguire l'applicazione JavaScript

Per eseguire l'applicazione, seguire questa procedura:

1. Creare una cartella del progetto (o usare l'ambiente di sviluppo integrato o l'editor preferito).
2. Da un terminale o un prompt dei comandi passare alla cartella appena creata.
3. Installare i moduli di richiesta:
  
   ```
   npm install request  
   ```
1. Installare i moduli dati di form:  
   ```
   npm install form-data  
   ```
1. Creare un file denominato GetVisualInsights.js e aggiungervi il codice seguente.
1. Sostituire il valore di `subscriptionKey` con la chiave di sottoscrizione.
1. Eseguire il programma.  
   ```
   node GetVisualInsights.js
   ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```

## <a name="use-with-python"></a>Uso di Python

### <a name="python-prerequisites"></a>Prerequisiti di Python

- È necessario disporre [Python 3](https://www.python.org/) per eseguire questo codice.
- Per questa guida introduttiva, è possibile usare una chiave di sottoscrizione [di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) o una chiave di sottoscrizione a pagamento.

## <a name="run-the-python-application"></a>Eseguire l'applicazione Python

Per eseguire l'applicazione, seguire questa procedura:

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato o nell'editor preferito.
2. Creare un file denominato visualsearch.py e aggiungere il codice illustrato in questo Avvio rapido.
3. Sostituire il valore di `SUBSCRIPTION_KEY` con la chiave di sottoscrizione.
4. Eseguire il programma.

```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un'app Web a pagina singola con Ricerca visiva](tutorial-bing-visual-search-single-page-app.md)  
[Che cos'è l'API ricerca visiva Bing?](overview.md)  
[Prova servizi cognitivi](https://aka.ms/bingvisualsearchtryforfree)  
[Ottenere una chiave di accesso per la versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Immagini - ricerca visiva](https://aka.ms/bingvisualsearchreferencedoc)
