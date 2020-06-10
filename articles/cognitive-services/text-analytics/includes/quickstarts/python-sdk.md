---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: aahi
ms.openlocfilehash: b842084d00c1ce8ec347994371a55c97b89ba54f
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140728"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

[Documentazione di riferimento v3](https://aka.ms/azsdk-python-textanalytics-ref-docs) | [Codice sorgente della libreria v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Pacchetto (PiPy) v3](https://pypi.org/project/azure-ai-textanalytics/) | [Esempi v3](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

[Documentazione di riferimento v2](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Codice sorgente della libreria v2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Pacchetto (PiPy) v2](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [Esempi v2](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* Dopo aver creato la sottoscrizione di Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="creare una risorsa di Analisi del testo"  target="_blank">creare una risorsa di Analisi del testo <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione all'API Analisi del testo. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

```console
pip install azure-ai-textanalytics
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), che contiene gli esempi di codice di questo argomento. 

#### <a name="version-21"></a>[Versione 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), che contiene gli esempi di codice di questo argomento. 

---

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo file Python, oltre alle variabili per l'endpoint e la chiave della sottoscrizione di Azure della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modello a oggetti

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

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

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una funzione per creare un'istanza dell'oggetto `TextAnalyticsClient` con le variabili `key` ed `endpoint` create in precedenza. Creare quindi un nuovo client. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
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

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una nuova funzione denominata `sentiment_analysis_example()` che accetta il client come argomento e quindi chiama la funzione `analyze_sentiment()`. L'oggetto risposta restituito conterrà l'etichetta e il punteggio del sentiment dell'intero documento di input, oltre all'analisi del sentiment per ogni frase.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
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

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una nuova funzione denominata `language_detection_example()` che accetta il client come argomento e quindi chiama la funzione `detect_language()`. L'oggetto risposta restituito conterrà la lingua rilevata in `primary_language`, se l'operazione riesce, oppure `error` in caso contrario.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `country_hint` per specificare un codice paese di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
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

#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

> [!NOTE]
> Nella versione `3.0`: 
> * Il collegamento di entità è una richiesta distinta rispetto al riconoscimento di entità denominate.

Creare una nuova funzione denominata `entity_recognition_example` che accetta il client come argomento e quindi chiama la funzione `recognize_entities()` e scorre i risultati. L'oggetto risposta restituito conterrà l'elenco di entità rilevate in `entity`, se l'operazione riesce, oppure `error` in caso contrario. Per ogni entità rilevata, stampare la categoria e la sottocategoria, se esistente.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Output

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

## <a name="entity-linking"></a>Collegamento delle entità

Creare una nuova funzione denominata `entity_linking_example()` che accetta il client come argomento e quindi chiama la funzione `recognize_linked_entities()` e scorre i risultati. L'oggetto risposta restituito conterrà l'elenco di entità rilevate in `entities`, se l'operazione riesce, oppure `error` in caso contrario. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `entity` come elenco di oggetti `match`.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Output

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
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


#### <a name="version-30"></a>[Versione 3.0](#tab/version-3)

Creare una nuova funzione denominata `key_phrase_extraction_example()` che accetta il client come argomento e quindi chiama la funzione `extract_key_phrases()`. Il risultato conterrà l'elenco delle frasi chiave rilevate in `key_phrases` in caso di esito positivo ed `error` in caso contrario. Stampare tutte le frasi chiave rilevate.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

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
