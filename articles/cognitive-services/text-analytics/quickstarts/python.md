---
title: "Guida introduttiva: Uso di Python per chiamare l'API Analisi del testo"
titleSuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice per iniziare a usare l'API Analisi del testo nei Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: cd8a895bef1580b10f50f1c5d31c35f36b6806ce
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214586"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Guida introduttiva: Uso di Python per chiamare il servizio cognitivo Analisi del testo 
<a name="HOLTop"></a>

Questa procedura dettagliata illustra come [rilevare la lingua](#Detect), [analizzare il sentiment](#SentimentAnalysis) e [estrarre le frasi chiave](#KeyPhraseExtraction) usando l'[API Analisi del testo](//go.microsoft.com/fwlink/?LinkID=759711)con Python.

È possibile eseguire questo esempio come Jupyter Notebook in [MyBinder](https://mybinder.org) facendo clic sulla notifica di avvio del Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Per la documentazione tecnica delle API, vedere le [definizioni delle API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

È inoltre necessario avere la [chiave di accesso e l'endpoint](../How-tos/text-analytics-how-to-access-key.md) generati automaticamente durante l'iscrizione. 

Per continuare con questa procedura dettagliata, sostituire `subscription_key` con una chiave di sottoscrizione valida ottenuta in precedenza.


```python
subscription_key = None
assert subscription_key
```

Verificare quindi che l'area in `text_analytics_base_url` corrisponda a quella usata durante la configurazione del servizio. Se si usa una chiave di prova gratuita, non occorre apportare alcuna modifica.


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>Rilevamento delle lingue

L'API Rilevamento lingua rileva la lingua di un documento di testo usando il [metodo per il rilevamento della lingua](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). L'endpoint del servizio dell'API Rilevamento lingua per l'area dell'utente è disponibile tramite l'URL seguente:


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


Il payload dell'API è costituito da un elenco di `documents`, ognuno dei quali contiene a sua volta un attributo `id` e `text`. L'attributo `text` archivia il testo da analizzare. 

Sostituire il dizionario `documents` con qualsiasi altro testo per il rilevamento della lingua. 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Le righe successive del codice richiamano l'API Rilevamento lingua usando la libreria `requests` di Python per determinare la lingua dei documenti.


```python
import requests
from pprint import pprint
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}


Queste righe di codice eseguono il rendering dei dati JSON in formato tabella HTML.


```python
from IPython.display import HTML
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analizzare la valutazione

L'API Analisi del sentiment rileva il sentiment di un set di record di testo, usando l'apposito [metodo](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). L'esempio seguente assegna un punteggio a due documenti, uno in inglese e un altro in spagnolo.

L'endpoint del servizio per l'analisi del sentiment è disponibile per l'area geografica dell'utente tramite l'URL seguente:


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


Come nell'esempio del rilevamento lingua, il servizio viene fornito con un dizionario con una chiave `documents` costituita da un elenco dei documenti. Ogni documento è una tupla costituita da `id`, `text` da analizzare e `language` del testo. È possibile usare l'API Rilevamento lingua dalla sezione precedente per popolare questo campo. 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

L'API del sentiment può ora essere usata per analizzare i documenti per il sentiment.


```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

    {'documents': [{'id': '1', 'score': 0.7673527002334595},
                   {'id': '2', 'score': 0.18574094772338867},
                   {'id': '3', 'score': 0.5}],
     'errors': []}


Il punteggio del sentiment per un documento è compreso tra $0$ e $1$, con il punteggio più alto che indica un sentiment più positivo.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Estrarre le frasi chiave

L'API Estrazione frasi chiave consente di estrarre le frasi chiave da un documento di testo usando il [metodo per le frasi chiave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). In questa sezione della procedura dettagliata vengono estratte le frasi chiave per i documenti in inglese e in spagnolo.

È possibile accedere all'endpoint del servizio di estrazione delle frasi chiave tramite l'URL seguente:


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


La raccolta di documenti è la stessa raccolta usata per l'analisi del sentiment.


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```


    {'documents': [
        {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
        {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
        {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
        {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
     'errors': []
    }


È possibile eseguire ancora una volta il rendering dell'oggetto JSON una tabella HTML usando le righe di codice seguenti:


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-entities"></a>Identificare le entità

L'API Entità identifica le entità note in un documento di testo usando il metodo [Entities](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634). L'esempio seguente identifica le entità per i documenti in inglese.

È possibile accedere all'endpoint del servizio di collegamento delle entità tramite l'URL seguente:


```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1-preview/entities


La raccolta di documenti è riportata qui di seguito:


```python
documents = {'documents' : [
  {'id': '1', 'text': 'Jeff bought three dozen eggs because there was a 50% discount.'},
  {'id': '2', 'text': 'The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.'}
]}
```

A questo punto, i documenti possono essere inviati all'API Analisi del testo per ricevere la risposta.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche  

 [Panoramica di Analisi del testo](../overview.md)  
 [Domande frequenti](../text-analytics-resource-faq.md)
