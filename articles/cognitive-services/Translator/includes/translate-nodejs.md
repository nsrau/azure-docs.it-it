---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: cf2dbbd7c5f5684059b646e24a8fbc808b7698d1
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405356"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto usando l'IDE o l'editor preferito o una nuova cartella con un nome file `translate-text.js` sul desktop. Copiare quindi questo frammento di codice nel progetto/file:

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `npm install request uuidv4`.

Questi moduli sono necessari per costruire la richiesta HTTP e creare un identificatore univoco per l'intestazione `'X-ClientTraceId'`.

## <a name="set-the-subscription-key-and-endpoint"></a>Impostare la chiave e l'endpoint della sottoscrizione

Questo esempio proverà a leggere la chiave e l'endpoint della sottoscrizione di Translator dalle variabili di ambiente `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` e `TRANSLATOR_TEXT_ENDPOINT`. Se non si ha familiarità con le variabili di ambiente, è possibile impostare `subscriptionKey` e `endpoint` come stringhe e le istruzioni condizionali come commenti.

Copiare questo codice nel progetto:

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>Configurare la richiesta

Il metodo `request()`, reso disponibile tramite il modulo di richiesta, consente di passare il metodo HTTP, l'URL, i parametri di richiesta, le intestazioni e il corpo JSON come oggetto `options`. In questo frammento di codice verrà configurata la richiesta:

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, consultare [Translator 3.0: Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'translate',
    qs: {
      'api-version': '3.0',
      'to': ['de', 'it']
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Hello World!'
    }],
    json: true,
};
```

Il modo più semplice per autenticare una richiesta consiste nel passare la chiave di sottoscrizione come intestazione `Ocp-Apim-Subscription-Key`, metodo che viene appunto usato in questo esempio. In alternativa, è possibile sostituire la chiave di sottoscrizione con un token di accesso e passare il token di accesso insieme a un'intestazione `Authorization` per convalidare la richiesta.

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nelle intestazioni della richiesta.

Per altre informazioni, vedere [Autenticazione](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Eseguire una richiesta e stampare la risposta

A questo punto, verrà creata la richiesta usando il metodo `request()`. Accetta l'oggetto `options` creato nella sezione precedente come primo argomento, quindi stampa la risposta JSON modificata.

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> In questo esempio viene definita la richiesta HTTP nell'oggetto `options`. Tuttavia, il modulo di richiesta supporta anche metodi pratici, ad esempio `.post` e `.get`. Per altre informazioni, vedere i [metodi pratici](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è completata. È stato realizzato un semplice programma che chiamerà Translator e restituirà una risposta JSON. A questo punto, è possibile eseguire il programma:

```console
node translate-text.js
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Risposta di esempio

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se la chiave di sottoscrizione è stata impostata come hardcoded nel programma, assicurarsi di rimuoverla al termine di questa guida introduttiva.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per sapere quali operazioni è possibile eseguire con Translator.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
