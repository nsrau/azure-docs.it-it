---
title: Avvio rapido della libreria client Java di Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: f155868483a0b00ed5ecb6f02ad3ee5440e6e45a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625458"
---
Usare questa guida di avvio rapido per effettuare la prima ricerca di immagini tramite la libreria client di Ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione Java invia una query di ricerca di immagini, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.

Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti

La versione più recente di [Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK)

Installare le dipendenze della libreria client di Ricerca immagini Bing usando Maven, Gradle o un altro sistema di gestione delle dipendenze. Il file POM di Maven richiede la dichiarazione seguente:

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo progetto Java nell'ambiente di sviluppo integrato o nell'editor preferito e aggiungere le importazioni seguenti all'implementazione della classe:

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. Nel metodo principale creare variabili per la chiave di sottoscrizione e per il termine di ricerca. Creare quindi un'istanza del client di Ricerca immagini Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Inviare una richiesta di ricerca all'API

1. Tramite `bingImages().search()`, inviare la richiesta HTTP contenente la query di ricerca. Salvare la risposta come `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analizzare e visualizzare il risultato

Analizzare le immagini restituite nella risposta.
Se la risposta contiene i risultati della ricerca, archiviare il primo risultato e stamparne i dettagli, ad esempio l'URL di anteprima e l'URL originale, insieme al numero totale di immagini restituite.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni su Ricerca immagini Bing](../../overview.md)  
* [Provare una demo interattiva online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Esempi Java per l'SDK di Servizi cognitivi di Azure](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Documentazione di Servizi cognitivi di Azure](../../../index.yml)
* [Informazioni di riferimento per l'API Ricerca immagini Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)