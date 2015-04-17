<properties
	pageTitle="App Machine Learning: Servizio di analisi del testo per l'analisi della valutazione | Azure "
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
	ms.date="01/27/2015"
	ms.author="luisca"/>


# Servizio analisi testo di Machine Learning#
##Informazioni generali
API di analisi del testo è una suite di [servizi Web]( https://datamarket.azure.com/dataset/amla/text-analytics) per l'analisi del testo compilata con Azure Machine Learning. L'API può essere utilizzata per analizzare il testo non strutturato per attività quali l'analisi della valutazione ed estrazione di frasi chiave. Non sono necessari dati di training per utilizzare l'API. Bisogna solo importare i dati di testo. Al momento viene supportata solo la lingua inglese. L'API utilizza tecniche di elaborazione avanzata del linguaggio naturale dietro le quinte.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
 
## Analisi dei sentimenti##
L'API restituisce un valore numerico compreso tra 0 e 1. I valori prossimi a 1 indicano una valutazione positiva, mentre i valori prossimi a 0 indicano una valutazione negativa. I valori relativi alla valutazione vengono generati utilizzando tecniche di classificazione. Le funzionalità di input per la funzione di classificazione includono n-grams, funzionalità generate da tag parti del discorso e incorporamenti di parole.
 
## Estrazione di frasi chiave##
L'API restituisce un elenco di stringhe che indicano i punti principali di discussione nel testo di input. A tale scopo vengono impiegate tecniche del toolkit per l'elaborazione del linguaggio naturale sofisticato Microsoft Office.

## Definizione dell'API##

###GetSentiment###

**URL**	

https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Richiesta di esempio**

Nella seguente chiamata GET si richiede la valutazione della frase *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Intestazioni:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey);  

Si ottiene l'account chiave [qui]( https://datamarket.azure.com/account/keys). 

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

Nella seguente chiamata GET si richiede la valutazione delle frasi chiave nel testo *Ottimo hotel in cui alloggiare: splendidi arredi e personale amichevole*

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Intestazioni:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey)

Si ottiene l'account chiave [qui]( https://datamarket.azure.com/account/keys). 


**Risposta di esempio**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 

<!--HONumber=49-->