---
title: 'Guida introduttiva: Eseguire una ricerca con PHP - API Ricerca Web Bing'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si apprenderà come eseguire la prima chiamata all'API Ricerca Web Bing usando PHP e ricevere una risposta JSON.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: 821d6a1b7df43c3eaddff503f6666e72251deec0
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127902"
---
# <a name="quickstart-use-php-to-call-the-bing-web-search-api"></a>Guida introduttiva: Usare PHP per chiamare l'API Ricerca Web Bing  

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca Web Bing e ricevere una risposta JSON in meno di 10 minuti.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire i passaggi illustrati in questa guida introduttiva, verificare di avere quanto segue:

* [PHP 5.6.x](http://php.net/downloads.php) o versione successiva
* Una chiave di sottoscrizione  

## <a name="enable-secure-http-support"></a>Abilitare il supporto HTTP sicuro

Prima di iniziare, individuare `php.ini` e rimuovere il carattere di commento da questa riga:

```
;extension=php_openssl.dll
```

## <a name="create-a-project-and-define-variables"></a>Creare un progetto e definire le variabili  

Creare un nuovo progetto PHP nell'ambiente di sviluppo integrato o nell'editor preferito. Non dimenticare di aggiungere i tag di apertura e chiusura `<?php` e `?>`.

Prima di continuare, è necessario impostare alcune variabili. Verificare che il valore di `$endpoint` sia corretto e sostituire il valore di `$accesskey` con una chiave di sottoscrizione valida del proprio account Azure. È possibile personalizzare la query di ricerca sostituendo il valore per `$term`.

```php
$accessKey = 'enter key here';
$endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/search';
$term = 'Microsoft Cognitive Services';
```

## <a name="construct-a-request"></a>Creare una richiesta

Questo codice dichiara una funzione denominata `BingWebSearch` che viene usata per creare le richieste all'API Ricerca Web Bing. La funzione accetta tre argomenti: `$url`, `$key` e `$query`.

```php
function BingWebSearch ($url, $key, $query) {
    /* Prepare the HTTP request.
     * NOTE: Use the key 'http' even if you are making an HTTPS request.
     * See: http://php.net/manual/en/function.stream-context-create.php.
     */
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ('http' => array (
                          'header' => $headers,
                           'method' => 'GET'));

    // Perform the request and get a JSON response.
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);

    // Extract Bing HTTP headers.
    $headers = array();
    foreach ($http_response_header as $k => $v) {
        $h = explode(":", $v, 2);
        if (isset($h[1]))
            if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                $headers[trim($h[0])] = trim($h[1]);
    }

    return array($headers, $result);
}
```

## <a name="make-a-request-and-print-the-response"></a>Eseguire una richiesta e stampare la risposta

Questo codice convalida la chiave di sottoscrizione, esegue una richiesta e stampa la risposta.

```php
// Validates the subscription key.
if (strlen($accessKey) == 32) {

    print "Searching the Web for: " . $term . "\n";
    // Makes the request.
    list($headers, $json) = BingWebSearch($endpoint, $accessKey, $term);

    print "\nRelevant Headers:\n\n";
    foreach ($headers as $k => $v) {
        print $k . ": " . $v . "\n";
    }
    // Prints JSON encoded response.
    print "\nJSON Response:\n\n";
    echo json_encode(json_decode($json), JSON_PRETTY_PRINT);

} else {

    print("Invalid Bing Search API subscription key!\n");
    print("Please paste yours into the source code.\n");

}
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

L'ultimo passaggio consiste nel convalidare il codice ed eseguirlo. Se si vuole confrontare il proprio codice con quello già disponibile, ecco il programma completo:

```php
<?php
$accessKey = 'enter key here';
$endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/search';
$term = 'Microsoft Cognitive Services';

function BingWebSearch ($url, $key, $query) {
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ('http' => array (
                          'header' => $headers,
                           'method' => 'GET'));
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);
    $headers = array();
    foreach ($http_response_header as $k => $v) {
        $h = explode(":", $v, 2);
        if (isset($h[1]))
            if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                $headers[trim($h[0])] = trim($h[1]);
    }
    return array($headers, $result);
}

if (strlen($accessKey) == 32) {
    print "Searching the Web for: " . $term . "\n";
    list($headers, $json) = BingWebSearch($endpoint, $accessKey, $term);
    print "\nRelevant Headers:\n\n";
    foreach ($headers as $k => $v) {
        print $k . ": " . $v . "\n";
    }
    print "\nJSON Response:\n\n";
    echo json_encode(json_decode($json), JSON_PRETTY_PRINT);

} else {
    print("Invalid Bing Search API subscription key!\n");
    print("Please paste yours into the source code.\n");
}
?>
```

## <a name="sample-response"></a>Risposta di esempio

Le risposte dell'API Ricerca Web Bing vengono restituite in formato JSON. Questa risposta di esempio è stata troncata in modo da visualizzare un singolo risultato.  

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
