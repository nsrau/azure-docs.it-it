---
title: 'Guida introduttiva: Usare un modello di dominio - REST, PHP - Visione artificiale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si identificano luoghi di interesse in un'immagine in base a un modello di dominio usando l'API Visione artificiale con PHP.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 40a4fa90442a5771f6b221a25ddca46d3ebc4f5c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629492"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-php-in-computer-vision"></a>Guida introduttiva: Usare un modello di dominio usando l'API REST e PHP in Visione artificiale

In questa guida introduttiva si identificheranno luoghi di interesse o celebrità in un'immagine archiviata in remoto in base a un modello di dominio usando l'API REST di Visione artificiale. Con il metodo [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), è possibile applicare un modello specifico di un dominio per riconoscere contenuti all'interno di un'immagine.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver installato [PHP](https://secure.php.net/downloads.php).
- È necessario aver installato [Pear](https://pear.php.net).
- È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere la sezione [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Creare ed eseguire l'esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Installare il pacchetto [`HTTP_Request2`](http://pear.php.net/package/HTTP_Request2) PHP5.
   1. Aprire una finestra del prompt dei comandi come amministratore.
   1. Eseguire il comando seguente:

      ```console
      pear install HTTP_Request2
      ```

   1. Dopo l'installazione del pacchetto, chiudere la finestra del prompt dei comandi.

1. Copiare il codice seguente in un editor di testo.
1. Apportare le modifiche seguenti al codice, dove necessario:
    1. Sostituire il valore di `subscriptionKey` con la chiave di sottoscrizione.
    1. Se necessario, sostituire il valore di `uriBase` con l'URL endpoint per il metodo [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) dall'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
    1. Facoltativamente, sostituire il valore di `imageUrl` con l'URL di un'altra immagine da analizzare.
    1. Facoltativamente, sostituire il valore del parametro `domain` della richiesta con `celebrites` se si vuole usare il modello di dominio `celebrities` anziché il modello di dominio `landmarks`.
1. Salvare il codice in un file con estensione `.php`. Ad esempio: `use-domain-model.php`.
1. Aprire una finestra del browser con supporto per PHP.
1. Trascinare e rilasciare il file nella finestra del browser.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
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

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo viene restituita in JSON. Il sito Web di esempio analizza e visualizza una risposta con esito positivo nella finestra del browser, come nell'esempio seguente:

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, eliminare il file e quindi disinstallare il pacchetto `HTTP_Request2` PHP5. Per disinstallare il pacchetto NuGet, seguire questa procedura:

1. Aprire una finestra del prompt dei comandi come amministratore.
2. Eseguire il comando seguente:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Dopo la disinstallazione del pacchetto, chiudere la finestra del prompt dei comandi.

## <a name="next-steps"></a>Passaggi successivi

Esaminare l'API Visione artificiale usata per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esplorare l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
