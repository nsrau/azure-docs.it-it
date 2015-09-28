<properties
	pageTitle="App di Machine Learning: analisi del sentimento | Microsoft Azure"
	description="API di analisi del testo è una suite di analisi di testo compilata con Azure Machine Learning. L'API può essere utilizzata per analizzare il testo non strutturato per attività quali l'analisi della valutazione ed estrazione di frasi chiave."
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/11/2015"
	ms.author="luisca"/>


# App di Machine Learning: servizio di analisi del testo per l'analisi del sentimento#
##Panoramica
L'API di analisi del testo è una suite di [servizi Web](https://datamarket.azure.com/dataset/amla/text-analytics) per l'analisi del testo creata con Azure Machine Learning. L'API può essere utilizzata per analizzare il testo non strutturato per attività quali l'analisi della valutazione ed estrazione di frasi chiave. Non sono necessari dati di training per usare questa API, è sufficiente inserire i propri dati. Al momento viene supportata solo la lingua inglese. L'API utilizza tecniche di elaborazione avanzata del linguaggio naturale dietro le quinte.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## Analisi dei sentimenti##
L'API restituisce un valore numerico compreso tra 0 e 1. I valori prossimi a 1 indicano una valutazione positiva, mentre i valori prossimi a 0 indicano una valutazione negativa. I valori relativi alla valutazione vengono generati utilizzando tecniche di classificazione. Le funzionalità di input per la funzione di classificazione includono n-grams, funzionalità generate da tag parti del discorso e incorporamenti di parole.
 
## Estrazione di frasi chiave##
L'API restituisce un elenco di stringhe che indicano i punti principali di discussione nel testo di input. A tale scopo vengono impiegate tecniche del toolkit per l'elaborazione del linguaggio naturale sofisticato Microsoft Office.

## Definizione dell'API##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Richiesta di esempio**

Nella chiamata GET di seguito viene richiesta la valutazione della frase *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Intestazioni:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

La chiave dell'account viene ottenuta [qui](https://datamarket.azure.com/account/keys).

**Risposta di esempio**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Richiesta di esempio**

Nella chiamata GET di seguito viene richiesta la valutazione delle frasi chiave nel testo *It was a wonderful hotel to stay at, with unique decor and friendly staff*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Intestazioni:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

La chiave dell'account viene ottenuta [qui](https://datamarket.azure.com/account/keys).


**Risposta di esempio**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Richiesta di esempio**

Nella chiamata POST di seguito vengono richieste le valutazioni delle seguenti frasi nel corpo della richiesta: Hello World, Hello Foo World, Hello My World

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Corpo:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


Intestazioni:

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


La chiave dell'account viene ottenuta [qui](https://datamarket.azure.com/account/keys).

**Risposta di esempio**

Nella risposta di seguito viene ottenuto l'elenco dei punteggi associati agli ID testo:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[
		   {"Id": "4", Message:"Record cannot be null/empty"}
		]
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Richiesta di esempio**

Nella chiamata POST di seguito viene richiesto l'elenco delle valutazioni delle frasi chiave nei testi seguenti:

*It was a wonderful hotel to stay at, with unique decor and friendly staff*
 
*It was an amazing build conference, with very interesting talks*

*The traffic was terrible, I spent three hours going to the airport*



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Corpo:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

Intestazioni:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

La chiave dell'account viene ottenuta [qui](https://datamarket.azure.com/account/keys).


**Risposta di esempio**

Nella risposta di seguito viene ottenuto l'elenco delle frasi chiave associate agli ID testo:

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[
		   {"Id": "4", Message:"Record cannot be null/empty"}
		]
	}

---

**Note correlate all'elaborazione batch**

Gli ID immessi nel sistema corrispondono agli ID restituiti dal sistema. Il servizio Web non verifica che gli ID siano univoci. È responsabilità del chiamante verificarne l'univocità.
 

<!---HONumber=Sept15_HO3-->