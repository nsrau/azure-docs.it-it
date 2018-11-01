---
title: "Guida introduttiva: Usare l'SDK di Ricerca Web Bing per Java"
titleSuffix: Azure Cognitive Services
description: L'SDK di Ricerca Web Bing facilita l'integrazione di Ricerca Web Bing nell'applicazione Java. In questa guida introduttiva si apprenderà come inviare una richiesta, ricevere una risposta JSON e filtrare e analizzare i risultati.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: erhopf
ms.openlocfilehash: 854cf1646d716f7cd721a7286b79a0f733243268
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416226"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-java"></a>Guida introduttiva: Usare l'SDK di Ricerca Web Bing per Java

L'SDK di Ricerca Web Bing facilita l'integrazione di Ricerca Web Bing nell'applicazione Java. In questa guida introduttiva si apprenderà come inviare una richiesta, ricevere una risposta JSON e filtrare e analizzare i risultati.

Per vedere il codice subito, gli [esempi dell'SDK di Ricerca Web Bing per Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/) sono disponibili in GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire i passaggi illustrati in questa guida introduttiva, verificare di avere quanto segue:

* [JDK 7 o 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) o lo strumento di automazione della compilazione preferito
* Una chiave di sottoscrizione

## <a name="create-a-project-and-configure-your-pom-file"></a>Creare un progetto e configurare il file POM

Creare un nuovo progetto Java usando Maven o lo strumento di automazione della compilazione preferito. Supponendo che venga usato Maven, aggiungere le righe seguenti al file POM. Sostituire tutte le istanze di `mainClass` con l'applicazione.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
          <!--Your comment
            Replace the mainClass with the path to your java application.
            It should begin with com and doesn't require the .java extension.
            For example: com.bingwebsearch.app.BingWebSearchSample. This maps to
            The following directory structure:
            src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
          -->
          <mainClass>com.path.to.your.app.APP_NAME</mainClass>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.0</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>attached</goal>
            </goals>
            <configuration>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
              <archive>
                <manifest>
                  <!--Your comment
                    Replace the mainClass with the path to your java application.
                    For example: com.bingwebsearch.app.BingWebSearchSample.java.
                    This maps to the following directory structure:
                    src/main/java/com/bingwebsearch/app/BingWebSearchSample.java.
                  -->
                  <mainClass>com.path.to.your.app.APP_NAME.java</mainClass>
                </manifest>
              </archive>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.9.0</version>
    </dependency>
    <dependency>
      <groupId>commons-net</groupId>
      <artifactId>commons-net</artifactId>
      <version>3.3</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-websearch</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies>
```

## <a name="declare-dependencies"></a>Dichiarare le dipendenze

Aprire il progetto nell'editor o nell'IDE preferito e importare le dipendenze seguenti:

```java
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchAPI;
import com.microsoft.azure.cognitiveservices.search.websearch.BingWebSearchManager;
import com.microsoft.azure.cognitiveservices.search.websearch.models.ImageObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.NewsArticle;
import com.microsoft.azure.cognitiveservices.search.websearch.models.SearchResponse;
import com.microsoft.azure.cognitiveservices.search.websearch.models.VideoObject;
import com.microsoft.azure.cognitiveservices.search.websearch.models.WebPage;
```

Se il progetto è stato creato con Maven, il pacchetto dovrebbe essere già dichiarato. In caso contrario, dichiararlo ora. Ad esempio: 

```java
package com.bingwebsearch.app
```

## <a name="declare-the-bingwebsearchsample-class"></a>Dichiarare la classe BingWebSearchSample

Dichiarare la classe `BingWebSearchSample`. Verrà inclusa la maggior parte del codice compreso il metodo `main`.  

```java
public class BingWebSearchSample {

// The code in the following sections goes here.

}
```

## <a name="construct-a-request"></a>Creare una richiesta

Il metodo `runSample`, che si trova nella classe `BingWebSearchSample`, crea la richiesta. Copiare il codice seguente nell'applicazione:

```java
public static boolean runSample(BingWebSearchAPI client) {
    /*
     * This function performs the search.
     *
     * @param client instance of the Bing Web Search API client
     * @return true if sample runs successfully
     */
    try {
        /*
         * Performs a search based on the .withQuery and prints the name and
         * url for the first web pages, image, news, and video result
         * included in the response.
         */
        System.out.println("Searched Web for \"Xbox\"");
        // Construct the request.
        SearchResponse webData = client.bingWebs().search()
            .withQuery("Xbox")
            .withMarket("en-us")
            .withCount(10)
            .execute();

// Code continues in the next section...
```

## <a name="handle-the-response"></a>Gestire la risposta

A questo punto è possibile aggiungere codice per analizzare la risposta e stampare i risultati. `name` e `url` della prima pagina Web, della prima immagine, del primo articolo e del primo video vengono stampati se sono inclusi nell'oggetto risposta.

```java
/*
* WebPages
* If the search response contains web pages, the first result's name
* and url are printed.
*/
if (webData != null && webData.webPages() != null && webData.webPages().value() != null &&
        webData.webPages().value().size() > 0) {
    // find the first web page
    WebPage firstWebPagesResult = webData.webPages().value().get(0);

    if (firstWebPagesResult != null) {
        System.out.println(String.format("Webpage Results#%d", webData.webPages().value().size()));
        System.out.println(String.format("First web page name: %s ", firstWebPagesResult.name()));
        System.out.println(String.format("First web page URL: %s ", firstWebPagesResult.url()));
    } else {
        System.out.println("Couldn't find the first web result!");
    }
} else {
    System.out.println("Didn't find any web pages...");
}
/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData != null && webData.images() != null && webData.images().value() != null &&
        webData.images().value().size() > 0) {
    // find the first image result
    ImageObject firstImageResult = webData.images().value().get(0);

    if (firstImageResult != null) {
        System.out.println(String.format("Image Results#%d", webData.images().value().size()));
        System.out.println(String.format("First Image result name: %s ", firstImageResult.name()));
        System.out.println(String.format("First Image result URL: %s ", firstImageResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first image result!");
    }
} else {
    System.out.println("Didn't find any images...");
}
/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData != null && webData.news() != null && webData.news().value() != null &&
        webData.news().value().size() > 0) {
    // find the first news result
    NewsArticle firstNewsResult = webData.news().value().get(0);
    if (firstNewsResult != null) {
        System.out.println(String.format("News Results#%d", webData.news().value().size()));
        System.out.println(String.format("First news result name: %s ", firstNewsResult.name()));
        System.out.println(String.format("First news result URL: %s ", firstNewsResult.url()));
    } else {
        System.out.println("Couldn't find the first news result!");
    }
} else {
    System.out.println("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData != null && webData.videos() != null && webData.videos().value() != null &&
        webData.videos().value().size() > 0) {
    // find the first video result
    VideoObject firstVideoResult = webData.videos().value().get(0);

    if (firstVideoResult != null) {
        System.out.println(String.format("Video Results#%s", webData.videos().value().size()));
        System.out.println(String.format("First Video result name: %s ", firstVideoResult.name()));
        System.out.println(String.format("First Video result URL: %s ", firstVideoResult.contentUrl()));
    } else {
        System.out.println("Couldn't find the first video result!");
    }
} else {
    System.out.println("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Dichiarare il metodo main

In questa applicazione il metodo main include codice che crea un'istanza del client, convalida `subscriptionKey` e chiama `runSample`. Immettere una chiave di sottoscrizione valida per l'account di Azure prima di continuare.

```java
public static void main(String[] args) {
    try {
        // Enter a valid subscription key for your account.
        final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
        // Instantiate the client.
        BingWebSearchAPI client = BingWebSearchManager.authenticate(subscriptionKey);
        // Make a call to the Bing Web Search API.
        runSample(client);
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
}
```

## <a name="run-the-program"></a>Eseguire il programma

Il passaggio finale consiste nell'eseguire il programma creato.

```console
mvn compile exec:java
```

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato di usare questo progetto, rimuovere la chiave di sottoscrizione dal codice del programma.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esempi di Java SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)

## <a name="see-also"></a>Vedere anche 

* [Informazioni di riferimento su Azure Java SDK](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/websearch)
