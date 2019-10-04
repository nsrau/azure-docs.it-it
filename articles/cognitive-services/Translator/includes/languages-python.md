---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 7f14a12d5de64206f64e8c7205beb2c59c4f1f2a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906926"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `get-languages.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests uuid`.

Il primo commento indica all'interprete Python di usare la codifica UTF-8. I moduli necessari vengono quindi importati per leggere la chiave di sottoscrizione da una variabile di ambiente, costruire la richiesta http, creare un identificatore univoco e gestire la risposta JSON restituita dall'API Traduzione testuale.

## <a name="set-the-endpoint-and-path"></a>Impostare l'endpoint e il percorso

Questo esempio proverà a leggere la chiave della sottoscrizione di Traduzione testuale dalla variabile di ambiente `TRANSLATOR_TEXT_ENDPOINT`. Se non si ha familiarità con le variabili di ambiente, è possibile impostare `endpoint` come stringa e l'istruzione condizionale come commento.

```python
endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

L'endpoint globale di Traduzione testuale viene impostato come `endpoint`. `path` imposta la route `languages` e identifica che si vuole usare la versione 3 dell'API.

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, vedere [API Traduzione testuale 3.0: Languages](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
path = '/languages?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>Aggiungere le intestazioni

La richiesta di ottenere lingue supportate non richiede autenticazione. Impostare `Content-type` su `application/json` e aggiungere `X-ClientTraceId` per identificare in modo univoco la richiesta.

Copiare questo frammento di codice nel progetto:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nei parametri della richiesta. [Informazioni sull'autenticazione con la sottoscrizione multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Creare una richiesta per ottenere un elenco di lingue supportate

Creare una richiesta POST usando il modulo `requests`. La richiesta accetta due argomenti: l'URL concatenato e le intestazioni della richiesta:

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>Stampare la risposta

L'ultimo passaggio consiste nello stampare i risultati. Questo frammento di codice migliora i risultati ordinando le chiavi, impostando i rientri e dichiarando i separatori di elementi e chiavi.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è completata. È stato realizzato un semplice programma che chiamerà l'API Traduzione testuale e restituirà una risposta JSON. A questo punto, è possibile eseguire il programma:

```console
python get-languages.py
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

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

Se la chiave di sottoscrizione è stata impostata come hardcoded nel programma, assicurarsi di rimuoverla al termine di questa guida introduttiva.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per conoscere quali operazioni è possibile eseguire con l'API Traduzione testuale.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
