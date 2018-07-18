---
title: Guida introduttiva per Servizi cognitivi di Azure, API Ricerca entità Bing in PHP | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Ricerca entità Bing in Servizi cognitivi Microsoft in Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 448064de764775c497de467b235837d66ef7093b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377353"
---
# <a name="quickstart-for-microsoft-bing-entity-search-api-with-php"></a>Guida introduttiva per l'API Ricerca entità Microsoft Bing con PHP 
<a name="HOLTop"></a>

Questo articolo spiega come usare l'[API Ricerca entità Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) con PHP.

## <a name="prerequisites"></a>prerequisiti

Per eseguire questo codice è necessario [PHP 5.6.x](http://php.net/downloads.php).

È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con l'**API Ricerca entità Bing**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="search-entities"></a>Entità di ricerca

Per eseguire l'applicazione, seguire questa procedura.

1. Creare un nuovo progetto PHP nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
$subscriptionKey = 'ENTER KEY HERE';

$host = "https://api.cognitive.microsoft.com";
$path = "/bing/v7.0/entities";

$mkt = "en-US";
$query = "italian restaurants near me";

function search ($host, $path, $key, $mkt, $query) {

    $params = '?mkt=' . $mkt . '&q=' . urlencode ($query);

    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'GET'
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    return $result;
}

$result = search ($host, $path, $subscriptionKey, $mkt, $query);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Risposta**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[Torna all'inizio](#HOLTop)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione su Ricerca entità Bing](../tutorial-bing-entities-search-single-page-app.md)
> [Panoramica su Ricerca entità Bing](../search-the-web.md )
> [Informazioni di riferimento per l'API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
