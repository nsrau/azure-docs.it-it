
<properties
	pageTitle="Eseguire la migrazione all'API Raccomandazioni Servizi cognitivi di Azure dall'API per le raccomandazioni DataMarket | Microsoft Azure"
	description="Raccomandazioni di Azure Machine Learning: migrazione alle raccomandazioni di Servizi Cognitivi"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="luisca"/>


# Eseguire la migrazione all'API Raccomandazioni Servizi cognitivi di Azure dall'API per le raccomandazioni DataMarket
Questo articolo illustra come eseguire la migrazione dall'[API per le raccomandazioni Microsoft DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) all'[API Raccomandazioni Servizi cognitivi di Microsoft Azure](https://www.microsoft.com/cognitive-services/it-IT/recommendations-api).

L'API per le raccomandazioni DataMarket non accetterà nuovi clienti dopo il 31 dicembre 2016 e verrà deprecata il 28 febbraio 2017.

## Come è possibile iniziare a usare l'API Raccomandazioni Servizi cognitivi?

Per eseguire la migrazione all'API Raccomandazioni Servizi cognitivi, seguire questa procedura:

1.	Se non si ha già una sottoscrizione di Azure, [iscriversi](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) per ottenerne una.

1.	Seguire le istruzioni dettagliate disponibili nella [Guida introduttiva](cognitive-services-recommendations-quick-start.md) per iscriversi all'API Raccomandazioni Servizi cognitivi e usarla a livello di codice.

1.	Per informazioni sul servizio e trovare la documentazione, visitare la [pagina di destinazione dell'API Raccomandazioni Servizi cognitivi](https://www.microsoft.com/cognitive-services/it-IT/recommendations-api).

## È stata usata l'interfaccia utente delle raccomandazioni per compilare i modelli. Esiste uno strumento simile per l'API Raccomandazioni Servizi cognitivi?

vantaggi. L'URL per la nuova [interfaccia utente delle raccomandazioni ](http://recommendations-portal.azurewebsites.net/) è http://recommendations-portal.azurewebsites.net.

>[AZURE.NOTE] Le credenziali di DataMarket non funzionano qui. Iscriversi per una sottoscrizione nel portale di Azure e ottenere la chiave dell'account necessaria per usare la nuova [interfaccia utente delle raccomandazioni](http://recommendations-portal.azurewebsites.net/). Se non è disponibile una chiave dell'account, vedere il passaggio 1 della [Guida introduttiva](cognitive-services-recommendations-quick-start.md).

##Il formato della nuova API è lo stesso dell'API per le raccomandazioni DataMarket?

L'API supporta gli stessi scenari e flussi del processo degli scenari supportati nella versione DataMarket, ma l'interfaccia API attuale è stata aggiornata per rispettare quanto definito in [Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md) (Linee guida per API REST Microsoft). Le API sono più coerenti e funzionano meglio con gli strumenti che supportano Swagger.

Per informazioni su ognuna delle API, vedere la pagina di [esplorazione dell'API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db). Usare il pulsante *Try it* (Prova) per testare una chiamata API. Il formato dei file utilizzato dall'API Raccomandazioni (file di catalogo e di utilizzo) non è cambiato, quindi è possibile continuare a usare gli stessi file e/o la stessa infrastruttura creata per generare tali file.

##Quali sono alcune nuove funzionalità dell'API Raccomandazioni Servizi cognitivi?

Negli ultimi due mesi sono state rilasciate nuove funzionalità per l'API Raccomandazioni Servizi cognitivi:
-	Interfaccia utente delle raccomandazioni per il training e il test dei modelli senza dover scrivere una singola riga di codice
-	Punteggio batch per fornire all'utente migliaia di raccomandazioni contemporaneamente
-	Supporto di metriche di compilazione per eseguire query sulla qualità dei modelli di raccomandazioni
-	Supporto delle regole di business
-	Possibilità di enumerare e scaricare i file di catalogo e di utilizzo
-	Supporto per la creazione di modelli di classificazione per eseguire query sulla qualità delle funzionalità degli elementi in un modello di raccomandazioni
-	Aggiunta di funzionalità per la ricerca di un prodotto nel catalogo

## Quando verrà interrotto il supporto dell'API per le raccomandazioni DataMarket da parte di Microsoft?

L'[API per le raccomandazioni DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) non accetterà nuovi clienti dopo il 31 dicembre 2016 e verrà completamente deprecata entro il 28 febbraio 2017.

## Cosa accade se non sono disponibili i dati necessari per ricreare i modelli nell'API Raccomandazioni Servizi cognitivi?

Per rendere questa transizione più semplice possibile, Microsoft offre assistenza agli utenti per spostare i vecchi modelli dall'account DataMarket alla nuova sottoscrizione dell'API Raccomandazioni Servizi cognitivi di Azure. A questo scopo, contattare [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Prima di associare i modelli al nuovo account, verrà chiesto di fornire l'ID sottoscrizione di DataMarket e l'ID sottoscrizione di Servizi cognitivi.

<!---HONumber=AcomDC_0907_2016-->