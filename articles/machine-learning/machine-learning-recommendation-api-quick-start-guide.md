<properties 
	pageTitle="Guida introduttiva per l'API Recommendations di Machine Learning | Azure" 
	description="Recommendations di Azure Machine Learning - Guida introduttiva" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="jaymathe"/>

# Guida introduttiva per l'API Recommendations di Machine Learning

Questo documento illustra come configurare il servizio o l'applicazione per l'uso di Recommendations di Azure ML. 

##Sommario

* Panoramica generale	
* Limitazioni	        
* Integrazione	        
  * Autenticazione	
  *	URI del servizio	    
  *	Versione dell'API	    
  *	Creare il modello	
  *	Importare i dati del catalogo	
  *	Importare i dati di utilizzo	
     * Caricamento del file	
	 * Uso dell'acquisizione dei dati	
* Compilare un modello di raccomandazione	
* Ottenere lo stato di compilazione	
* Ottenere raccomandazioni	
* Aggiornare il modello	
* Note legali	

 
##Panoramica generale

Per usare Recommentations di Azure ML, è necessario eseguire le operazioni seguenti:

* Creare un modello: un modello è un contenitore per dati di utilizzo, dati del catalogo e modello di raccomandazione.
* Importare i dati del catalogo: questo passaggio è facoltativo. Un catalogo contiene informazioni sui metadati negli elementi. Se non si caricano i dati del catalogo, i servizi di raccomandazione apprenderanno implicitamente informazioni sul catalogo dai dati di utilizzo.
* Importare i dati di utilizzo: i dati di utilizzo possono essere caricati in uno dei due modi possibili (o entrambi):
	* Caricando un file contenente i dati di utilizzo.
	* Inviando eventi di acquisizione dei dati.
	In genere, si carica un file dei dati di utilizzo per poter creare un modello di raccomandazione iniziale (bootstrap) e usarlo finché il sistema non raccoglie abbastanza dati usando il formato di acquisizione dei dati.
* Compilare un modello di raccomandazione: si tratta di un'operazione asincrona in cui il sistema di raccomandazione accetta tutti i dati di utilizzo e crea un modello di raccomandazione. Questa operazione può richiedere diversi minuti o diverse ore a seconda delle dimensioni dei dati e dei parametri di configurazione della build. Quando si attiva la compilazione, si ottiene un ID compilazione che si userà per verificare se il processo di compilazione è terminato prima di iniziare a usare raccomandazioni. 
* Consumo di raccomandazioni: ottenere raccomandazioni per un elemento o un elenco di elementi specifico.

Tutti i passaggi precedenti vengono eseguiti tramite l'API Recommendations di Azure ML

##Limitazioni

* Numero massimo di modelli per ogni sottoscrizione: 10
* Numero massimo di elementi che possono essere inclusi nel catalogo: 100.000
* Il numero massimo di punti di utilizzo mantenuti è ~5.000.000. I meno recenti saranno eliminati se ne vengono caricati o segnalati di nuovi.
* La dimensione massima dei dati che possono essere inviati in POST (ad esempio, importazione dei dati del catalogo o dei dati di utilizzo) è di 200 MB
* Il numero di transazioni al secondo per una build di un modello di raccomandazioni non attivo è di ~2 TPS. Solo le build di modelli di raccomandazioni attivi possono includere fino a 20 TPS

##Integrazione

###Autenticazione
Seguire le linee guida di Microsoft Azure Marketplace relative all'autenticazione. In Marketplace è supportato il metodo di autenticazione di base o OAuth.

###URI del servizio 
L'URI radice del servizio per ogni API Recommendations di Azure ML è disponibile [qui.](https://api.datamarket.azure.com/amla/recommendations/v1/)

L'URI del servizio completo è espresso usando elementi della specifica OData.  

###Versione dell'API
Ogni chiamata API terminerà con un parametro di query denominato apiVersion che dovrà essere impostato su 1.0.

###Creare il modello
Creazione di una richiesta "crea modello":

![Table1][1]

Risposta:

Codice stato HTTP: 200

XML OData
	
	feed/entry/content/properties/id - contains the model id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'</id>
	  <rights type="text" />	
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>


###Importare i dati del catalogo

Se si caricano diversi file del catalogo nello stesso modello con diverse chiamate, verranno inseriti solo i nuovi elementi del catalogo. Gli elementi esistenti manterranno i valori originali.

![Table2][2]

Risposta:

Codice stato HTTP: 200

XML OData	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###Importare i dati di utilizzo

####Caricamento del file
Queste sezioni mostrano come caricare dati di utilizzo usando un file. È possibile chiamare l'API più volte con i dati di utilizzo. Tutti i dati di utilizzo verranno salvati per tutte le chiamate.

![Table3a][3]
![Table3b][4]

Risposta:

Codice stato HTTP: 200

XML OData	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error
	Feed\entry\content\properties\FileId - the file identifier


	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####Uso dell'acquisizione dei dati
Questa sezione illustra come inviare eventi in tempo reale a Recommendations di Azure ML, in genere dal sito Web.

![Table4][5]

Corpo della richiesta

	Immissione di dati evento per ogni evento da inviare. Per lo stesso utente o la stessa sessione del browser si dovrà inviare lo stesso ID nel campo SessionId.


	Example of Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event> 


	Example of Recommendation Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RecommendationClick</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>


	Example of Adding to shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Removing from shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RemoveShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Purchase:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">   
	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>   
	<SessionId>11112222</SessionId>   
	<EventData>     
	<EventData>       
		<Name>Purchase</Name> 
		<PurchaseItems>
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>       
				<Count>3</Count>
			</PurchaseItems>
		</PurchaseItems>
	</EventData>     
	</EventData> 
	</Event>

	Example of sending 2 events "Click" and "AddShopCart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      <ItemName>itemName</ItemName>
      <ItemDescription>item description</ItemDescription>
      <ItemCategory>category</ItemCategory>
    </EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    </EventData>
  	</EventData>
	</Event>

Risposta:
Codice stato HTTP: 200

###Compilare un modello di raccomandazione

![Table5][6]

Risposta:

Codice stato HTTP: 200

	OData XML	
	This is an asynchronous API. You will get a Build Id as a response. To know when the build has ended, you should call the "Get Builds Status of a Model" API and locate this build Id in the response. Note that a build can take from minutes to hours depending on the size of the data.
	You cannot consume recommendations till the build ends.

	Valid build status:

	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Id - contains the build id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###Ottenere lo stato di compilazione di un modello
![Table6][7]

Risposta:

Codice stato HTTP: 200

	OData XML	Valid build status:
	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Status -contains the build status

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###Ottenere raccomandazioni
![Table7][8]

Codice stato HTTP: 200

	OData XML	The response includes one entry per recommended item, each entry has the following data:
	* Feed\entry\content\properties\Id - The recommended item id
	* Feed\entry\content\properties\Name - The name of the item 
	* Feed\entry\content\properties\Rating - The rating of the recommendation, higher number means higher confidence
	* Feed\entry\content\properties\Reasoning - the recommendation reasoning
	The example response below includes 10 recommended items:

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###Aggiornare il modello
È possibile aggiornare la descrizione del modello o l'ID compilazione attivo.
ID compilazione attivo: ogni compilazione per ogni modello ha un "ID compilazione". Con "ID compilazione" attivo si identifica la prima compilazione riuscita di ogni nuovo modello. Se dopo avere ottenuto un ID compilazione attivo si eseguono altre compilazioni per lo stesso modello, è consigliabile impostarlo in modo esplicito come ID compilazione predefinito. Quando si usano raccomandazioni, se non si specifica l'ID compilazione da usare, verrà usato automaticamente quello predefinito.

Dopo avere implementato un modello di raccomandazione nell'ambiente di produzione, questo meccanismo consente di compilare nuovi modelli e testarli prima di alzarli di livello e passarli in produzione.

![Table8][9]

Risposta:

Codice stato HTTP: 200

	OData XML
	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'" />
	</feed>

##Note legali
Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Web Internet, sono soggette a modifica senza preavviso. 
Alcuni esempi usati in questo documento vengono forniti a scopo puramente illustrativo e sono fittizi. Nessuna associazione reale o connessione è intenzionale o può essere desunta. 
Il presente documento non fornisce all'utente alcun diritto legale rispetto a qualsiasi proprietà intellettuale in qualsiasi prodotto Microsoft. È possibile copiare e usare il presente documento per scopi interni e di riferimento. 
(c) 2014 Microsoft. Tutti i diritti sono riservati. 


[1]: ./media/machine-learning-recommendation-api-quick-start-guide/Table01.png
[2]: ./media/machine-learning-recommendation-api-quick-start-guide/Table02.png
[3]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03a.png
[4]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03b.png
[5]: ./media/machine-learning-recommendation-api-quick-start-guide/Table04.png
[6]: ./media/machine-learning-recommendation-api-quick-start-guide/Table05.png
[7]: ./media/machine-learning-recommendation-api-quick-start-guide/Table06.png
[8]: ./media/machine-learning-recommendation-api-quick-start-guide/Table07.png
[9]: ./media/machine-learning-recommendation-api-quick-start-guide/Table08.png


<!--HONumber=46--> 

<!--HONumber=46--> 
 