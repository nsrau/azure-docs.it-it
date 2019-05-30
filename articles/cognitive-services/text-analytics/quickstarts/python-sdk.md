---
title: "Guida introduttiva: Chiamare il servizio Analisi del testo usando l'SDK di Python"
titleSuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice per iniziare a usare l'API Analisi del testo in Servizi cognitivi di Azure.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: b319abf22f9aa4cdd9a5fef91be0628672d47bd4
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297790"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Guida introduttiva: Chiamare il servizio Analisi del testo usando l'SDK di Python 
<a name="HOLTop"></a>

Usare questo argomento di avvio rapido per iniziare ad analizzare il linguaggio con l'SDK di Analisi del testo per Python. Sebbene l'API REST di Analisi del testo sia compatibile con la maggior parte dei linguaggi di programmazione, l'SDK rappresenta un modo semplice per integrare il servizio nelle applicazioni senza serializzare e deserializzare JSON. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.x](https://www.python.org/)

* [SDK per Python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) di Analisi del testo. È possibile installare il pacchetto con il comando seguente:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

È anche necessario avere la [chiave di accesso e l'endpoint](../How-tos/text-analytics-how-to-access-key.md) generati durante l'iscrizione.

## <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare una nuova applicazione Python nell'ambiente di sviluppo integrato o nell'editor preferito. Aggiungere quindi le istruzioni import seguenti al file.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Eseguire l'autenticazione delle proprie credenziali

> [!Tip]
> Per la distribuzione sicura dei segreti in sistemi di produzione, è consigliabile usare [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

Dopo aver definito una variabile per la chiave di sottoscrizione di Analisi del testo, creare un'istanza di un oggetto `CognitiveServicesCredentials` con essa.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Creare un client di Analisi del testo

Creare un nuovo oggetto `TextAnalyticsClient` con `credentials` e `text_analytics_url` come parametro. Usare l'area di Azure corretta per la sottoscrizione di Analisi del testo, ad esempio `westcentralus`.

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Analisi del sentiment

Il payload dell'API è costituito da un elenco di `documents`, che sono dizionari contenenti un `id` e un attributo `text`. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. 

```python
documents = [
  {
    "id": "1", 
    "language": "en", 
    "text": "I had the best day of my life."
  },
  {
    "id": "2", 
    "language": "en", 
    "text": "This was a waste of my time. The speaker put me to sleep."
  },  
  {
    "id": "3", 
    "language": "es", 
    "text": "No tengo dinero ni nada que dar..."
  },  
  {
    "id": "4", 
    "language": "it", 
    "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
  }
]
```

Chiamare la funzione `sentiment()` e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio del sentiment. I punteggi più vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>Output

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare un elenco di dizionari, ognuno contenente il documento da analizzare. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. 

```python
documents = [
    { 
        'id': '1', 
        'text': 'This is a document written in English.' 
    },
    {
        'id': '2', 
        'text': 'Este es un document escrito en Español.' 
    },
    { 
        'id': '3', 
        'text': '这是一个用中文写的文件' 
    }
]
``` 

Usando il client creato in precedenza, chiamare `detect_language()` e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il primo linguaggio restituito.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>Output

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Riconoscimento delle entità

Creare un elenco di dizionari, contenenti i documenti da analizzare. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. 


```python
documents = [
    {
        "id": "1",
        "language": "en", 
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es", 
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Usando il client creato in precedenza, chiamare la funzione `entities()` e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le entità in esso contenute.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Output

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare un elenco di dizionari, contenenti i documenti da analizzare. Nell'attributo `text` viene memorizzato il testo da analizzare, mentre l'`id` può essere un valore qualsiasi. 


```python
documents = [
    {
        "id": "1", 
        "language": "ja", 
        "text": "猫は幸せ"
    },
    {
        "id": "2", 
        "language": "de", 
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3", 
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4", 
        "language": "es", 
        "text": "A mi me encanta el fútbol!"
    }
]
```

Usando il client creato in precedenza, chiamare la funzione `key_phrases()` e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave in esso contenute.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>Output

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Analisi del testo con Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Vedere anche 

* [Informazioni sull'API Analisi del testo](../overview.md)
* [Esempi di scenari utente](../text-analytics-user-scenarios.md)
* [Domande frequenti](../text-analytics-resource-faq.md)
