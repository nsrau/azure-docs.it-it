---
title: 'Traduzione testuale: Convertire testo da un sistema di scrittura a un altro con Python | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: In questa guida introduttiva si converte il testo in una lingua da un sistema di scrittura a un altro usando l'API Traduzione testuale con Python in Servizi cognitivi.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 41fb0f72c5974a1ab034680a820dca6aa7bbdc6d
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2018
ms.locfileid: "43770882"
---
# <a name="quickstart-transliterate-text-with-python"></a>Guida introduttiva: Traslitterare testo con Python

In questa guida introduttiva si converte il testo in una lingua da un sistema di scrittura a un altro usando l'API Traduzione testuale.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire il codice è necessario [Python 3.x](https://www.python.org/downloads/).

Per usare l'API Traduzione testuale, è necessario avere anche una chiave di sottoscrizione. Per informazioni, vedere [Come registrarsi all'API Traduzione testuale](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Richiesta di traslitterazione

Il codice seguente converte il testo in una lingua da un sistema di scrittura a un altro tramite il metodo [Transliterate](./reference/v3-0-transliterate.md).

1. Creare un nuovo progetto Python nell'editor di codice preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `subscriptionKey` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = '&language=ja&fromScript=jpan&toScript=latn';

# Transliterate "good afternoon".
text = 'こんにちは'

def transliterate (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = transliterate (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="transliterate-response"></a>Risposta alla richiesta di traslitterazione

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare il codice di esempio per questa guida introduttiva e per altre, incluse quelle relative alla traduzione e all'identificazione della lingua, e anche altri progetti di esempio di Traduzione testuale su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Python su GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
