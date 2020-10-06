---
title: "Guida introduttiva: Uso di Python per chiamare l'API Analisi del testo"
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come usare Python per ottenere informazioni ed esempi di codice per iniziare rapidamente a usare l'API Analisi del testo in Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 0ef870b6b2d3b88b13c16c8c2acbfcee7ed551c1
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527242"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Uso dell'API REST di Python per chiamare il servizio cognitivo Analisi del testo 
<a name="HOLTop"></a>

Usare questo argomento di avvio rapido per iniziare ad analizzare la lingua con l'API REST Analisi del testo e Python. Questo articolo illustra come [rilevare la lingua](#Detect), [analizzare il sentiment](#SentimentAnalysis), [estrarre le frasi chiave](#KeyPhraseExtraction) e [identificare le entità collegate](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Prerequisiti 

* [Python 3.x](https://python.org)

* La libreria di richieste di Python.
    
    È possibile installare la libreria con questo comando:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare una nuova applicazione Python nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere le istruzioni import seguenti al file.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Creare le variabili per l'endpoint e la chiave della sottoscrizione di Azure della risorsa.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

Le sezioni successive descrivono come chiamare ciascuna delle funzionalità dell'API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Rilevamento delle lingue

Aggiungere `/text/analytics/v3.0/languages` all'endpoint di base di Analisi del testo per formare l'URL di rilevamento delle lingue. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

Il payload dell'API è costituito da un elenco di `documents`, che sono tuple contenenti un `id` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

Usare la libreria di richieste per inviare i documenti all'API. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key` e inviare la richiesta con `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Output

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analizzare la valutazione

Per rilevare il sentiment (che può essere positivo o negativo) di un set di documenti, aggiungere `/text/analytics/v3.0/sentiment` all'endpoint di base di Analisi del testo per formare l'URL di rilevamento delle lingue. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Come nell'esempio del rilevamento delle lingue, creare un dizionario con una chiave `documents` costituita da un elenco di documenti. Ogni documento è una tupla costituita da `id`, `text` da analizzare e `language` del testo. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

Usare la libreria di richieste per inviare i documenti all'API. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key` e inviare la richiesta con `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Output

Il punteggio del sentiment per un documento è compreso tra 0,0 e 1,0, con il punteggio più alto che indica un sentiment più positivo.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Estrarre le frasi chiave
 
Per estrarre le frasi chiave da un set di documenti, aggiungere `/text/analytics/v3.0/keyPhrases` all'endpoint di base di Analisi del testo per formare l'URL di rilevamento delle lingue. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

La raccolta di documenti è la stessa usata per l'esempio di analisi del sentiment.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

Usare la libreria di richieste per inviare i documenti all'API. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key` e inviare la richiesta con `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Output

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identificare le entità

Per identificare entità note (persone, luoghi e cose) in documenti di testo, aggiungere `/text/analytics/v3.0/entities/recognition/general` all'endpoint di base di Analisi del testo per formare l'URL di rilevamento delle lingue. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Creare una raccolta di documenti, come negli esempi precedenti. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

Usare la libreria di richieste per inviare i documenti all'API. Aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key` e inviare la richiesta con `requests.post()`.

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Output

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche 

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti](../text-analytics-resource-faq.md)
