---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/23/2019
ms.author: aahi
ms.openlocfilehash: b71c66c9025ea76d9f99f27537c0d4239ce93fdc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717223"
---
<a name="HOLTop"></a>

[Documentazione di riferimento](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Pacchetto (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * Questo argomento di avvio rapido la versione `3.0-preview` della libreria client di Analisi del testo, che include un'anteprima pubblica con funzionalità migliorate di [Analisi del sentiment](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e [riconoscimento di entità denominate](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Ad esempio, importare il client dallo spazio dei nomi `azure.ai.textanalytics.aio` e chiamare `analyze_sentiment()`, invece di `analyze_sentiment()` dallo spazio dei nomi `azure.ai.textanalytics`.

## <a name="prerequisites"></a>Prerequisites

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configurazione

### <a name="create-a-text-analytics-azure-resource"></a>Creare una risorsa di Azure per Analisi del testo

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo file Python, oltre alle variabili per l'endpoint e la chiave della sottoscrizione di Azure della risorsa.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Modello a oggetti

Il client di Analisi del testo è un oggetto `TextAnalyticsClient` che esegue l'autenticazione in Azure tramite la chiave. Il client fornisce diversi metodi per l'analisi del testo in batch. Questo argomento di avvio rapido usa una raccolta di funzioni per inviare rapidamente documenti singoli.

Quando viene eseguita l'elaborazione batch, il testo viene inviato all'API come elenco di `documents`, che sono oggetti `dictionary` contenenti una combinazione di attributi `id`, `text` e `language`, a seconda del metodo usato. L'attributo `text` archivia il testo da analizzare in base all'attributo `language` di origine, mentre `id` può essere un valore qualsiasi. Quando si elaborano documenti singoli, è necessario solo un input `text`, come si può vedere negli esempi seguenti.  

L'oggetto risposta è un elenco contenente le informazioni di analisi per ogni documento. 

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le attività seguenti con la libreria client di Analisi del testo per Python:

* [Analisi del sentiment](#sentiment-analysis) (anteprima pubblica)
* [Rilevamento della lingua](#language-detection)
* [Riconoscimento di entità denominate](#named-entity-recognition-public-preview) (anteprima pubblica)
* [Riconoscimento di entità denominate - informazioni personali](#named-entity-recognition---personal-information-public-preview) (anteprima pubblica)
* [Collegamento di entità](#entity-linking)
* [Estrazione delle frasi chiave](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Analisi del sentiment

> [!NOTE]
> Il codice seguente riguarda l'analisi del sentiment v3, disponibile in anteprima pubblica.

Creare una nuova funzione denominata `sentiment_analysis_example()`, che accetta l'endpoint e la chiave come argomenti e quindi chiama la funzione `single_analyze_sentiment()`. L'oggetto risposta restituito conterrà l'etichetta e il punteggio del sentiment dell'intero documento di input, oltre all'analisi del sentiment per ogni frase.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, credential=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>Rilevamento della lingua

Creare una nuova funzione denominata `language_detection_example()`, che accetta l'endpoint e la chiave come argomenti e quindi chiama la funzione `single_detect_languages()`. L'oggetto risposta restituito conterrà la lingua rilevata in `detected_languages`, se l'operazione riesce, oppure `error` in caso contrario.

> [!Tip]
> In alcuni casi potrebbe essere difficile distinguere le lingue in base all'input. È possibile usare il parametro `country_hint` per specificare un codice paese di 2 lettere. Per impostazione predefinita, l'API usa "US" come valore predefinito di countryHint. Per rimuovere questo comportamento, è possibile reimpostare questo parametro usando come valore una stringa vuota, `country_hint : ""`. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, credential=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Output

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>Riconoscimento di entità denominate (anteprima pubblica)

> [!NOTE]
> Il codice seguente riguarda il riconoscimento di entità denominate v3, disponibile in anteprima pubblica.

Creare una nuova funzione denominata `entity_recognition_example`, che accetta l'endpoint e la chiave come argomenti, quindi chiama la funzione `single_recognize_entities()` e scorre i risultati. L'oggetto risposta restituito conterrà l'elenco di entità rilevate in `entity`, se l'operazione riesce, oppure `error` in caso contrario. Per ogni entità rilevata, stamparne il tipo e il sottotipo se esiste.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, credential=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Riconoscimento di entità denominate - informazioni personali (anteprima pubblica)

> [!NOTE]
> Il codice seguente riguarda il rilevamento di informazioni personali tramite il riconoscimento di entità denominate v3, disponibile in anteprima pubblica.

Creare una nuova funzione denominata `entity_pii_example()`, che accetta l'endpoint e la chiave come argomenti, quindi chiama la funzione `single_recognize_pii_entities()` e ottiene il risultato. Quindi scorrere i risultati e stampare le entità.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, credential=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Collegamento delle entità

Creare una nuova funzione denominata `entity_linking_example()`, che accetta l'endpoint e la chiave come argomenti, quindi chiama la funzione `single_recognize_linked_entities()` e scorre i risultati. L'oggetto risposta restituito conterrà l'elenco di entità rilevate in `entities`, se l'operazione riesce, oppure `error` in caso contrario. Poiché le entità collegate vengono identificate in modo univoco, le occorrenze della stessa entità vengono raggruppate in un oggetto `entity` come elenco di oggetti `match`.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, credential=key, input_text= document)

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
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Output

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

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
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Creare una nuova funzione denominata `key_phrase_extraction_example()`, che accetta l'endpoint e la chiave come argomenti e quindi chiama la funzione `single_extract_key_phrases()`. Il risultato conterrà l'elenco delle frasi chiave rilevate in `key_phrases` in caso di esito positivo ed `error` in caso contrario. Stampare tutte le frasi chiave rilevate.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, credential=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Output

```console
    Key Phrases:
         cat
         veterinarian
```
