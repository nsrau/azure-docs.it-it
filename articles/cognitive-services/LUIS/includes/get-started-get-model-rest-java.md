---
title: Ottenere il modello con una chiamata REST in Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 7d1915025c31a0c201da87b1d4bde9b52659e5e3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655525"
---
## <a name="prerequisites"></a>Prerequisiti

* Azure Language Understanding: chiave di creazione della risorsa di 32 caratteri e URL dell'endpoint di creazione. Usare il [portale di Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) o l'[interfaccia della riga di comando di Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importare l'app [Pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) dal repository GitHub `Azure-Samples/cognitive-services-sample-data-files`.
* L'ID applicazione di LUIS per l'app Pizza importata. L'ID applicazione viene visualizzato nel dashboard delle applicazioni.
* L'ID della versione all'interno dell'applicazione che riceve le espressioni.
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) o un altro IDE

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Cambiare il modello a livello di codice

1. Creare una nuova cartella che contenga il progetto Java, ad esempio `java-model-with-rest`.

1. Creare una sottodirectory denominata `lib` e copiarla nelle librerie Java seguenti nella sottodirectory `lib`:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Creare un file denominato `Model.java`. Aggiungere il codice seguente:

```java
import java.io.*;
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

// To compile, execute this command at the console:
//      Windows: javac -cp ";lib/*" Model.java
//      macOs: javac -cp ":lib/*" Model.java
//      Linux: javac -cp ":lib/*" Model.java

// To run, execute this command at the console:
//      Windows: java -cp ";lib/*" Model
//      macOs: java -cp ":lib/*" Model
//      Linux: java -cp ":lib/*" Model

public class Model
{
    public static void main(String[] args)
    {
        try
        {
            //////////
            // Values to modify.

            // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
            String AppId = "YOUR-APP-ID";

            // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
            String Key = "YOUR-AUTHORING-KEY";

            // YOUR-AUTHORING-ENDPOINT: Replace this with your authoring key endpoint.
            // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
            String Endpoint = "YOUR-AUTHORING-ENDPOINT";

            // NOTE: Replace this your version number. The Pizza app uses a version number of "0.1".
            String Version = "0.1";
            //////////

            // The list of utterances to add, in JSON format.
            String Utterances = "[{'text': 'order a pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 12}]}, {'text': 'order a large pepperoni pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'PizzaType', 'startCharIndex': 14, 'endCharIndex': 28}, {'entityName': 'Size', 'startCharIndex': 8, 'endCharIndex': 12}]}, {'text': 'I want two large pepperoni pizzas on thin crust', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'PizzaType', 'startCharIndex': 17, 'endCharIndex': 32}, {'entityName': 'Size', 'startCharIndex': 11, 'endCharIndex': 15}, {'entityName': 'Quantity', 'startCharIndex': 7, 'endCharIndex': 9}, {'entityName': 'Crust', 'startCharIndex': 37, 'endCharIndex': 46}]}]";

            // Create the URLs for uploading example utterances and for training.
            URIBuilder addUtteranceURL = new URIBuilder(Endpoint + "luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
            URIBuilder trainURL = new URIBuilder(Endpoint + "luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");
            URI addUtterancesURI = addUtteranceURL.build();
            URI trainURI = trainURL.build();


            // Add the utterances.

            // Create the request.
            HttpClient addUtterancesClient = HttpClients.createDefault();
            HttpPost addUtterancesRequest = new HttpPost(addUtterancesURI);

            // Add the headers.
            addUtterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            addUtterancesRequest.setHeader("Content-type","application/json");

            // Add the body.
            StringEntity stringEntity = new StringEntity(Utterances);
            addUtterancesRequest.setEntity(stringEntity);

            // Execute the request and obtain the response.
            HttpResponse addUtterancesResponse = addUtterancesClient.execute(addUtterancesRequest);
            HttpEntity addUtterancesEntity = addUtterancesResponse.getEntity();

            // Print the response on the console.
            if (addUtterancesEntity != null)
            {
                System.out.println(EntityUtils.toString(addUtterancesEntity));
            }


            // Train the model.

            // Create the request.
            HttpClient trainClient = HttpClients.createDefault();
            HttpPost trainRequest = new HttpPost(trainURI);

            // Add the headers.
            trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            trainRequest.setHeader("Content-type","application/json");

            // Execute the request and obtain the response.
            HttpResponse trainResponse = trainClient.execute(trainRequest);
            HttpEntity trainEntity = trainResponse.getEntity();

            // Print the response on the console.
            if (trainEntity != null)
            {
                System.out.println(EntityUtils.toString(trainEntity));
            }


            // Get the training status.


            // Create the request.
            HttpClient trainStatusClient = HttpClients.createDefault();
            HttpGet trainStatusRequest = new HttpGet(trainURI);

            // Add the headers.
            trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            trainStatusRequest.setHeader("Content-type","application/json");

            // Execute the request and obtain the response.
            HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
            HttpEntity trainStatusEntity = trainStatusResponse.getEntity();

            // Print the response on the console.
            if (trainStatusEntity != null)
            {
                System.out.println(EntityUtils.toString(trainStatusEntity));
            }
        }

        // Display errors if they occur.
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

1. Sostituire i valori che iniziano con `YOUR-` con i propri valori.

    |Informazioni|Scopo|
    |--|--|
    |`YOUR-APP-ID`| L'ID app di LUIS. |
    |`YOUR-AUTHORING-KEY`|La chiave di creazione di 32 caratteri.|
    |`YOUR-AUTHORING-ENDPOINT`| L'endpoint dell'URL di creazione. Ad esempio: `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Il nome della risorsa è stato impostato quando è stata creata la risorsa.|

    Le chiavi e le risorse assegnate sono visibili nel portale LUIS nella sezione Gestisci della pagina **Risorse di Azure**. L'ID app è disponibile nella stessa sezione Gestisci della pagina **Impostazioni applicazione**.

1. Da un prompt dei comandi nella stessa directory in cui è stato creato il file `Model.java`, immettere il comando seguente per compilare il file Java:

    ::: zone pivot="client-operating-system-linux"

    ```console
    javac -cp ":lib/*" Model.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    javac -cp ":lib/*" Model.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    javac -cp ";lib/*" Model.java
    ```

    ::: zone-end

1. Eseguire l'applicazione Java dalla riga di comando immettendo il testo seguente al prompt dei comandi:

    ::: zone pivot="client-operating-system-linux"

    ```console
    java -cp ":lib/*" Model
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    java -cp ":lib/*" Model
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    java -cp ";lib/*" Model
    ```

    ::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare la cartella del progetto dal file system.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per un'app](../luis-concept-best-practices.md)