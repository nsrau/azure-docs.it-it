---
title: Guida introduttiva all'API Emozioni di Java per Android | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Emozioni con Java per Android in Servizi cognitivi.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 0e7d3991b195a83a8b87e306b3b34fbed2098581
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018027"
---
# <a name="emotion-api-java-for-android-quick-start"></a>Guida introduttiva all'API Emozioni di Java per Android

> [!IMPORTANT]
> L'anteprima dell'API Video terminerà il 30 ottobre 2017. La nuova [anteprima API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) consente di estrarre informazioni dettagliate dai video e ottimizzare le esperienze di individuazione del contenuto, ad esempio risultati della ricerca, tramite l'individuazione di parole, visi, caratteri ed emozioni. [Altre informazioni](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Questo articolo include informazioni ed esempi di codice per iniziare rapidamente a usare il [metodo di riconoscimento delle emozioni](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) nella libreria client Android dell'API Emozioni. L'esempio illustra come usare Java per riconoscere le emozioni espresse dalle persone. 

## <a name="prerequisites"></a>prerequisiti
* Per ottenere l'API Emozioni di Java per Android SDK, fare clic [qui](https://github.com/Microsoft/Cognitive-emotion-android)
* Per ottenere la chiave di sottoscrizione gratuita fare clic [qui](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-java-for-android-example-request"></a>Richiesta di esempio del riconoscimento delle emozioni di Java per Android

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Risposta di esempio delle emozioni riconosciute
Una chiamata completata con successo restituisce una matrice di voci relative ai visi con associati punteggi relativi alle emozioni, ordinate in base alle dimensioni del rettangolo del viso in ordine decrescente. Una risposta vuota indica che non sono stati individuati visi. Una voce contenente emozioni include i campi seguenti:
* faceRectangle: posizione del rettangolo del viso nell'immagine.
* scores: punteggi delle emozioni per ogni viso nell'immagine. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
