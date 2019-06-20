---
title: "Guida introduttiva: Riconoscere l'input penna con l'API REST Riconoscimento input penna e Node.js"
description: Usare l'API Riconoscimento input penna per avviare il riconoscimento di tratti input penna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 0dc672f0efc420ab73fd923191c2bd52fb571a4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056842"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Guida introduttiva: Riconoscere l'input penna con l'API REST Riconoscimento input penna e JavaScript

Usare questa guida di avvio rapido per iniziare a usare l'API Riconoscimento input penna su tratti input penna. Questa applicazione JavaScript # invia una richiesta API contenenti i dati del tratto input penna in formato JSON e visualizza la risposta.

L'applicazione è scritta in JavaScript ed eseguita nel Web browser, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

In genere è necessario chiamare l'API da un'app di input penna. Questa guida di avvio rapido invia i dati del tratto input penna per l'esempio scritto a mano seguente da un file JSON.

![immagine di testo scritto a mano](../media/handwriting-sample.jpg)

Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Prerequisiti

- Un Web browser
- I dati di esempio del tratto input penna per questo avvio rapido sono disponibili in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. Nell'ambiente di sviluppo integrato o nell'editor preferito creare un nuovo file `.html`. Aggiungere il codice HTML di base per il codice che verrà aggiunto in un secondo momento.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. All'interno del tag `<body>` aggiungere il codice HTML seguente:
    1. Due aree di testo per visualizzare la risposta e la richiesta JSON.
    2. Un pulsante per chiamare la funzione `recognizeInk()` che verrà creato in un secondo momento.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Caricare i dati JSON di esempio

1. All'interno del tag `<script>`, creare una variabile per sampleJson. Creare quindi una funzione JavaScript denominata `openFile()` che apre una sessione di Esplora file per consentire la selezione del file JSON. Quando si fa clic sul pulsante `Recognize ink`, verrà chiamata questa funzione e inizierà la lettura del file.
2. Usare la funzione `onload()` di un oggetto `FileReader` per elaborare il file in modalità asincrona. 
    1. Sostituire tutti i caratteri `\n` o `\r` presenti nel file con una stringa vuota. 
    2. Usare `JSON.parse()` per convertire il testo in formato JSON valido
    3. Aggiornare la casella di testo `request` nell'applicazione. Usare `JSON.stringify()` per formattare la stringa JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Inviare una richiesta all'API Riconoscimento input penna

1. All'interno del tag `<script>` creare una funzione denominata `recognizeInk()`. Questa funzione chiamerà in un secondo momento l'API e aggiornerà la pagina con la risposta. Aggiungere il codice della procedura seguente all'interno di questa funzione. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Creare le variabili per l'URL dell'endpoint, la chiave di sottoscrizione e il codice JSON di esempio. Creare quindi un oggetto `XMLHttpRequest` per inviare la richiesta API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Creare la funzione return per l'oggetto `XMLHttpRequest`. Questa funzione analizzerà la risposta API da una richiesta riuscita e la visualizzerà nell'applicazione. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Creare la funzione error per l'oggetto richiesta. Questa funzione registra l'errore nella console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Creare una funzione per la proprietà `onreadystatechange` dell'oggetto richiesta. Quando lo stato di conformità dell'oggetto richiesta cambia, verranno applicate le funzioni return e error sopra descritte.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Inviare la richiesta API. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key` e impostare `content-type` su `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
