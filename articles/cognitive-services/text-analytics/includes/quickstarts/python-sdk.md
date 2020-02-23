---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2019
ms.author: aahi
ms.openlocfilehash: b553cd2bed68bc8df7241f4f843b32ec13b1a08a
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77372198"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

[Documentazione di riferimento v3](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b2/azure.ai.textanalytics.html) | [Codice sorgente della libreria v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Pacchetto (PiPy) v3](https://pypi.org/project/azure-ai-textanalytics/) | [Esempi v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

[Documentazione di riferimento v2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Codice sorgente della libreria v2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Pacchetto (PiPy) v2](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [Esempi v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Prerequisites

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

---

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo file Python, oltre alle variabili per l'endpoint e la chiave della sottoscrizione di Azure della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modello a oggetti

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

Il client di Analisi del testo è un oggetto `TextAnalyticsClient` che esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo in batch. 

Quando viene eseguita l'elaborazione batch, il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. 

L'oggetto risposta è un elenco contenente le informazioni di analisi per ogni documento. 

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Il client di Analisi del testo è un oggetto [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python) che esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo, come singola stringa o batch. 

Il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. 

---

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Analisi del testo per Python:

* [Autenticare il client](#authenticate-the-client)
* [Analisi del sentiment](#sentiment-analysis)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento di entità denominate](#named-entity-recognition-ner) 
* [Collegamento di entità](#entity-linking)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Autenticare il client

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

Creare una funzione per creare un'istanza dell'oggetto `TextAnalyticsClient` con le variabili `key` ed `endpoint` create in precedenza. Creare quindi un nuovo client. 

```python
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiKeyCredential

def authenticate_client():
    ta_credential = TextAnalyticsApiKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Creare una funzione per creare un'istanza dell'oggetto `TextAnalyticsClient` con le variabili `key` ed `endpoint` create in precedenza. Creare quindi un nuovo client. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Analisi del sentiment

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

Creare una nuova funzione denominata `sentiment_analysis_example()` che accetta il client come argomento e quindi chiama la funzione `analyze_sentiment()`. L'oggetto risposta restituito conterrà l'etichetta e il punteggio del sentiment dell'intero documento di input, oltre all'analisi del sentiment per ogni frase.


```python
def sentiment_analysis_example(client):

    document = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(inputs=document)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.sentiment_scores.positive,
        response.sentiment_scores.neutral,
        response.sentiment_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentiment_scores.positive,
            sentence.sentiment_scores.neutral,
            sentence.sentiment_scores.negative,
        ))

            
sentiment_analysis_example(client)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=1.000; neutral=0.000; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
Positive=1.000
Neutral=0.000
Negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.210
Neutral=0.770
Negative=0.020
```

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Autenticare un oggetto client e chiamare la funzione [sentiment()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-). Eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il punteggio del sentiment. I punteggi più vicini a 0 indicano un sentiment negativo, mentre quelli più vicini a 1 indicano un sentiment positivo.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Rilevamento della lingua

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

Creare una nuova funzione denominata `language_detection_example()` che accetta il client come argomento e quindi chiama la funzione `detect_language()`. L'oggetto risposta restituito conterrà la lingua rilevata in `primary_language`, se l'operazione riesce, oppure `error` in caso contrario.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `country_hint` per specificare un codice paese di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        document = ["Ce document est rédigé en Français."]
        response = client.detect_language(inputs = document, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Output

```console
Language:  French
```

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Usando il client creato prima, chiamare [detect_language()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e ottenere il risultato. Quindi eseguire l'iterazione attraverso i risultati e stampare l'ID di ogni documento e il primo linguaggio restituito.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Output

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Riconoscimento di entità denominate

#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

> [!NOTE]
> Nella versione `3.0-preview`:
> * Il riconoscimento di entità denominate include metodi distinti per il rilevamento di informazioni personali. 
> * Il collegamento di entità è una richiesta distinta rispetto al riconoscimento di entità denominate.

Creare una nuova funzione denominata `entity_recognition_example` che accetta il client come argomento e quindi chiama la funzione `recognize_entities()` e scorre i risultati. L'oggetto risposta restituito conterrà l'elenco di entità rilevate in `entity`, se l'operazione riesce, oppure `error` in caso contrario. Per ogni entità rilevata, stampare la categoria e la sottocategoria, se esistente.

```python
def entity_recognition_example(client):

    try:
        document = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(inputs= document)[0]

        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Output

```console
Named Entities:

    Text:    Seattle    Category:    Location   SubCategory:     GPE 
    Offset:      26     Length:      26     Confidence Score:    0.92 

    Text:    last week  Category:    DateTime   SubCategory:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="using-ner-to-detect-personal-information"></a>Uso del riconoscimento di entità denominate per rilevare informazioni personali

Creare una nuova funzione denominata `entity_pii_example()` che accetta il client come argomento e quindi chiama la funzione `recognize_pii_entities()` e ottiene il risultato. Quindi scorrere i risultati e stampare le entità.

```python
def entity_pii_example(client):

        document = ["Insurance policy for SSN on file 123-12-1234 is here by approved."]


        result = client.recognize_pii_entities(inputs= document)[0]
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tCategory: ", entity.category,"\tSubCategory: ", entity.subcategory)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(client)
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Category:  U.S. Social Security Number (SSN)    SubCategory:  None
        Offset:  33     Length:  11     Score: 0.850 
```


## <a name="entity-linking"></a>Collegamento delle entità

Creare una nuova funzione denominata `entity_linking_example()` che accetta il client come argomento e quindi chiama la funzione `recognize_linked_entities()` e scorre i risultati. L'oggetto risposta restituito conterrà l'elenco di entità rilevate in `entities`, se l'operazione riesce, oppure `error` in caso contrario. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `entity` come elenco di oggetti `match`.

```python
def entity_linking_example(client):

    try:
        document = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(inputs= document)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Output

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  125    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  179    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  168    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  98     Length: 5

```

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

> [!NOTE]
> Nella versione 2.1 il collegamento di entità è incluso nella risposta del riconoscimento di entità denominate.

Usando il client creato prima, chiamare la funzione [entities()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le entità in esso contenute.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Output

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave


#### <a name="version-30-preview"></a>[Versione 3.0-preview](#tab/version-3)

Creare una nuova funzione denominata `key_phrase_extraction_example()` che accetta il client come argomento e quindi chiama la funzione `extract_key_phrases()`. Il risultato conterrà l'elenco delle frasi chiave rilevate in `key_phrases` in caso di esito positivo ed `error` in caso contrario. Stampare tutte le frasi chiave rilevate.

```python
def key_phrase_extraction_example(client):

    try:
        document = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(inputs= document)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Output

```console
    Key Phrases:
         cat
         veterinarian
```

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

Usando il client creato prima, chiamare la funzione [key_phrases()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-python#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) e ottenere il risultato. Eseguire quindi l'iterazione attraverso i risultati e stampare l'ID di ogni documento e le frasi chiave in esso contenute.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---