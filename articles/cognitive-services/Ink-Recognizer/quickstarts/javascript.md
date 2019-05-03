---
title: "Guida introduttiva: Riconoscere l'input penna digitale con l'API REST per il riconoscimento di input penna e Node. js"
description: Usare l'API di riconoscimento input penna per avviare tratti input penna.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: febc6e72ed40541a230c606a3ec96a8bffef5036
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026378"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Guida introduttiva: Riconoscere l'input penna digitale con l'API REST per il riconoscimento di input penna e JavaScript

Usare questa Guida introduttiva per iniziare a usare l'API di riconoscimento input penna su tratti input penna digitale. Questa applicazione JavaScript invia una richiesta di API che contiene i dati tratti input penna in formato JSON e Visualizza la risposta.

Anche se questa applicazione è scritta in Javascript ed eseguita nel web browser, l'API è un servizio web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

In genere è necessario chiamare l'API da un'app di input penna digitale. Questa Guida introduttiva invia i dati di tratti input penna per il seguente codice di esempio scritto a mano da un file JSON.

![un'immagine di testo scritto a mano](../media/handwriting-sample.jpg)

Il codice sorgente per questo avvio rapido è disponibile su [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Prerequisiti

- Un Web browser
- Dati tratti input penna di esempio per questa Guida introduttiva sono disponibili nella [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Creare una nuova applicazione

1. Nel tuo IDE o editor preferito, creare un nuovo `.html` file. Quindi aggiungervi codice HTML di base per il codice che verrà aggiunto in un secondo momento.
    
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

2. All'interno di `<body>` tag, aggiungere il codice html seguente:
    1. Due aree di testo per visualizzare la risposta e una richiesta JSON.
    2. Un pulsante per la chiamata di `recognizeInk()` funzione che verrà creato in un secondo momento.
    
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

1. All'interno di `<script>` tag, creare una variabile per il sampleJson. Creare quindi una funzione JavaScript denominata `openFile()` che si apre Esplora file in modo che è possibile selezionare il file JSON. Quando il `Recognize ink` si fa clic sul pulsante, chiamare questa funzione e iniziare la lettura del file.
2. Usare un `FileReader` dell'oggetto `onload()` funzione per elaborare il file in modo asincrono. 
    1. Sostituire eventuali `\n` o `\r` caratteri nel file con una stringa vuota. 
    2. Usare `JSON.parse()` per convertire il testo in formato JSON valido
    3. Aggiornamento di `request` casella di testo nell'applicazione. Usare `JSON.stringify()` per formattare la stringa JSON. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Inviare una richiesta all'API di riconoscimento di input penna

1. All'interno di `<script>` tag, creare una funzione denominata `recognizeInk()`. Questa funzione verrà in un secondo momento chiamare l'API e aggiornare la pagina con la risposta. Aggiungere il codice di questa procedura all'interno di questa funzione. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Creare variabili per l'URL dell'endpoint, la chiave di sottoscrizione e il file JSON di esempio. Creare quindi un `XMLHttpRequest` oggetto per inviare la richiesta dell'API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Creare la funzione restituita per le `XMLHttpRequest` oggetto. Questa funzione analizzerà la risposta dell'API da una richiesta ha esito positivo e li visualizzi nell'applicazione. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Creare la funzione di errore per l'oggetto richiesta. Questa funzione registra l'errore nella console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Creare una funzione per l'oggetto di richiesta `onreadystatechange` proprietà. Quando viene modificato lo stato di conformità dell'oggetto richiesta, verranno applicate le funzioni rendimento ed errore precedente.
            
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
    
    5. Inviare la richiesta dell'API. Aggiungere la chiave di sottoscrizione per il `Ocp-Apim-Subscription-Key` intestazione e impostare il `content-type` a `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
