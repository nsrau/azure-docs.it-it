---
title: Guida introduttiva all'API Emozioni di PHP | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Emozioni con PHP in Servizi cognitivi.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 987d5a3eedaa17f1127be34e5f90ec2456fab99b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019398"
---
# <a name="emotion-api-php-quick-start"></a>Guida introduttiva all'API Emozioni di PHP

> [!IMPORTANT]
> L'anteprima dell'API Video terminerà il 30 ottobre 2017. La nuova [anteprima API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) consente di estrarre informazioni dettagliate dai video e ottimizzare le esperienze di individuazione del contenuto, ad esempio risultati della ricerca, tramite l'individuazione di parole, visi, caratteri ed emozioni. [Altre informazioni](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Questo articolo contiene informazioni ed esempi di codice per iniziare rapidamente a usare PHP e i [metodi di riconoscimento dell'API Emozioni](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) per riconoscere le emozioni espresse da una o più persone in un'immagine. 

## <a name="prerequisite"></a>Prerequisito
* Per ottenere la chiave di sottoscrizione gratuita fare clic [qui](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-php-example-request"></a>Richiesta di esempio di riconoscimento emozioni con PHP

Modificare l'URL REST per usare il percorso da cui sono state ottenute le chiavi di sottoscrizione, modificare il corpo dell'URL di un'immagine che si vuole testare e sostituire il valore "Ocp-Apim-Subscription-Key" con la propria chiave di sottoscrizione.

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

// NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
//   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
//   URL below with "westcentralus".
$request = new Http_Request2('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody('{"url": "http://www.example.com/images/image.jpg"}');

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
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

