<properties
	pageTitle="Informazioni su come gestire i servizi Web di AzureML con Gestione API | Microsoft Azure"
	description="Guida che mostra come gestire i servizi Web di AzureML con Gestione API."
	keywords="apprendimento automatico, gestione api"
	services="machine-learning"
	documentationCenter=""
	authors="roalexan"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2016"
	ms.author="roalexan" />


# Informazioni su come gestire i servizi Web di AzureML con Gestione API

##Panoramica

Questa guida illustra le procedure per iniziare subito a usare Gestione API per gestire i servizi Web di AzureML.

##Cos'è Gestione API di Azure?

Gestione API di Azure è un servizio di Azure che consente di gestire gli endpoint dell'API REST definendo l'accesso utente, la limitazione all'utilizzo e il monitoraggio del dashboard. Per informazioni dettagliate su Gestione API di Azure, fare clic [qui](https://azure.microsoft.com/services/api-management/). Per una guida introduttiva a Gestione API di Azure, fare clic [qui](../api-management/api-management-get-started.md). L'altra guida, su cui è basata questa, tratta più argomenti, tra cui le configurazioni delle notifiche, il livello di prezzo, la gestione delle risposte, l'autenticazione utente, la creazione di prodotti, le sottoscrizioni per sviluppatori e il dashboarding dell'utilizzo.

##Informazioni su AzureML

AzureML è un servizio di Azure basato su Machine Learning che consente di compilare, distribuire e condividere facilmente soluzioni di analisi avanzate. Per informazioni dettagliate su AzureML, fare clic [qui](https://azure.microsoft.com/services/machine-learning/).

##Prerequisiti

Per completare questa guida, è necessario:

* Un account Azure. Se non si dispone di un account Azure, fare clic [qui](https://azure.microsoft.com/pricing/free-trial/) per informazioni dettagliate su come creare un account di prova gratuito.
* Un account AzureML. Se non si dispone di un account AzureML, fare clic [qui](https://studio.azureml.net/) per informazioni dettagliate su come creare un account di prova gratuito.
* L'area di lavoro, il servizio e l'api\_key per un esperimento di AzureML distribuito come servizio web. Per informazioni dettagliate su come creare un esperimento di AzureML, fare clic [qui](machine-learning-create-experiment.md). Per informazioni dettagliate su come distribuire un esperimento di AzureML come servizio Web, fare clic [qui](machine-learning-publish-a-machine-learning-web-service.md). In alternativa, l'Appendice A contiene le istruzioni per creare e testare un semplice esperimento di AzureML e distribuirlo come servizio Web.

##Creare un'istanza di Gestione API

Di seguito sono riportati i passaggi per gestire il servizio Web di AzureML con Gestione API. Creare innanzitutto un'istanza del servizio. Accedere al [portale classico](https://manage.windowsazure.com/) e fare clic su **Nuovo** > **Servizi app** > **Gestione API** > **Crea**.

![create-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Specificare un **URL** univoco. Questa guida usa **demoazureml**: sarà necessario sceglierne uno diverso. Selezionare i valori di **Sottoscrizione** e **Area** desiderati per l'istanza del servizio. Una volta effettuate le selezioni, fare clic sul pulsante Avanti.

![create-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Specificare un valore per **Nome organizzazione**. Questa guida usa **demoazureml**: sarà necessario sceglierne uno diverso. Immettere l'indirizzo di posta elettronica nel campo **Indirizzo di posta elettronica dell'amministratore**. Questo indirizzo di posta elettronica viene usato per le notifiche inviate dal sistema Gestione API.

![create-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Selezionare la casella di controllo per creare l'istanza del servizio. *La creazione di un nuovo servizio richiede fino a trenta minuti*.

##Creare l'API

Dopo aver creato l'istanza del servizio, l'operazione successiva consiste nel creare l'API. Un'API rappresenta un set di operazioni che possono essere richiamate da un'applicazione client. Le operazioni API vengono trasmesse tramite proxy ai servizi Web esistenti. Questa guida crea le API che usano un proxy per i servizi Web RRS e BES di AzureML esistenti.

Le API vengono create e configurate dal portale di pubblicazione delle API, accessibile dal portale di Azure classico. Per raggiungere il portale di pubblicazione, selezionare l'istanza del servizio.

![select-service-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Fare clic su **Gestisci** nel portale di Azure classico per il servizio Gestione API.

![manage-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Fare clic su **API** dal menu **Gestione API** sulla sinistra, quindi scegliere **Aggiungi API**.

![api-management-menu](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Digitare **AzureML Demo API** in **Nome API Web**. Digitare **https://ussouthcentral.services.azureml.net** in **URL del servizio Web**. Digitare **azureml-demo** in **Suffisso dell'URL dell'API Web**. Selezionare **HTTPS** come schema dell'**URL dell'API Web**. Selezionare **Starter** in **Prodotti**. Al termine, fare clic su **Salva** per creare l'API.

![add-new-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##Aggiungere le operazioni

Fare clic su **Aggiungi operazione** per aggiungere operazioni a questa API.

![add-operation](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

Verrà visualizzata la finestra **Nuova operazione** in cui la scheda **Firma** è visualizzata per impostazione predefinita.

##Aggiungere un'operazione RRS

Creare innanzitutto un'operazione per il servizio RRS di AzureML. Selezionare **POST** in **Verbo HTTP**. Digitare **/workspaces/{area di lavoro}/services/{servizio}/execute?api-version={versioneapi}&details={dettagli}** in **Modello di URL**. Digitare **RRS Execute** in **Nome visualizzato**.

![add-rrs-operation-signature](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare questa operazione.

![add-rrs-operation-response](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##Aggiungere operazioni BES

Non sono incluse schermate per le operazioni di BES perché sono molto simili a quelle per l'aggiunta dell'operazione RRS.

###Inviare (ma non avviare) un processo di esecuzione in batch

Fare clic su **aggiungi operazione** per aggiungere l'operazione BES di AzureML all'API. Selezionare **POST** in **Verbo HTTP**. Digitare **/workspaces/{area di lavoro}/services/{servizio}/jobs?api-version={versioneapi}** in **Modello di URL**. Digitare **BES Submit** in **Nome visualizzato**. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare questa operazione.

###Avviare un processo di esecuzione in batch

Fare clic su **aggiungi operazione** per aggiungere l'operazione BES di AzureML all'API. Selezionare **POST** in **Verbo HTTP**. Digitare **/workspaces/{area di lavoro}/services/{servizio}/jobs/{jobid}/start?api-version={versioneapi}** in **Modello di URL**. Digitare **BES Start** in **Nome visualizzato**. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare questa operazione.

###Ottenere lo stato o il risultato di un processo di esecuzione del Batch

Fare clic su **aggiungi operazione** per aggiungere l'operazione BES di Azure ML all'API. Selezionare **GET** in **Verbo HTTP**. Digitare **/workspaces/{area di lavoro}/services/{servizio}/jobs/{jobid}?api-version={versioneapi}** in **Modello di URL**. Digitare **BES Status** in **Nome visualizzato**. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare questa operazione.

###Eliminare un processo di esecuzione in batch

Fare clic su **aggiungi operazione** per aggiungere l'operazione BES di Azure ML all'API. Selezionare **DELETE** in **Verbo HTTP**. Digitare **/workspaces/{area di lavoro}/services/{servizio}/jobs/{jobid}?api-version={versioneapi}** in **Modello di URL**. Digitare **BES Delete** in **Nome visualizzato**. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**. Fare clic su **Salva** per salvare questa operazione.

##Chiamare un'operazione dal portale per sviluppatori

È possibile chiamare le operazioni direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API in tutta comodità. In questo passaggio della guida si chiamerà il metodo **RRS Execute** aggiunto all'**AzureML Demo API**. Fare clic su **Portale per sviluppatori** nel menu in alto a destra del portale classico.

![developer-portal](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Fare clic su **API** nel menu superiore e quindi su **AzureML Demo API** per visualizzare le operazioni disponibili.

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Selezionare **RRS Execute** come operazione. Fare clic su **Prova**.

![try-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Per i parametri della richiesta, digitare l'**area di lavoro**, il **servizio**, **2.0** per la **versione API** e **true** per i **dettagli**. È possibile trovare l'**area di lavoro** e il **servizio** nel dashboard del servizio Web di AzureML (vedere **Testare il servizio Web** nell'Appendice A).

Per le intestazioni della richiesta, fare clic su **Aggiungi intestazione** e digitare **Content-Type** e **application/json**, quindi fare clic su **Aggiungi intestazione** e digitare **Authorization** e **Bearer <YOUR AZUREML SERVICE API-KEY>**. È possibile trovare la **chiave API** nel dashboard del servizio Web di AzureML (vedere **Testare il servizio Web** nell'Appendice A).

Digitare **{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}** come corpo della richiesta.

![azureml-demo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Fare clic su **Send**.

![send](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Una volta richiamata un'operazione, nel portale per sviluppatori vengono visualizzati l'**URL richiesto** restituito dal servizio back-end, lo **Stato della risposta**, le **Intestazioni della risposta** e l'eventuale **Contenuto della risposta**.

![response-status](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##Appendice A - Creazione e test di un semplice servizio Web di AzureML

###Creazione di un esperimento

Di seguito sono riportati i passaggi per creare un semplice esperimento di AzureML e distribuirlo come servizio Web. Il servizio Web accetta come input una colonna di testo arbitrario e restituisce un set di funzionalità rappresentate come valori Integer. Ad esempio:

Text | Testo con hash
--- | ---
This is a good day | 1 1 2 2 0 2 0 1

Per prima cosa, usando il browser preferito, andare a [https://studio.azureml.net/](https://studio.azureml.net/) e immettere le credenziali di accesso. Quindi creare un nuovo esperimento vuoto.

![search-experiment-templates](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Rinominarlo **SimpleFeatureHashingExperiment**. Espandere **Saved Datasets** e trascinare **Book Reviews from Amazon** sull'esperimento.

![simple-feature-hashing-experiment](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Espandere **Data Transformation** e **Manipulation** e trascinare **Select Columns in Dataset** sull’esperimento. Connettere **Book Reviews from Amazon** a **Select Columns in Dataset**.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Fare clic su **Select Columns in Dataset** e quindi fare clic su **Launch column selector** e selezionare **Col2**. Fare clic sul segno di spunta per applicare queste modifiche.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Espandere **Text Analytics** e trascinare **Feature Hashing** sull'esperimento. Connettere **Select Columns in Dataset** a **Feature Hashing**.

![connect-project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Digitare **3** come **Hashing bitsize**. Verranno create 8 (23) colonne.

![hashing-bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

A questo punto, è possibile fare clic su **Run** per testare l'esperimento.

![run](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###Creare un servizio Web

Ora creare un servizio Web. Espandere **Web Service** e trascinare **Input** sull'esperimento. Connettere **Input** a **Feature Hashing**. Trascinare anche **output** sull'esperimento. Connettere **Output** a **Feature Hashing**.

![output-to-feature-hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Fare cli su **Publish web service**.

![publish-web-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Fare clic su **Yes** per pubblicare l'esperimento.

![yes-to-publish](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###Testare il servizio Web

Un servizio Web di AzureML è costituito dagli endpoint RSS (servizio di richiesta/risposta) e BES (servizio di esecuzione batch). RSS è per l'esecuzione sincrona. BES è per l'esecuzione di processi asincrona. Per testare il servizio Web con la seguente origine Python di esempio, potrebbe essere necessario scaricare e installare Azure SDK per Python (vedere: [Come installare Python](../python-how-to-install.md)).

Per la seguente origine di esempio, saranno necessari anche l'**area di lavoro**, il **servizio** e la **chiave API** dell'esperimento. È possibile trovare l'area di lavoro e il servizio facendo clic su **Request/Response** o su **Batch Execution** per l'esperimento nel dashboard del servizio Web.

![find-workspace-and-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

È possibile trovare la **chiave API** facendo clic sull'esperimento nel dashboard del servizio Web.

![find-api-key](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####Testare l'endpoint RRS

#####Pulsante Test

Un modo semplice per testare l'endpoint RRS consiste nel fare clic su **Test** nel dashboard del servizio.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Digitare **This is a good day** in **col2**. Fare clic sul segno di spunta.

![enter-data](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Verrà visualizzato qualcosa di simile a quanto segue

![sample-output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####Codice di esempio

È possibile testare RRS anche dal codice client. Se si fa clic su **Request/response** sul dashboard e si scorre fino in fondo, si vedrà il codice di esempio per C#, Python e R. Si vedrà anche la sintassi della richiesta RRS, incluso l'URI della richiesta, le intestazioni e il corpo.

Questa guida mostra un esempio di Python funzionante. Sarà necessario modificarlo con l'**area di lavoro**, il **servizio** e la **chiave API** dell'esperimento.

	import urllib2
	import json
	workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
	service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
	api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
	data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
	}
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
	headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	body = str.encode(json.dumps(data))
	try:
    	req = urllib2.Request(url, body, headers)
    	response = urllib2.urlopen(req)
    	result = response.read()
    	print "result:" + result
			except urllib2.HTTPError, error:
    	print("The request failed with status code: " + str(error.code))
    	print(error.info())
    	print(json.loads(error.read()))

####Testare l'endpoint BES
Fare clic su **Batch execution** sul dashboard e scorrere fino in fondo. Si vedrà il codice di esempio per C#, Python e R. Si vedrà anche la sintassi delle richieste BES per inviare un processo, avviare un processo, ottenere lo stato o i risultati di un processo ed eliminare un processo.

Questa guida mostra un esempio di Python funzionante. È necessario modificarlo con l'**area di lavoro**, il **servizio** e la **chiave API** dell'esperimento. Inoltre è necessario modificare il **nome account di archiviazione**, la **chiave dell'account di archiviazione** e il **nome del contenitore di archiviazione**. Infine sarà necessario modificare il percorso del **file di input** e il percorso del **file di output**.

	import urllib2
	import json
	import time
	from azure.storage import *
	workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
	service = "<REPLACE WITH YOUR SERVICE ID>"
	api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
	storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
	storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
	storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
	input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
	output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
	input_blob_name = "mydatablob.csv"
	output_blob_name = "myresultsblob.csv"
	def printHttpError(httpError):
	print("The request failed with status code: " + str(httpError.code))
	print(httpError.info())
	print(json.loads(httpError.read()))
	return
	def saveBlobToFile(blobUrl, resultsLabel):
	print("Reading the result from " + blobUrl)
	try:
		response = urllib2.urlopen(blobUrl)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	with open(output_file, "w+") as f:
		f.write(response.read())
	print(resultsLabel + " have been written to the file " + output_file)
	return
	def processResults(result):
	first = True
	results = result["Results"]
	for outputName in results:
		result_blob_location = results[outputName]
		sas_token = result_blob_location["SasBlobToken"]
		base_url = result_blob_location["BaseLocation"]
		relative_url = result_blob_location["RelativeLocation"]
		print("The results for " + outputName + " are available at the following Azure Storage location:")
		print("BaseLocation: " + base_url)
		print("RelativeLocation: " + relative_url)
		print("SasBlobToken: " + sas_token)
		if (first):
			first = False
			url3 = base_url + relative_url + sas_token
			saveBlobToFile(url3, "The results for " + outputName)
	return

	def invokeBatchExecutionService():
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
	blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
	print("Uploading the input to blob storage...")
	data_to_upload = open(input_file, "r").read()
	blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
	print "Uploaded the input to blob storage"
	input_blob_path = "/" + storage_container_name + "/" + input_blob_name
	connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
	payload =  {
		"Input": {
			"ConnectionString": connection_string,
			"RelativeLocation": input_blob_path
		},
		"Outputs": {
			"output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
		},
		"GlobalParameters": {
		}
	}
		body = str.encode(json.dumps(payload))
	headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
	print("Submitting the job...")
	# submit the job
	req = urllib2.Request(url + "?api-version=2.0", body, headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	result = response.read()
	job_id = result[1:-1] # remove the enclosing double-quotes
	print("Job ID: " + job_id)
	# start the job
	print("Starting the job...")
	req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	url2 = url + "/" + job_id + "?api-version=2.0"

	while True:
		print("Checking the job status...")
		# If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
		req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
		try:
			response = urllib2.urlopen(req)
		except urllib2.HTTPError, error:
			printHttpError(error)
			return
		result = json.loads(response.read())
		status = result["StatusCode"]
		print "status:" + status
		if (status == 0 or status == "NotStarted"):
			print("Job " + job_id + " not yet started...")
		elif (status == 1 or status == "Running"):
			print("Job " + job_id + " running...")
		elif (status == 2 or status == "Failed"):
			print("Job " + job_id + " failed!")
			print("Error details: " + result["Details"])
			break
		elif (status == 3 or status == "Cancelled"):
			print("Job " + job_id + " cancelled!")
			break
		elif (status == 4 or status == "Finished"):
			print("Job " + job_id + " finished!")
			processResults(result)
			break
		time.sleep(1) # wait one second
	return
	invokeBatchExecutionService()

<!---HONumber=AcomDC_0608_2016-->