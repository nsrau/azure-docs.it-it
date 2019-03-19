---
title: Come usare l'API Ricerca di anomalie con PHP - Servizi cognitivi Microsoft | Microsoft Docs
description: Ottenere informazioni ed esempi di codice per iniziare rapidamente a usare Ricerca di anomalie con PHP in Servizi cognitivi.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f65f389d93a90df1bc16d2228586beead5f96a73
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728744"
---
# <a name="use-the-anomaly-finder-api-with-php"></a>Usare l'API Ricerca di anomalie con PHP

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Questo articolo fornisce informazioni ed esempi di codice per aiutare a iniziare rapidamente a usare l'API Ricerca di anomalie con PHP per acquisire i risultati sulle anomalie per dati di serie temporali.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-php"></a>Recupero di punti di anomalie con l'API Ricerca di anomalie usando PHP
[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Esempio di dati di serie temporali
L'esempio di dati di una serie temporale è riportato di seguito.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-php-example"></a>Esempio di analisi dei dati e acquisizione dei punti di anomalie con PHP
1. Sostituire il valore `[YOUR_SUBSCRIPTION_KEY]` con la propria chiave di sottoscrizione valida.
2. Sostituire `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` con l'esempio o con i propri punti dati.
3. Eseguire il codice e controllare la risposta.

```PHP
<?php
# This sample uses the Apache HTTP client from HTTP components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new HTTP_Request2('https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection');
$url = $request->getUrl();

$requestData = '[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]';

$headers = array(
    # Request headers
    'Content-Type' => 'application/json',
    # NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '[YOUR_SUBSCRIPTION_KEY]',
);

$request->setHeader($headers);

$request->setMethod(HTTP_Request2::METHOD_POST);

# Request body
$request->setBody($requestData);

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

### <a name="example-response"></a>Risposta di esempio

Una risposta con esito positivo viene restituita in JSON. Di seguito viene riportata una risposta di esempio.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
