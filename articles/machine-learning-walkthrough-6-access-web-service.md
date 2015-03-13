<properties 
	pageTitle="Passaggio 6: Accedere al servizio Web di Machine Learning | Azure" 
	description="Passaggio 6 della procedura dettagliata della soluzione: Accedere a un servizio Web API di Azure Machine Learning attivo" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/06/2014" 
	ms.author="garye"/>


Questo è l'ultimo passaggio della procedura dettagliata, [Sviluppare una soluzione predittiva con Azure Machine Learning][sviluppare]:

[sviluppare]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Creare un'area di lavoro ML][create-workspace]
2.	[Caricare i dati esistenti][upload-data]
3.	[Creare un nuovo esperimento][create-new]
4.	[Eseguire il training e valutare i modelli][train-models]
5.	[Pubblicare il servizio Web][pubblicare]
6.	**Accedere al servizio Web**

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Passaggio 6: Accedere al servizio Web di Azure Machine Learning

Per l'uso come servizio Web, gli utenti devono essere in grado di inviare dati al servizio e ricevere risultati. Il servizio Web è un servizio Web di Azure che può ricevere e restituire dati in due modi:  

-	**Richiesta/risposta**: l'utente invia un singolo set di dati di credito al servizio usando un protocollo HTTP e il servizio risponde con un singolo set di risultati.
-	**Esecuzione batch**: l'utente invia al servizio l'URL di un BLOB di Azure che contiene una o più righe di dati di credito. Il servizio archivia i risultati in un altro BLOB e restituisce l'URL di tale contenitore.  

Nella scheda **DASHBOARD** del servizio Web sono presenti due collegamenti a informazioni utili agli sviluppatori per scrivere codice per l'accesso al servizio. Fare clic sul collegamento alla **pagina della guida dell'API** nella riga **RICHIESTA/RISPOSTA**. Verrà visualizzata una pagina contenente codice di esempio per l'uso del protocollo di richiesta/risposta del servizio. Analogamente, il collegamento nella riga **ESECUZIONE BATCH** fornisce l'accesso a codice di esempio per l'esecuzione di una richiesta batch al servizi.  

La pagina della guida dell'API contiene esempi per i linguaggi di programmazione R, C# e Python. Ad esempio, ecco il codice R che è possibile usare per accedere al servizio Web pubblicato (nel codice di esempio dell'utente verrà visualizzato il reale URL del servizio):  

	library("RCurl")
	library("RJSONIO")
	
	h = basicTextGatherer()
	req = list(Id="score00001",
	 Instance=list(FeatureVector=list(
	    "row.names"= "0",
	    "Status of checking account"= "0",
	    "Duration in months"= "0",
	    "Credit history"= "0",
	    "Purpose"= "0",
	    "Credit amount"= "0",
	    "Savings account/bond"= "0",
	    "Present employment since"= "0",
	    "Installment rate in percentage of disposable income"= "0",
	    "Personal status and sex"= "0",
	    "Other debtors"= "0",
	    "Present residence since"= "0",
	    "Property"= "0",
	    "Age in years"= "0",
	    "Other installment plans"= "0",
	    "Housing"= "0",
	    "Number of existing credits"= "0",
	    "Job"= "0",
	    "Number of people providing maintenance for"= "0",
	    "Telephone"= "0",
	    "Foreign worker"= "0",
	    "Credit risk"= "0"
	 ),GlobalParameters=fromJSON('{}')))
	
	body = toJSON(req)
	api_key = "abc123" # You can obtain the API key from the publisher of the web service
	
	h$reset()
	curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
	            httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
	            postfields=body,
	            writefunction = h$update,
	            verbose = TRUE
	            )
	
	result = h$value()



<!--HONumber=46--> 
