---
title: 'Guida introduttiva: Estrarre testo stampato (OCR) - REST, Java - Visione artificiale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si estrarrà testo stampato da un'immagine usando l'API Visione artificiale con Java.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: e7bcb1ef902bfa525a917adead0dd2fe8c44fa83
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413826"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-rest-api-and-java-in-computer-vision"></a>Guida introduttiva: Estrarre testo stampato (OCR) usando l'API REST e Java in Visione artificiale

In questa guida introduttiva si estrarrà testo stampato da un'immagine con la tecnica di riconoscimento ottico dei caratteri (OCR, Optical Character Recognition) usando l'API REST di Visione artificiale. Con il metodo [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) è possibile rilevare il testo stampato in un'immagine ed estrarre i caratteri riconosciuti in un flusso utilizzabile da computer.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver installato la piattaforma [Java&trade; Standard Edition Development Kit 7 o 8](https://aka.ms/azure-jdks) (JDK 7 o 8).
- È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere la sezione [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

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

1. Sostituire la classe pubblica `Main` con il codice seguente e quindi apportare le modifiche seguenti nel codice, dove necessario:
   1. Sostituire il valore di `subscriptionKey` con la chiave di sottoscrizione.
   1. Se necessario, sostituire il valore di `uriBase` con l'URL endpoint per il metodo [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) dall'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
   1. Facoltativamente, sostituire il valore di `imageToAnalyze` con l'URL di un'altra immagine da cui si vuole estrarre il testo stampato.
1. Salvare e quindi compilare il progetto Java.
1. Se si usa un ambiente di sviluppo integrato, eseguire `Main`. In caso contrario, aprire una finestra del prompt dei comandi e usare il comando `java` per eseguire la classe compilata. Ad esempio: `java Main`.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the West Central US region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

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

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il progetto Java con la classe compilata e le librerie importate.

## <a name="next-steps"></a>Passaggi successivi

Esaminare un'applicazione Java Swing che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime ritagliate in modo intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, di un'immagine. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esercitazione in Java per l'API Visione artificiale](../Tutorials/java-tutorial.md)
