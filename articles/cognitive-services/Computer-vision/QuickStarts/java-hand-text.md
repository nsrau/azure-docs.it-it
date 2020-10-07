---
title: 'Avvio rapido:  Visione artificiale 2.1 e 3.0 - Estrarre testo stampato e scritto a mano - REST, Java'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione dell'avvio rapido si estrarrà testo stampato e scritto a mano da un'immagine usando l'API Visione artificiale con Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: e9f31a16434c8b54e13f4d52a412afbc90fb5c6b
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760887"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-java"></a>Avvio rapido: Estrarre testo stampato e scritto a mano usando l'API REST Visione artificiale e Java

In questa guida di avvio rapido si estrarrà testo stampato e scritto a mano da un'immagine usando l'API REST Visione artificiale. Con i metodi [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) e [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) è possibile rilevare il testo in un'immagine ed estrarre i caratteri riconosciuti in un flusso di caratteri leggibile al computer.

Il metodo [Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) viene eseguito in modo asincrono. Questo metodo non restituisce alcuna informazione nel corpo di una risposta con esito positivo. Il metodo Batch Read restituisce invece un URI nel valore del campo di intestazione `Operation-Location` della risposta. È quindi possibile chiamare questo URI, che rappresenta l'API [Get Read Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750), per controllare lo stato e restituire i risultati della chiamata al metodo Read.

---

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
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    import org.apache.http.util.EntityUtils;
    import org.apache.http.Header;
    import org.json.JSONObject;
    ```

1. Sostituire la classe pubblica `Main` con il codice seguente.
1. Facoltativamente, sostituire il valore di `imageToAnalyze` con l'URL di un'altra immagine da cui si vuole estrarre il testo.
1. Salvare e quindi compilare il progetto Java.
1. Se si usa un ambiente di sviluppo integrato, eseguire `Main`. In caso contrario, aprire una finestra del prompt dei comandi e usare il comando `java` per eseguire la classe compilata. Ad esempio: `java Main`.

```java

public class Main {

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");

    private static String uriBase = endpoint + "/vision/v3.0/read/analyze";

    private static String imageToAnalyze =
            "https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpTextClient = HttpClientBuilder.create().build();
        CloseableHttpClient httpResultClient = HttpClientBuilder.create().build();;

        System.out.println("Endpoint:         " + endpoint);
        System.out.println("Subscription key: " + subscriptionKey);

        try {
            // This operation requires two REST API calls. One to submit the image
            // for processing, the other to retrieve the text found in the image.

            URIBuilder builder = new URIBuilder(uriBase);

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Two REST API methods are required to extract text.
            // One method to submit the image for processing, the other method
            // to retrieve the text found in the image.

            // Call the first REST API method to detect the text.
            HttpResponse response = httpTextClient.execute(request);

            // Check for success.
            if (response.getStatusLine().getStatusCode() != 202) {
                // Format and display the JSON error message.
                HttpEntity entity = response.getEntity();
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
                return;
            }

            // Store the URI of the second REST API method.
            // This URI is where you can get the results of the first REST API method.
            String operationLocation = null;

            // The 'Operation-Location' response header value contains the URI for
            // the second REST API method.
            Header[] responseHeaders = response.getAllHeaders();
            for (Header header : responseHeaders) {
                if (header.getName().equals("Operation-Location")) {
                    operationLocation = header.getValue();
                    break;
                }
            }

            if (operationLocation == null) {
                System.out.println("\nError retrieving Operation-Location.\nExiting.");
                System.exit(1);
            }

            // If the first REST API method completes successfully, the second
            // REST API method retrieves the text written in the image.
            //
            // Note: The response may not be immediately available. Text
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text.
            // You may need to wait or retry this operation.

            System.out.println("\nText submitted.\n" +
                    "Waiting 10 seconds to retrieve the recognized text.\n");
            Thread.sleep(10000);

            // Call the second REST API method and get the response.
            HttpGet resultRequest = new HttpGet(operationLocation);
            resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            HttpResponse resultResponse = httpResultClient.execute(resultRequest);
            HttpEntity responseEntity = resultResponse.getEntity();

            if (responseEntity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(responseEntity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Text recognition result response: \n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```


## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo viene restituita in JSON. L'applicazione di esempio analizza e visualizza una risposta con esito positivo nella finestra della console, come nell'esempio seguente:


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              },
              {
                "boundingBox": [
                  540,
                  665,
                  926,
                  679,
                  926,
                  825,
                  541,
                  823
                ],
                "text": "quick",
                "confidence": 0.57
              },
              {
                "boundingBox": [
                  1125,
                  686,
                  1569,
                  700,
                  1569,
                  838,
                  1125,
                  828
                ],
                "text": "brown",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  1674,
                  703,
                  1966,
                  711,
                  1966,
                  851,
                  1674,
                  841
                ],
                "text": "fox",
                "confidence": 0.442
              },
              {
                "boundingBox": [
                  2083,
                  714,
                  2580,
                  725,
                  2579,
                  876,
                  2083,
                  855
                ],
                "text": "jumps",
                "confidence": 0.878
              }
            ]
          },
          {
            "boundingBox": [
              187,
              1062,
              485,
              1056,
              486,
              1120,
              189,
              1126
            ],
            "text": "over",
            "words": [
              {
                "boundingBox": [
                  190,
                  1064,
                  439,
                  1059,
                  441,
                  1122,
                  192,
                  1126
                ],
                "text": "over",
                "confidence": 0.37
              }
            ]
          },
          {
            "boundingBox": [
              664,
              1008,
              1973,
              1023,
              1969,
              1178,
              664,
              1154
            ],
            "text": "the lazy dog!",
            "words": [
              {
                "boundingBox": [
                  668,
                  1008,
                  923,
                  1015,
                  923,
                  1146,
                  669,
                  1117
                ],
                "text": "the",
                "confidence": 0.909
              },
              {
                "boundingBox": [
                  1107,
                  1018,
                  1447,
                  1023,
                  1445,
                  1178,
                  1107,
                  1162
                ],
                "text": "lazy",
                "confidence": 0.853
              },
              {
                "boundingBox": [
                  1639,
                  1024,
                  1974,
                  1023,
                  1971,
                  1170,
                  1636,
                  1178
                ],
                "text": "dog!",
                "confidence": 0.41
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Esplorare ora un'applicazione Swing Java che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime con ritaglio intelligente e rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive nelle immagini.

> [!div class="nextstepaction"]
> [Esercitazione in Java per l'API Visione artificiale](../Tutorials/java-tutorial.md)

* Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).
