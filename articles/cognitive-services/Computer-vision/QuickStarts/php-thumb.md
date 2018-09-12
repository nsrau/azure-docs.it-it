---
title: API Visione artificiale - Guida introduttiva alla generazione di un'anteprima con PHP | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si genera un'anteprima da un'immagine usando Visione artificiale con PHP in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 7170e469d042c4406a555fddaa25bff53236f365
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772267"
---
# <a name="quickstart-generate-a-thumbnail---rest-php"></a>Guida introduttiva: Generare un'anteprima - REST, PHP

In questa guida introduttiva si genera un'anteprima da un'immagine usando Visione artificiale.

## <a name="prerequisites"></a>Prerequisiti

Per usare Visione artificiale, è necessario avere una chiave di sottoscrizione. A tale scopo, vedere [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Come ottenere chiavi di sottoscrizione).

## <a name="get-thumbnail-request"></a>Richiesta di generazione di un'anteprima

Con il [metodo Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), è possibile generare un'anteprima di un'immagine. Si specificano l'altezza e la larghezza, che possono essere diverse rispetto alle proporzioni dell'immagine di input. Visione artificiale usa il ritaglio intelligente per identificare l'area di interesse in modo intelligente e generare le coordinate di ritaglio in base a tale area.

Per eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Modificare `uriBase` in modo da usare l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione, se necessario.
1. Facoltativamente, impostare `imageUrl` sull'immagine che si vuole analizzare.
1. Salvare il file con estensione `.php`.
1. Aprire il file in una finestra del browser con supporto per PHP.

Questo esempio usa il pacchetto [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) di PHP5.

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="get-thumbnail-response"></a>Risposta alle richiesta di generazione di un'anteprima

Una risposta con esito positivo contiene il file binario dell'immagine di anteprima. Se invece la richiesta ha esito negativo, la risposta contiene un codice di errore e un messaggio per determinarne la causa.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le API Visione artificiale usate per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente le API Visione artificiale, provare la [console di test delle API aperta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esaminare le API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
