<properties 
	pageTitle="Data factory - Creazione di pipeline predittive tramite Data factory e Machine Learning | Azure" 
	description="Viene descritto come creare creare pipeline predittive utilizzando Azuer dati Factory e Azure Machine Learning" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# Creazione di pipeline predittive tramite Data factory di Azure e Azure Machine Learning 
## Panoramica
È possibile rendono operativi pubblicato [Azure Machine Learning][azure-machine-learning] modelli all'interno della pipeline di Factory di dati di Azure. Queste pipeline vengono chiamate pipeline predittive. Per creare una pipeline predittiva, sono necessari gli elementi seguenti:

-	La chiave API del modello dell'area di lavoro pubblicato e l'URL per l'assegnazione dei punteggi di batch (vedere l'immagine seguente)
-	Un blob di Azure che contiene il file CSV di input (o) un database SQL Azure che contiene l'input di dati di archiviazione da valutare. 
-	Archiviazione di blob di Azure che conterrà il file CSV risultati punteggio (o) un database SQL Azure che conterrà i dati di output. 

	![Machine Learning Dashboard][machine-learning-dashboard]

	Il batch di assegnazione dei punteggi URL per il AzureMLLinkedService viene ottenuto da come indicato nella figura sopra riportata, meno ' * * Guida * * ': https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Oggetto **pipeline predittiva** include i seguenti elementi:

-	Tabelle di input e output
-	SQL Azure/archiviazione di Azure e Azure ML collegato servizi
-	Una pipeline con attività di assegnazione dei punteggi di batch di Azure ML

> [AZURE.NOTE]È possibile utilizzare i parametri di servizio Web esposto da un servizio Web di Azure Machine Learning pubblicato nelle pipeline Azure dati Factory (ADF). Per ulteriori informazioni, vedere la sezione di parametri del servizio Web in questo articolo.

## Esempio
In questo esempio utilizza l'archiviazione di Azure per contenere i dati di input e outpui. È possibile utilizzare anche il Database SQL Azure invece di utilizzare l'archiviazione di Azure.

Si consiglia di eseguire il [iniziare a utilizzare Azure dati Factory][adf-getstarted] esercitazione prima di essere mediante questo esempio e utilizzare l'Editor dei dati Factory per creare elementi di dati Factory (servizi collegati, tabelle, pipeline) in questo esempio.
 

1. Creare un servizio collegato per Archiviazione di Azure Se i file di output e input di assegnazione del punteggio si trovano in account di archiviazione diversi, sarà necessario disporre di due servizi collegati. Di seguito è fornito un esempio JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Creare le tabelle di input e output di Data factory di Azure. Si noti che a differenza di altre tabelle di dati Factory, queste devono contenere entrambi **folderPath** e **fileName** valori. È possibile usare il partizionamento per fare in modo che ogni esecuzione di batch (ogni sezione di dati) elabori o produca file di input e di output univoci. Potrebbe essere necessario includere alcune attività upstream per trasformare il file di input in formato CSV e collocarlo nell'account di archiviazione per ogni sezione. In tal caso, non è necessario includere le impostazioni “waitOnExternal” illustrate nell'esempio seguente e ScoringInputBlob è la tabella di output di un'attività diversa.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}
	
	Il batch di classificazione file csv deve disporre la riga di intestazione di colonna. Se si utilizza il **attività Copia** per creare e lo spostamento csv nell'archiviazione blob, è necessario impostare la proprietà di sink **blobWriterAddHeader** a **true**. Ad esempio:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se la riga di intestazione non dispone di file csv, si può vedere il seguente errore: **errore nell'attività: errore durante la lettura di stringa. Token imprevisto: StartObject. Percorso ', riga 1, posizione 1**.
3. In questo esempio il file di output usa il partizionamento per creare un percorso di output univoco per l'esecuzione di ciascuna sezione. Senza questa opzione, l'attività sovrascriverebbe il file.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. Creare un servizio di tipo collegato: **AzureMLLinkedService**, offrendo la chiave dell'API e il modello batch URL punteggio.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Infine, creare una pipeline che contiene un **AzureMLBatchScoringActivity**. La pipeline otterrà il percorso del file di input dalle tabelle di input, chiamerà l'API per l'assegnazione dei punteggi di batch di Azure ML e copierà il file di output dell'assegnazione dei punteggi di batch nel BLOB specificato nella tabella di output. A differenza di altre attività di Data factory, AzureMLBatchScoringActivity può avere una sola tabella di input e una sola tabella di output.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}


## Parametri del servizio Web
È possibile utilizzare i parametri di servizio Web esposto da un servizio Web di Azure Machine Learning pubblicato nelle pipeline Azure dati Factory (ADF). È possibile creare un esperimento in Azure Machine Learning e pubblicare come servizio web e quindi utilizzare tale servizio web in più file ADF pipeline o attività, passando diversi input tramite i parametri del servizio Web.

### Passaggio di valori per parametri di servizio Web
Aggiungere un **trasformazione** sezione per la **AzureMLBatchScoringActivty** sezione nella pipeline di JSON per specificare valori per parametri di servizio Web in questa sezione come illustrato nell'esempio seguente:

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


È inoltre possibile utilizzare [dati Factory funzioni](https://msdn.microsoft.com/library/dn835056.aspx) nel passaggio di valori per il Web service parametri, come illustrato nell'esempio seguente:

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]I parametri del servizio Web tra maiuscole e minuscole, verificare che i nomi specificati nell'attività JSON corrispondano a quelli esposti dal servizio Web.

### Writer e visualizzatori di SQL azure
Uno scenario comune per l'utilizzo di parametri del servizio Web è l'utilizzo di Azure SQL reader e writer. Il modulo del lettore viene utilizzato per caricare dati in un esperimento da servizi di gestione dei dati all'esterno di Azure Machine Learning Studio e il modulo writer consiste nel salvare i dati di esperimenti in servizi di gestione dei dati all'esterno di Azure Machine Learning Studio. Per informazioni dettagliate su SQL Azure/Blob di Azure in lettura/scrittura, vedere [lettore](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) argomenti in MSDN Library. Il lettore di Blob di Azure e la scrittura di Blob di Azure, verrà utilizzato l'esempio nella sezione precedente. In questa sezione viene illustrato l'utilizzo di SQL Azure reader e writer SQL Azure.

#### Lettore SQL Azure
In Azure ML Studio, è possibile compilare un esperimento e pubblicare un servizio Web con un lettore di SQL Azure per l'input. Il lettore di SQL Azure dispone di proprietà di connessione che possono essere esposti come parametri del servizio Web, consentendo ai valori per le proprietà di connessione deve essere passato in fase di esecuzione del batch valutazione richiesta.

In fase di esecuzione, i dettagli della tabella dati Factory input utilizzerà il servizio dati Factory per riempire i parametri del servizio Web. Si noti che è necessario utilizzare nomi predefiniti (nome server Database, nome del Database, nome Server dell'account utente, password dell'account utente Server) per i parametri del servizio Web per l'integrazione con il servizio dati Factory per funzionare.

Se si dispone di qualsiasi Web ulteriori parametri del servizio, utilizzare il **webServiceParameters** sezione dell'attività JSON. Se si specificano valori per i parametri del lettore di SQL Azure in questa sezione, i valori sostituirà i valori prelevati dal servizio di SQL Azure collegate input. Non è consigliabile che si specificano valori per la lettura di SQL Azure direttamente nella sezione webServiceParameters. Utilizzare la sezione per passare i valori per parametri aggiuntivi.

Per utilizzare un lettore di SQL Azure tramite una pipeline di Factory di dati di Azure, procedere come segue:

- Creare un **SQL Azure collegate servizio**. 
- Creare una Factory di dati **tabella** che utilizza **AzureSqlTableLocation**.
- Impostare tale Factory dati **tabella** come il **input** per il **AzureMLBatchScoringActivity** nella pipeline di JSON. 



#### Writer SQL Azure
Come con il lettore di SQL Azure, un Writer SQL Azure può anche avere le proprietà esposte come parametri di servizio Web. Un Writer SQL Azure utilizza impostazioni dal servizio collegato associato nella tabella di input o la tabella di output. Quando l'input e output viene utilizzato il servizio collegato collegato servizio descritti nella tabella seguente.

<table>
<tr>
<td>Input/output</td>
<td><b>Input è SQL Azure</b></td>
<td><b>Input è il Blob di Azure</b></td>
</tr>
<tr>
<td><b>Output è SQL Azure</b></td>
<td><p>Il servizio dati Factory utilizza le informazioni della stringa di connessione dal servizio di INPUT collegato per generare i parametri del servizio web con nomi: "Nome server Database", "Nome Database", "Nome Server dell'account utente", "Password dell'account utente Server". Si noti che è necessario utilizzare questi nomi predefiniti per i parametri di servizio Web in Azure ML Studio.</p>
<p>Se il lettore di SQL Azure e il Writer SQL Azure nel modello ML Azure condividono gli stessi parametri del servizio Web indicati in precedenza, sono corrette. Se non condividono paramers del servizio Web stesso, ad esempio, se il Writer SQL Azure utilizza i nomi dei parametri: Database nome1 server, Database nome1, name1 account utente di Server, Server user account password1 (con "1" alla fine), è necessario passare i valori per questi parametri OUTPUT del servizio web nella sezione webServiceParameters dell'attività JSON.</p>
<p>
È possibile passare i valori per eventuali altri parametri del servizio Web utilizzando la sezione webServiceParameters dell'attività JSON.  
</p>

</td>
<td>
<p>Il servizio dati Factory utilizza le informazioni della stringa di connessione dal servizio di OUTPUT collegato per generare i parametri del servizio web con nomi: "Nome server Database", "Nome Database", "Nome Server dell'account utente", "Password dell'account utente Server". Si noti che è necessario utilizzare questi nomi predefiniti per i parametri di servizio Web in Azure ML Studio.</p>
<p>È possibile passare i valori per eventuali altri parametri del servizio Web utilizzando la sezione webServiceParameters dell'attività JSON. <p>Blob di input verrà utilizzato come percorso di input.</p>
</td>
</tr>
<tr>
<td><b>Output è il Blob di Azure</b></td>
<td>Il servizio dati Factory utilizza le informazioni della stringa di connessione dal servizio di INPUT collegato per generare i parametri del servizio web con nomi: "Nome server Database", "Nome Database", "Nome Server dell'account utente", "Password dell'account utente Server". Si noti che è necessario utilizzare questi nomi predefiniti per i parametri di servizio Web in Azure ML Studio.
</td>
<td>
<p>È necessario passare i valori per i parametri di servizio Web utilizzando la sezione WebServiceParameters dell'attività JSON.</p> 

<p>BLOB verrà utilizzato come percorsi di input e output.</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Writer SQL Azure potrebbero verificarsi violazioni di chiave, se sovrascrive una colonna identity. È necessario assicurarsi che la tabella di output per evitare questa situazione la struttura.
> 
> È possibile utilizzare tabelle di gestione temporanea con un'attività di routine archiviate righe di tipo merge o per troncare i dati prima dell'assegnazione dei punteggi. Se si utilizza questo approccio, impostare i criteri di esecuzione impostazione concurrency su 1.

### Esempio di utilizzo di parametri del servizio Web
#### Pipeline con AzureMLBatchScoringActivity con parametri di servizio Web

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	]
		}
	}
 
Nell'esempio JSON precedente:

- Il modello ML Azure utilizza Azure SQL Reader e Writer SQL Azure
- Quando viene esposta tramite servizio Web, i nomi predefiniti vengono utilizzati per i parametri
	- Per il **lettore**: nome del server, nome del Database, nome Server dell'account utente e password dell'account utente Server Database.
	- Per il **writer**: nome1 server, Database nome1, nome1 account utente di Server e password1 account utente di Server di Database.
	
		Si noti che il reader e writer non condividano parametri in questo caso.  
- Il servizio Factory dati genera automaticamente valori per parametri con i nomi del servizio Web **nome server Database**, **nome del Database**, **nome dell'account utente Server**, e **password dell'account utente Server**, che corrispondono ai nomi del lettore di input. Pertanto, non è necessario passare in modo esplicito i valori per questi parametri tramite **webServiceParameters** nell'attività JSON riportata di seguito.  
- I parametri per il writer (quelli con suffisso '1') non vengono automaticamente compilati in dal servizio dati Factory. Pertanto, è necessario specificare i valori per questi parametri nel **webServiceParameters** sezione dell'attività JSON.  
- **ID cliente**, **catalogato come etichette**, e **assegnati dei punteggi probabilità** vengono salvati come colonne separati da virgola. 
- Il **nome della tabella dati** in questo esempio corrisponde a una tabella nel database di output.




## Vedere anche

Articolo | Descrizione
------ | ---------------
[Riferimenti per sviluppatori Factory di dati di Azure][developer-reference] | Il riferimento per sviluppatori presenta il contenuto di riferimento completo per i cmdlet, script JSON, libreria di classi .NET, funzioni e così via... 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

<!---HONumber=GIT-SubDir--> 