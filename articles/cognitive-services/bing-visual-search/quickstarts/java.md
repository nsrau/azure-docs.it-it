---
title: Guida introduttiva a Java per l'API Ricerca visiva Bing | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Viene illustrato come caricare un'immagine nell'API Ricerca visiva Bing e ottenere informazioni dettagliate sull'immagine.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 41e0855b126ca6e54d0a487a88fe59a0be6f72f6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071996"
---
# <a name="your-first-bing-visual-search-query-in-java"></a>La prima query di Ricerca visiva Bing in Java

L'API Ricerca visiva Bing restituisce informazioni su un'immagine fornita. È possibile fornire l'immagine usando l'URL dell'immagine o un token di informazioni dettagliate oppure caricando l'immagine. Per informazioni su queste opzioni, vedere [Informazioni sull'API Ricerca visiva Bing](../overview.md) Questo articolo illustra come caricare un'immagine. Caricare un'immagine può essere utile negli scenari per dispositivi mobili in cui si acquisisce un'immagine di un punto di riferimento ben noto e si ottengono le relative informazioni. Le informazioni dettagliate, ad esempio, possono includere curiosità sul punto di riferimento. 

Se si carica un'immagine locale, la figura seguente illustra i dati del modulo che è necessario includere nel corpo del POST. I dati dl modulo devono includere l'intestazione Content-Disposition. Il parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Questo articolo include una semplice applicazione console che invia una richiesta all'API Ricerca visiva Bing e visualizza i risultati della ricerca JSON. Mentre l'applicazione è scritta in Java, l'API è un servizio Web RESTful compatibile con qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e di analizzare il codice JSON. 


## <a name="prerequisites"></a>Prerequisiti

Per compilare ed eseguire questo codice è necessario [JDK 7 o 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Se si preferisce, è possibile usare un ambiente IDE Java, ma è sufficiente un editor di testo.

Per questa guida introduttiva, è possibile usare una chiave di sottoscrizione [di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) o una chiave di sottoscrizione a pagamento.

## <a name="running-the-application"></a>Esecuzione dell'applicazione

Di seguito viene illustrato come caricare l'immagine usando MultipartEntityBuilder in Java.

Per eseguire l'applicazione, seguire questa procedura:

1. Scaricare o installare la [libreria gson](https://github.com/google/gson). È anche possibile ottenerla tramite Maven.
2. Creare un nuovo progetto Java nell'IDE o nell'editor preferito.
3. Aggiungere il codice fornito in un file denominato `VisualSearch.java`.
4. Sostituire il valore di `subscriptionKey` con la chiave di sottoscrizione.
4. Sostituire il valore di `imagePath` con il percorso dell'immagine da caricare.
5. Eseguire il programma.


```java
package uploadimage;

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

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class UploadImage2 {

    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere";
    static String imagePath = "<pathtoyourimagetouploadgoeshere>";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addBinaryBody("image", new File(imagePath))
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

## <a name="next-steps"></a>Passaggi successivi

[Ottenere informazioni dettagliate su un'immagine usando un token di informazioni dettagliate](../use-insights-token.md)  
[Esercitazione sul caricamento dell'immagine di Ricerca visiva Bing di Ricerca visiva Bing](../tutorial-visual-search-image-upload.md)
[Panoramica su Ricerca visiva Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Panoramica di Ricerca visiva Bing](../overview.md)  
[Prova](https://aka.ms/bingvisualsearchtryforfree)  
[Ottenere una chiave di accesso per la versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Informazioni di riferimento sull'API Ricerca visiva Bing](https://aka.ms/bingvisualsearchreferencedoc)

