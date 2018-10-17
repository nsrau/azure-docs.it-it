---
title: 'Guida introduttiva: Progetto Anteprima URL, JavaScript'
titlesuffix: Azure Cognitive Services
description: Esempio di script per iniziare a usare rapidamente l'API Anteprima URL Bing con JavaScript.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: b10e33a0947057c35bb6227cc43f92c4f0a56ceb
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869466"
---
# <a name="quickstart-url-preview-in-javascript"></a>Guida introduttiva: Anteprima URL in JavaScript 

L'applicazione a pagina singola seguente usa JavaScript per creare un'anteprima URL per il sito SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Prerequisiti

Ottenere una chiave di accesso per la versione di valutazione gratuita di [Lab di Servizi cognitivi](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Scenario di codice
L'esempio JavaScript seguente include un oggetto di input casella di testo in cui l'utente inserisce l'URL da visualizzare in anteprima.  Quando l'utente fa clic sul pulsante **Anteprima**, il metodo onclick instrada a `getPreview`, dove il codice genera una richiesta Web all'endpoint **UrlPreview**.

Il codice crea una richiesta *XMLHttpRequest*, aggiunge l'intestazione e la chiave *Ocp-Apim-Subscription-Key* e invia la richiesta.  Aggiunge un gestore eventi asincrono per elaborare la risposta.

Se la risposta viene restituita correttamente, il gestore assegna il testo JSON della risposta al paragrafo `demo` della pagina. Gli altri elementi della risposta vengono impostati sui paragrafi seguenti per la visualizzazione.

**Risposta JSON non elaborata**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**Demo in esecuzione**

![Esempio di URL Preview in JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Esecuzione dell'applicazione

Per eseguire l'applicazione:

1. Sostituire il valore `YOUR-SUBSCRIPTION-KEY` con una chiave di accesso valida per la sottoscrizione.
2. Salvare il codice HTML e lo script in un file HTML.
3. Eseguire la pagina Web in un browser.
4. Usare l'URL esistente oppure immetterne un altro nella casella di testo.
5. Fare clic sul pulsante **Anteprima**.

**Codice sorgente:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Passaggi successivi
- [Guida introduttiva in C#](csharp.md)
- [Guida introduttiva in Java](java-quickstart.md)
- [Guida introduttiva in Node](node-quickstart.md)
- [Guida introduttiva in Python](python-quickstart.md)
