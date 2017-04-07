---
title: 'API di Machine Learning: Analisi del testo | Documentazione Microsoft'
description: "Le API di Machine Learning Microsoft per l&quot;analisi del testo possono essere usate per analizzare testo non strutturato per attività quali l&quot;analisi del sentiment, l&quot;estrazione di frasi chiave, il rilevamento della lingua e il rilevamento di argomenti."
services: machine-learning
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: 5b60694e-5521-4e4d-bf6a-1a92fdf94b65
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: onewth
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: ../cognitive-services/cognitive-services-text-analytics-quick-start
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f4389705a81b531bd706cbabc0b4c3b171febd5f
ms.lasthandoff: 11/17/2016


---
# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>API di Machine Learning: Analisi del testo per analisi del sentiment, estrazione di frasi chiave, rilevamento della lingua e rilevamento di argomenti
> [!NOTE]
> Questa guida riguarda la versione 1 dell'API. Per la versione 2, [**fare riferimento a questo documento**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). La versione 2 è ora la versione preferita di questa API.
> 
> 

## <a name="overview"></a>Overview
L'API di analisi del testo è un gruppo di [servizi Web](https://datamarket.azure.com/dataset/amla/text-analytics) per l'analisi del testo creata con Azure Machine Learning. L'API può essere usata per analizzare il testo non strutturato per attività quali l'analisi del sentiment, l'estrazione di frasi chiave, il rilevamento della lingua e il rilevamento di argomenti. Non sono necessari dati di training per usare questa API, è sufficiente inserire dati di testo. L'API usa tecniche di elaborazione avanzata del linguaggio naturale per fornire stime avanzate.

È possibile vedere il funzionamento dell'analisi del testo nel [sito di demo](https://text-analytics-demo.azurewebsites.net/), in cui si trovano anche [esempi](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) dell'implementazione di analisi del testo in C# e Python.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

- - -
## <a name="sentiment-analysis"></a>Analisi del sentiment
L'API restituisce un valore numerico compreso tra 0 e 1. I valori prossimi a 1 indicano una valutazione positiva, mentre i valori prossimi a 0 indicano una valutazione negativa. I valori relativi alla valutazione vengono generati utilizzando tecniche di classificazione. Le funzionalità di input per la funzione di classificazione includono n-grams, funzionalità generate da tag parti del discorso e incorporamenti di parole. Attualmente l'inglese è l'unica lingua supportata.

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave
L'API restituisce un elenco di stringhe che indicano i punti principali di discussione nel testo di input. A tale scopo vengono impiegate tecniche del toolkit per l'elaborazione del linguaggio naturale sofisticato Microsoft Office. Attualmente l'inglese è l'unica lingua supportata.

## <a name="language-detection"></a>Rilevamento della lingua
L'API restituisce la lingua rilevata e un valore punteggio numerico compreso tra 0 e 1. I punteggi prossimi a 1 indicano con una certezza del 100% che la lingua identificata è true. È supportato un totale di 120 lingue.

## <a name="topic-detection"></a>Rilevamento di argomenti
Si tratta di un'API rilasciata di recente che restituisce i primi argomenti rilevati a fronte di un elenco di record di testo inviati. Un argomento viene identificato da una frase chiave, che può essere costituita da una o più parole correlate. Questa API richiede un minimo di 100 record di testo da inviare, ma è progettata per rilevare gli argomenti in centinaia o addirittura migliaia di record. Si noti che con questa API viene addebitata una transazione per ogni record di testo inviato. L'API è progettata per funzionare al meglio con testi brevi in linguaggio naturale, ad esempio recensioni e commenti degli utenti.

- - -
## <a name="api-definition"></a>Definizione dell'API
### <a name="headers"></a>Headers
Assicurarsi di includere le intestazioni corrette nella richiesta, che dovrebbe essere come la seguente:

    Authorization: Basic <creds>
    Accept: application/json

    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

È possibile trovare la chiave dell'account relativa al proprio account in [Azure Marketplace](https://datamarket.azure.com/account/keys). Si noti che per i formati di input e output è attualmente accettato solo JSON. XML non è supportato.

- - -
## <a name="single-response-apis"></a>API con risposta singola
### <a name="getsentiment"></a>GetSentiment
**URL**    

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Richiesta di esempio**

Nella chiamata seguente viene richiesta l'analisi del sentiment per la frase "Hello World":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Restituirà una risposta come la seguente:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

- - -
### <a name="getkeyphrases"></a>GetKeyPhrases
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Richiesta di esempio**

Nella chiamata seguente vengono richieste le frasi chiave trovate nel testo "It was a wonderful hotel to stay at, with unique decor and friendly staff":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Restituirà una risposta come la seguente:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }

- - -
### <a name="getlanguage"></a>GetLanguage
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Richiesta di esempio**

Nella chiamata GET seguente viene richiesta la valutazione per le frasi chiave nel testo *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Restituirà una risposta come la seguente:

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Parametri facoltativi**

`NumberOfLanguagesToDetect` è un parametro facoltativo. Il valore predefinito è 1.

- - -
## <a name="batch-apis"></a>API di batch
Il servizio di Analisi del testo consente di eseguire estrazioni di sentiment e frase chiave in modalità batch. Si noti che ogni record con punteggio conta come un'unica transazione. Quindi, ad esempio, se si richiede una valutazione per 1000 record in una singola chiamata, verranno dedotte 1000 transazioni.

Gli ID immessi nel sistema corrispondono agli ID restituiti dal sistema. Il servizio Web non verifica che gli ID siano univoci. È responsabilità del chiamante verificarne l'univocità. 

### <a name="getsentimentbatch"></a>GetSentimentBatch
**URL**    

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Richiesta di esempio**

Nella chiamata POST seguente vengono richieste le valutazioni delle frasi "Hello World", "Hello Foo World" e "Hello My World" nel corpo della richiesta:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corpo della richiesta:

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

Nella risposta di seguito viene ottenuto l'elenco dei punteggi associati agli ID testo:

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


- - -
### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Richiesta di esempio**

In questo esempio viene richiesto l'elenco delle valutazioni delle frasi chiave nei testi seguenti: 

* "It was a wonderful hotel to stay at, with unique decor and friendly staff"
* "It was an amazing build conference, with very interesting talks"
* "The traffic was terrible, I spent three hours going to the airport"

Questa richiesta viene effettuata come una chiamata POST all'endpoint:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corpo della richiesta:

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

Nella risposta di seguito viene ottenuto l'elenco delle frasi chiave associate agli ID testo:

    { "odata.metadata":"<url>",
         "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

- - -
### <a name="getlanguagebatch"></a>GetLanguageBatch

Nella chiamata POST seguente si richiede il rilevamento delle lingua per due input di testo:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Corpo della richiesta:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

Restituisce la risposta seguente, in cui inglese viene rilevato nel primo parametro di input e francese nel secondo input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

- - -
## <a name="topic-detection-apis"></a>API per il rilevamento di argomenti
Si tratta di un'API rilasciata di recente che restituisce i primi argomenti rilevati a fronte di un elenco di record di testo inviati. Un argomento viene identificato da una frase chiave, che può essere costituita da una o più parole correlate. Si noti che con questa API viene addebitata una transazione per ogni record di testo inviato.

Questa API richiede un minimo di 100 record di testo da inviare, ma è progettata per rilevare gli argomenti in centinaia o addirittura migliaia di record.

### <a name="topics--submit-job"></a>Argomenti: processo di invio
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Richiesta di esempio**

Nella chiamata POST riportata di seguito vengono richiesti gli argomenti per un insieme di 100 articoli. Vengono visualizzati il primo e l'ultimo articolo di input e sono incluse due frasi di stop.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Corpo della richiesta:

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

Nella risposta seguente si ottiene l'ID processo (JobId) per il processo inviato:

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

Un elenco di parole singole o di frasi costituite da più parole che non dovrebbero essere restituite come argomenti. Può essere usato per filtrare argomenti molto generici. Ad esempio, in un set di dati riguardante recensioni di alberghi, "albergo" e "ostello" possono esser frasi di stop sensibili.  

### <a name="topics--poll-for-job-results"></a>Argomenti: polling risultati processo
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Richiesta di esempio**

Passare l'ID processo (JobId) restituito al passaggio relativo al processo di invio per recuperare i risultati. È consigliabile chiamare questo endpoint ogni minuto, fino a quando nella risposta viene visualizzato Status='Complete'. Per completare un processo sono necessari circa 10 minuti. Nel caso di processi con diverse migliaia di record, può essere necessario un periodo di tempo maggiore.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Durante l'elaborazione, la risposta avrà un aspetto simile al seguente:

    {
        "odata.metadata":"<url>",
        "Status":"Running",
         "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


L'API restituisce un output in formato JSON, come mostrato di seguito:

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
          ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


Le proprietà di ciascuna parte della risposta sono le seguenti:

**Proprietà TopicInfo**

| Chiave | Descrizione |
|:--- |:--- |
| TopicId |Identificatore univoco di ogni argomento. |
| Score |Numero di record assegnati all'argomento. |
| KeyPhrase |Parola o frase di riepilogo dell'argomento. Può essere costituita da una o più parole. |

**Proprietà TopicAssignment**

| Chiave | Descrizione |
|:--- |:--- |
| ID |Identificatore del record. Equivale all'ID incluso nell'input. |
| TopicId |ID dell'argomento a cui sono stati assegnati i record. |
| Distance |Probabilità che il record appartenga all'argomento. Un valore vicino a zero indica una probabilità elevata. |


