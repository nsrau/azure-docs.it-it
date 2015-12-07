<properties
	pageTitle="API di Machine Learning: Analisi del testo | Microsoft Azure"
	description="API di analisi di testo fornite da Azure Machine Learning. L'API può essere usata per analizzare il testo non strutturato per l'analisi di valutazione, estrazione di frasi chiave e rilevamento della lingua."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="onewth"/>


# API di Machine Learning: Analisi del testo per la valutazione, estrazione di frasi chiave e rilevamento della lingua

## Panoramica

L'API di analisi del testo è un gruppo di [servizi Web](https://datamarket.azure.com/dataset/amla/text-analytics) per l'analisi del testo creata con Azure Machine Learning. L'API può essere usata per analizzare il testo non strutturato per attività quali l'analisi di valutazione, l'estrazione di frasi chiave e il rilevamento della lingua. Non sono necessari dati di training per usare questa API, è sufficiente inserire dati di testo. L'API usa tecniche di elaborazione avanzata del linguaggio naturale per fornire stime avanzate.

È possibile vedere il funzionamento dell'analisi del testo nel [sito di demo](https://text-analytics-demo.azurewebsites.net/), in cui si trovano anche [esempi](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) sull'implementazione di analisi del testo in C# e Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## Analisi dei sentimenti

L'API restituisce un valore numerico compreso tra 0 e 1. I valori prossimi a 1 indicano una valutazione positiva, mentre i valori prossimi a 0 indicano una valutazione negativa. I valori relativi alla valutazione vengono generati utilizzando tecniche di classificazione. Le funzionalità di input per la funzione di classificazione includono n-grams, funzionalità generate da tag parti del discorso e incorporamenti di parole. Attualmente l'inglese è l'unica lingua supportata.
 
## Estrazione di frasi chiave

L'API restituisce un elenco di stringhe che indicano i punti principali di discussione nel testo di input. A tale scopo vengono impiegate tecniche del toolkit per l'elaborazione del linguaggio naturale sofisticato Microsoft Office. Attualmente l'inglese è l'unica lingua supportata.

## Rilevamento della lingua

L'API restituisce la lingua rilevata e un valore punteggio numerico compreso tra 0 e 1. I punteggi prossimi a 1 indicano con una certezza del 100% che la lingua identificata è true. È supportato un totale di 120 lingue.

---

## Definizione dell'API

### Headers

Assicurarsi di includere le intestazioni corrette nella richiesta, che dovrebbe essere come la seguente:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

È possibile trovare la chiave dell'account relativa al proprio account in [Azure Marketplace](https://datamarket.azure.com/account/keys).

---

## API con risposta singola

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Richiesta di esempio**

Nella chiamata seguente viene richiesta l'analisi di valutazione per la frase "Hello World":

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Restituirà una risposta come la seguente:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

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
 
---

### GetLanguage

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

---

## API di batch

Il servizio di Analisi del testo consente di eseguire estrazioni di sentiment e frase chiave in modalità batch. Si noti che ogni record con punteggio conta come un'unica transazione. Quindi, ad esempio, se si richiede una valutazione per 1000 record in una singola chiamata, verranno dedotte 1000 transazioni.

Gli ID immessi nel sistema corrispondono agli ID restituiti dal sistema. Il servizio Web non verifica che gli ID siano univoci. È responsabilità del chiamante verificarne l'univocità.


### GetSentimentBatch

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
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

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

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

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

<!---HONumber=AcomDC_1125_2015-->