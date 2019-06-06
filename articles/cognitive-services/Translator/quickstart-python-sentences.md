---
title: 'Avvio rapido: Ottenere la lunghezza delle frasi, Python - API Traduzione testuale'
titleSuffix: Azure Cognitive Services
description: In questa Guida introduttiva si apprenderà come determinare la lunghezza delle frasi (in caratteri) con Python e l'API REST Traduzione testuale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 9ab2ebe5556e6850845392fc574c35809730a41a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515069"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-python"></a>Avvio rapido: Usare l'API Traduzione testuale per determinare la lunghezza delle frasi con Python

In questa Guida introduttiva si apprenderà come determinare la lunghezza delle frasi (in caratteri) con Python e l'API REST Traduzione testuale.

Per questa guida introduttiva è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa Traduzione testuale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* Python 2.7.x o 3.x
* Una chiave di sottoscrizione di Azure per Traduzione testuale

## <a name="create-a-project-and-import-required-modules"></a>Creare un progetto e importare i moduli necessari

Creare un nuovo progetto Python tramite l'editor o l'IDE preferito. Copiare quindi questo frammento di codice nel progetto all'interno di un file denominato `sentence-length.py`.

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> Se non si è mai usato questi moduli, sarà necessario installarli prima di eseguire il programma. Per installare questi pacchetti, eseguire: `pip install requests uuid`.

Il primo commento indica all'interprete Python di usare la codifica UTF-8. I moduli necessari vengono quindi importati per leggere la chiave di sottoscrizione da una variabile di ambiente, costruire la richiesta http, creare un identificatore univoco e gestire la risposta JSON restituita dall'API Traduzione testuale.

## <a name="set-the-subscription-key-base-url-and-path"></a>Impostare la chiave di sottoscrizione, l'URL di base e il percorso

Questo esempio proverà a leggere la chiave di sottoscrizione di Traduzione testuale dalla variabile di ambiente `TRANSLATOR_TEXT_KEY`. Se non si ha familiarità con le variabili di ambiente, è possibile impostare `subscriptionKey` come stringa e l'istruzione condizionale come commento.

Copiare questo codice nel progetto:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

L'endpoint globale di Traduzione testuale viene impostato come `base_url`. `path` imposta la route `breaksentence` e identifica che si vuole usare la versione 3 dell'API.

`params` in questo esempio viene usato per impostare la lingua del testo fornito. `params` non è necessario per la route `breaksentence`. Se viene escluso dalla richiesta, l'API proverà a rilevare la lingua del testo fornito e a fornire queste informazioni insieme a un punteggio di attendibilità nella risposta.

>[!NOTE]
> Per altre informazioni su endpoint, route e parametri della richiesta, vedere [API Traduzione testuale 3.0: Languages](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/breaksentence?api-version=3.0'
params = '&language=en'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Aggiungere le intestazioni

Il modo più semplice per autenticare una richiesta consiste nel passare la chiave di sottoscrizione come intestazione `Ocp-Apim-Subscription-Key`, metodo che viene appunto usato in questo esempio. In alternativa, è possibile sostituire la chiave di sottoscrizione con un token di accesso e passare il token di accesso insieme a un'intestazione `Authorization` per convalidare la richiesta. Per altre informazioni, vedere [Autenticazione](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copiare questo frammento di codice nel progetto:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-determine-sentence-length"></a>Creare una richiesta per determinare la lunghezza delle frasi

Definire la frase (o le frasi) di cui si vuole determinare la lunghezza:

```python
# You can pass more than one object in body.
body = [{
    'text': 'How are you? I am fine. What did you do today?'
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
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

La procedura è completata. È stato realizzato un semplice programma che chiamerà l'API Traduzione testuale e restituirà una risposta JSON. A questo punto, è possibile eseguire il programma:

```console
python sentence-length.py
```

Se si vuole confrontare il proprio codice con quello già disponibile, l'esempio completo è pubblicato su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Risposta di esempio

```json
[
    {
        "sentLen": [
            13,
            11,
            22
        ]
    }
]
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se la chiave di sottoscrizione è stata impostata come hardcoded nel programma, assicurarsi di rimuoverla al termine di questa guida introduttiva.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Python su GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>Vedere anche

Informazioni su come usare l'API Traduzione testuale per:

* [Tradurre un testo](quickstart-python-translate.md)
* [Traslitterare testo](quickstart-python-transliterate.md)
* [Identificare la lingua da un input](quickstart-python-detect.md)
* [Ottenere traduzioni alternative](quickstart-python-dictionary.md)
* [Ottenere un elenco di lingue supportate](quickstart-python-languages.md)
