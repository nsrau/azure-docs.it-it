---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 649af0d3b1503a5607717c4ccbadd8f1d822be32
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586875"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `get-languages.js`.

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `npm install request uuidv4`.

Questi moduli sono necessari per costruire la richiesta HTTP e creare un identificatore univoco per l'intestazione `'X-ClientTraceId'`.

## <a name="set-the-endpoint"></a>Impostare l'endpoint

Questo esempio proverà a leggere la chiave dell'endpoint di Translator dalla variabile di ambiente `TRANSLATOR_TEXT_ENDPOINT`. Se non si ha familiarità con le variabili di ambiente, è possibile impostare `endpoint` come stringa e l'istruzione condizionale come commento.

```javascript
lorum ipsum
```

## <a name="configure-the-request"></a>Configurare la richiesta

Il metodo `request()`, reso disponibile tramite il modulo di richiesta, consente di passare il metodo HTTP, l'URL, i parametri di richiesta, le intestazioni e il corpo JSON come oggetto `options`. In questo frammento di codice verrà configurata la richiesta:

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, consultare [Translator 3.0: Lingue](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```javascript
let options = {
    method: 'GET',
    baseUrl: endpoint,
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nei parametri della richiesta. [Informazioni sull'autenticazione con la sottoscrizione multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

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
node get-languages.js
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Risposta di esempio

Trovare l'abbreviazione del paese o dell'area geografica in questo [elenco di lingue](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Questo esempio è stato troncato per visualizzare un frammento di codice del risultato:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se la chiave di sottoscrizione è stata impostata come hardcoded nel programma, assicurarsi di rimuoverla al termine di questo argomento di avvio rapido.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per sapere quali operazioni è possibile eseguire con Translator.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
