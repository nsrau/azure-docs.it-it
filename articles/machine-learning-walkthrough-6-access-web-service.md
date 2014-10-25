<properties title="Step 6: Access the Azure Machine Learning web service" pageTitle="Step 6: Access the Machine Learning web service | Azure" description="Step 6: Access an active Azure Machine Learning API web service" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Questo è l'ultimo passaggio della procedura dettagliata [Sviluppare una soluzione predittiva con Azure Machine Learning][Sviluppare una soluzione predittiva con Azure Machine Learning]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Creare un'area di lavoro ML][Creare un'area di lavoro ML]
2.	[Caricare i dati esistenti][Caricare i dati esistenti]
3.	[Creare un nuovo esperimento][Creare un nuovo esperimento]
4.	[Addestrare e valutare i modelli][Addestrare e valutare i modelli]
5.	[Pubblicare il servizio Web][Pubblicare il servizio Web]
6.  **Accedere al servizio Web**

----------

# Passaggio 6: Accedere al servizio Web di Azure Machine Learning

Per l'uso come servizio Web, gli utenti devono essere in grado di inviare dati al servizio e ricevere risultati. Il servizio Web è un servizio Web di Azure che può ricevere e restituire dati in due modi:

-	**Richiesta/risposta** - L'utente invia un singolo set di dati di credito al servizio usando un protocollo HTTP e il servizio risponde con un singolo set di risultati.
-	**Esecuzione batch** - L'utente invia al servizio l'URL di un BLOB di Azure che contiene una o più righe di dati di credito. Il servizio archivia i risultati in un altro BLOB e restituisce l'URL di tale contenitore.

Nella scheda **DASHBOARD** del servizio Web sono presenti due collegamenti a informazioni utili agli sviluppatori per scrivere codice per l'accesso al servizio. Fare clic sul collegamento alla **pagina della guida dell'API** nella riga **RICHIESTA/RISPOSTA** e verrà visualizzata una pagina contenente codice di esempio per l'uso del protocollo di richiesta/risposta del servizio. Analogamente, il collegamento nella riga **ESECUZIONE BATCH** offre accesso a codice di esempio per l'esecuzione di una richiesta batch al servizi.

La pagina della guida dell'API contiene esempi per i linguaggi di programmazione R, C\#e Python. Ad esempio, ecco il codice R che è possibile usare per accedere al servizio Web pubblicato (nel codice di esempio dell'utente verrà visualizzato il reale URL del servizio):

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



  [Sviluppare una soluzione predittiva con Azure Machine Learning]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Creare un'area di lavoro ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Caricare i dati esistenti]: ../machine-learning-walkthrough-2-upload-data/
  [Creare un nuovo esperimento]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Addestrare e valutare i modelli]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Pubblicare il servizio Web]: ../machine-learning-walkthrough-5-publish-web-service/
