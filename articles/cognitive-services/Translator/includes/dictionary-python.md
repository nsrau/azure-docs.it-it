---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 6c72cd270e634ca0aebff7c17d5663241428e182
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907036"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python usando l'IDE o l'editor preferito oppure creare una nuova cartella sul desktop. Copiare questo frammento di codice nel progetto o nella cartella all'interno di un file denominato `dictionary-lookup.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests uuid`.

Il primo commento indica all'interprete Python di usare la codifica UTF-8. I moduli necessari vengono quindi importati per leggere la chiave di sottoscrizione da una variabile di ambiente, costruire la richiesta http, creare un identificatore univoco e gestire la risposta JSON restituita dall'API Traduzione testuale.

## <a name="set-the-subscription-key-endpoint-and-path"></a>Impostare la chiave, l'endpoint e il percorso della sottoscrizione

Questo esempio proverà a leggere la chiave e l'endpoint della sottoscrizione di Traduzione testuale dalle variabili di ambiente `TRANSLATOR_TEXT_KEY` e `TRANSLATOR_TEXT_ENDPOINT`. Se non si ha familiarità con le variabili di ambiente, è possibile impostare `subscription_key` e `endpoint` come stringhe e le istruzioni condizionali come commenti.

Copiare questo codice nel progetto:

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

L'endpoint globale di Traduzione testuale viene impostato come `endpoint`. `path` imposta la route `dictionary/lookup` e identifica che si vuole usare la versione 3 dell'API.

`params` viene usato per impostare le lingue di origine e di output. In questo esempio vengono usate le lingue inglese e spagnolo: `en` e `es`.

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, consultare [API Traduzione testuale 3.0: Dictionary Lookup](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```python
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es'
constructed_url = endpoint + path + params
```

## <a name="add-headers"></a>Aggiungere le intestazioni

Il modo più semplice per autenticare una richiesta consiste nel passare la chiave di sottoscrizione come intestazione `Ocp-Apim-Subscription-Key`, metodo che viene appunto usato in questo esempio. In alternativa, è possibile sostituire la chiave di sottoscrizione con un token di accesso e passare il token di accesso insieme a un'intestazione `Authorization` per convalidare la richiesta. Per altre informazioni, vedere [Autenticazione](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copiare questo frammento di codice nel progetto:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nei parametri della richiesta. [Informazioni sull'autenticazione con la sottoscrizione multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-find-alternate-translations"></a>Creare una richiesta per trovare traduzioni alternative

Definire la stringa (o stringhe) per cui si vogliono trovare traduzioni alternative:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
}]
```

A questo punto, verrà creata una richiesta POST usando il modulo `requests`. La richiesta accetta tre argomenti: l'URL concatenato, le intestazioni della richiesta e il corpo della richiesta:

```python
request = requests.post(constructed_url, headers=headers, json=body)
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
python alt-translations.py
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Risposta di esempio

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se la chiave di sottoscrizione è stata impostata come hardcoded nel programma, assicurarsi di rimuoverla al termine di questa guida introduttiva.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per conoscere quali operazioni è possibile eseguire con l'API Traduzione testuale.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
