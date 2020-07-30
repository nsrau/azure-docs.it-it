---
title: 'Avvio rapido: Estrarre testo stampato - REST, Java'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si estrarrà testo stampato da un'immagine usando l'API Visione artificiale con Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: 4b09a956f4956548a47d87d93404b8304bc287fa
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322262"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-java"></a>Avvio rapido: Estrarre testo stampato (OCR) usando l'API REST Visione artificiale e Java

> [!NOTE]
> Se si estrae testo in lingua inglese, valutare la possibilità di usare la nuova [operazione di lettura](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text). È disponibile un [avvio rapido per Java](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/java-hand-text). 

In questa guida di avvio rapido si estrarrà testo stampato da un'immagine con il riconoscimento ottico dei caratteri (OCR) usando l'API REST Visione artificiale. Con il metodo [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) è possibile rilevare il testo stampato in un'immagine ed estrarre i caratteri riconosciuti in un flusso utilizzabile da computer.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Piattaforma [Java&trade; Standard Edition Development Kit 7 o 8](https://aka.ms/azure-jdks) (JDK 7 o 8)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="creare una risorsa Visione artificiale"  target="_blank">creare una risorsa Visione artificiale <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.
* [Creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per l'URL dell'endpoint, denominati rispettivamente `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`.

## <a name="create-and-run-the-sample-application"></a>Creare ed eseguire l'applicazione di esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Creare un nuovo progetto Java nell'IDE o nell'editor preferito. Se l'opzione è disponibile, creare il progetto Java da un modello di applicazione della riga di comando.
1. Importare le librerie seguenti nel progetto Java. Se si usa Maven, verranno fornite le coordinate di Maven per ogni libreria.
   - [Client HTTP Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [HTTP Apache core](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Libreria JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Aggiungere le istruzioni `import` seguenti al file che contiene la classe pubblica `Main` del progetto.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Sostituire la classe pubblica `Main` con il codice seguente.
1. Facoltativamente, sostituire il valore di `imageToAnalyze` con l'URL di un'altra immagine da cui si vuole estrarre il testo stampato.
1. Salvare e quindi compilare il progetto Java.
1. Se si usa un ambiente di sviluppo integrato, eseguire `Main`. In caso contrario, aprire una finestra del prompt dei comandi e usare il comando `java` per eseguire la classe compilata. Ad esempio: `java Main`.

```java
public class Main {

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + "vision/v3.0/ocr";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
            "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation", "true");

            // Request parameters.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo viene restituita in JSON. L'applicazione di esempio analizza e visualizza una risposta con esito positivo nella finestra della console, come nell'esempio seguente:

```json
REST Response:

{
  "orientation": "Up",
  "regions": [{
    "boundingBox": "21,16,304,451",
    "lines": [
      {
        "boundingBox": "28,16,288,41",
        "words": [{
          "boundingBox": "28,16,288,41",
          "text": "NOTHING"
        }]
      },
      {
        "boundingBox": "27,66,283,52",
        "words": [{
          "boundingBox": "27,66,283,52",
          "text": "EXISTS"
        }]
      },
      {
        "boundingBox": "27,128,292,49",
        "words": [{
          "boundingBox": "27,128,292,49",
          "text": "EXCEPT"
        }]
      },
      {
        "boundingBox": "24,188,292,54",
        "words": [{
          "boundingBox": "24,188,292,54",
          "text": "ATOMS"
        }]
      },
      {
        "boundingBox": "22,253,297,32",
        "words": [
          {
            "boundingBox": "22,253,105,32",
            "text": "AND"
          },
          {
            "boundingBox": "144,253,175,32",
            "text": "EMPTY"
          }
        ]
      },
      {
        "boundingBox": "21,298,304,60",
        "words": [{
          "boundingBox": "21,298,304,60",
          "text": "SPACE."
        }]
      },
      {
        "boundingBox": "26,387,294,37",
        "words": [
          {
            "boundingBox": "26,387,210,37",
            "text": "Everything"
          },
          {
            "boundingBox": "249,389,71,27",
            "text": "else"
          }
        ]
      },
      {
        "boundingBox": "127,431,198,36",
        "words": [
          {
            "boundingBox": "127,431,31,29",
            "text": "is"
          },
          {
            "boundingBox": "172,431,153,36",
            "text": "opinion."
          }
        ]
      }
    ]
  }],
  "textAngle": 0,
  "language": "en"
}
```

## <a name="next-steps"></a>Passaggi successivi

Esplorare un'applicazione Swing Java che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime con ritaglio intelligente e rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive nelle immagini.

> [!div class="nextstepaction"]
> [Esercitazione in Java per l'API Visione artificiale](../Tutorials/java-tutorial.md)

* Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
