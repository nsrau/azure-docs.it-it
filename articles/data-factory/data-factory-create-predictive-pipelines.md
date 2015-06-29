<properties 
	pageTitle="Data factory - Creare pipeline predittive tramite Data factory e Machine Learning | Azure" 
	description="Illustra come creare pipeline predittive usando Data factory di Azure e Azure Machine Learning" 
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
	ms.date="06/09/2015" 
	ms.author="spelluru"/>

# Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning 
## Panoramica
È possibile rendere operativi i modelli pubblicati di [Azure Machine Learning][azure-machine-learning] nelle pipeline di Data factory di Azure. Queste pipeline sono definite pipeline predittive. Per creare una pipeline predittiva, sono necessari gli elementi seguenti:

-	La chiave API del modello dell'area di lavoro pubblicato e l'URL per l'assegnazione dei punteggi di batch \(vedere l'immagine seguente\).
-	Un archivio BLOB di Azure che include il file CSV di input \(oppure\) un database SQL di Azure contenente i dati di input a cui assegnare un punteggio. 
-	Un archivio BLOB di Azure che conterrà il file CSV dei risultati di punteggio \(oppure\) un database SQL di Azure che conterrà i dati di output. 

	![Dashboard di Machine Learning][machine-learning-dashboard]

	L'URL per l'assegnazione dei punteggi di batch per AzureMLLinkedService viene ottenuto nel modo illustrato nella figura sopra riportata, meno '\*\*help\*\*': https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Una **pipeline predittiva** include gli elementi seguenti:

-	Tabelle di input e output
-	Servizi collegati di Archiviazione di Azure/SQL di Azure e Azure ML
-	Una pipeline con attività di assegnazione dei punteggi di batch di Azure ML

> [AZURE.NOTE]È possibile usare i parametri del servizio Web esposti da un servizio Web pubblicato di Azure Machine Learning nelle pipeline di Data factory di Azure. Per altre informazioni, vedere la sezione Parametri del servizio Web in questo articolo.

## Esempio
Questo esempio usa Archiviazione di Azure per archiviare i dati di input e di output. È anche possibile usare il database SQL di Azure invece di Archiviazione di Azure.

È consigliabile eseguire l'esercitazione [Introduzione a Data factory di Azure][adf-getstarted] prima di esaminare questo esempio e usare l'editor di Data factory per creare elementi di Data factory \(servizi collegati, tabelle, pipeline\) nell'esempio.
 

1. Creare un servizio collegato per Archiviazione di Azure Se i file di output e input di assegnazione del punteggio si trovano in account di archiviazione diversi, sarà necessario disporre di due servizi collegati. Di seguito è fornito un esempio JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Creare le tabelle di input e output di Data factory di Azure. Si noti che, a differenza di altre tabelle di Data factory, queste tabelle devono includere valori sia **folderPath** che **fileName**. È possibile usare il partizionamento per fare in modo che ogni esecuzione di batch \(ogni sezione di dati\) elabori o produca file di input e di output univoci. Potrebbe essere necessario includere alcune attività upstream per trasformare il file di input in formato CSV e collocarlo nell'account di archiviazione per ogni sezione. In tal caso, non è necessario includere le impostazioni “waitOnExternal” illustrate nell'esempio seguente e ScoringInputBlob è la tabella di output di un'attività diversa.

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
	
	Il file CSV di punteggio di batch deve avere una riga di intestazione di colonna. Se si usa l'**attività di copia** per creare/spostare il file CSV nell'archivio BLOB, è consigliabile impostare la proprietà **blobWriterAddHeader** del sink su **true**. Ad esempio:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se il file CSV non ha la riga di intestazione, potrebbe essere visualizzato un messaggio di errore analogo al seguente: **Errore nell'attività: Errore di lettura della stringa. Token imprevisto: StartObject. Percorso '', riga 1, posizione 1**.
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


4. Creare un servizio collegato di tipo **AzureMLLinkedService**, che fornisce la chiave API e un modello di URL per l'assegnazione dei punteggi di batch.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Creare infine una pipeline contenente un'attività **AzureMLBatchScoringActivity**. La pipeline otterrà il percorso del file di input dalle tabelle di input, chiamerà l'API per l'assegnazione dei punteggi di batch di Azure ML e copierà il file di output dell'assegnazione dei punteggi di batch nel BLOB specificato nella tabella di output. A differenza di altre attività di Data factory, AzureMLBatchScoringActivity può avere una sola tabella di input e una sola tabella di output.

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
È possibile usare i parametri del servizio Web esposti da un servizio Web pubblicato di Azure Machine Learning nelle pipeline di Data factory di Azure. È possibile creare un esperimento in Azure Machine Learning e pubblicarlo come servizio Web, quindi usare tale servizio Web in più pipeline o attività di Data factory di Azure, passando diversi input tramite i parametri del servizio Web.

### Passaggio di valori per i parametri del servizio Web
Aggiungere una sezione **transformation** alla sezione **AzureMLBatchScoringActivity** nel file JSON della pipeline per specificare i valori per i parametri del servizio Web in tale sezione, come illustrato nell'esempio seguente:

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


È anche possibile usare le [funzioni di Data factory](https://msdn.microsoft.com/library/dn835056.aspx) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.

### Reader e writer SQL di Azure
Uno scenario comune per l'uso dei parametri del servizio Web è costituito dai reader e dai writer SQL di Azure. Il modulo reader viene usato per caricare dati in un esperimento da servizi di gestione dati esterni ad Azure Machine Learning Studio e il modulo writer consente di salvare i dati degli esperimenti in servizi di gestione dati esterni ad Azure Machine Learning Studio. Per informazioni dettagliate sui moduli reader/writer di BLOB di Azure/SQL di Azure, vedere gli argomenti [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library. L'esempio della sezione precedente usa un reader e un writer di BLOB di Azure. Questa sezione illustra l'uso di un reader e un writer SQL di Azure.

#### Reader SQL di Azure
In Azure ML Studio è possibile creare un esperimento e pubblicare un servizio Web con un reader SQL di Azure per l'input. Il reader SQL di Azure ha proprietà di connessione che possono essere esposte come parametri del servizio Web, consentendo il passaggio dei valori per le proprietà di connessione in fase di runtime nella richiesta di punteggio di batch.

In fase di runtime, i dettagli della tabella di Data factory di input verranno usati dal servizio Data factory per completare i parametri del servizio Web. Si noti che, per consentire il funzionamento dell'integrazione con il servizio Data factory, è necessario usare i nomi predefiniti \(Database server name, Database name, Server user account name, Server user account password\) per i parametri del servizio Web.

Se sono disponibili parametri aggiuntivi del servizio Web, usare la sezione **webServiceParameters** del file JSON dell'attività. Se si specificano valori per i parametri del reader SQL di Azure in questa sezione, tali valori eseguiranno l'override dei valori recuperati dal servizio collegato SQL di Azure di input. Non è consigliabile specificare i valori per il reader SQL di Azure direttamente nella sezione webServiceParameters. Usare la sezione per passare valori per eventuali parametri aggiuntivi.

Per usare un reader SQL di Azure tramite una pipeline di Data factory di Azure, eseguire queste operazioni:

- Creare un **servizio collegato SQL di Azure**. 
- Creare una **tabella** di Data factory che usi **AzureSqlTableLocation**.
- Impostare tale **tabella** di Data factory come **input** per **AzureMLBatchScoringActivity** nel file JSON della pipeline. 



#### Writer SQL di Azure
Analogamente a un reader SQL di Azure, un writer SQL di Azure può avere proprietà esposte come parametri del servizio Web. Un writer SQL di Azure usa le impostazioni del servizio collegato associato alla tabella di input o alla tabella di output. La tabella seguente illustra le situazioni in cui viene usato il servizio collegato di input e quelle in cui viene usato il servizio collegato di output.

<table>
<tr>
<td>Output/Input</td>
<td><b>Input: SQL di Azure</b></td>
<td><b>Input: BLOB di Azure</b></td>
</tr>
<tr>
<td><b>Output: SQL di Azure</b></td>
<td><p>Il servizio Data factory usa le informazioni della stringa di connessione del servizio collegato di INPUT per generare i parametri del servizio Web con i nomi seguenti: "Database server name", "Database name", "Server user account name", "Server user account password". Si noti che è necessario usare questi nomi predefiniti per i parametri del servizio Web in Azure ML Studio.</p>
<p>Se il reader e il writer SQL di Azure nel modello di Azure ML condividono gli stessi parametri del servizio Web indicati in precedenza, non si verificheranno problemi. Se non condividono gli stessi parametri del servizio Web, ad esempio se il writer SQL di Azure usa nomi di parametri quali Database server name1, Database name1, Server user account name1 e Server user account password1 (con "1" alla fine), sarà necessario passare i valori per questi parametri del servizio Web di OUTPUT nella sezione webServiceParameters del file JSON dell'attività.</p>
<p>
È possibile passare i valori per qualsiasi altro parametro del servizio Web usando la sezione webServiceParameters del file JSON dell'attività.  
</p>

</td>
<td>
<p>Il servizio Data factory usa le informazioni della stringa di connessione del servizio collegato di OUTPUT per generare parametri del servizio Web con i nomi seguenti: "Database server name", "Database name", "Server user account name", "Server user account password". Si noti che è necessario usare questi nomi predefiniti per i parametri del servizio Web in Azure ML Studio.</p>
<p>È possibile passare i valori per qualsiasi altro parametro del servizio Web usando la sezione webServiceParameters del file JSON dell'attività. <p>Come percorso di input verrà usato il BLOB di input.</p>
</td>
</tr>
<tr>
<td><b>Output: BLOB di Azure</b></td>
<td>Il servizio Data factory usa le informazioni della stringa di connessione del servizio collegato di INPUT per generare i parametri del servizio Web con i nomi seguenti: "Database server name", "Database name", "Server user account name", "Server user account password". Si noti che è necessario usare questi nomi predefiniti per i parametri del servizio Web in Azure ML Studio.
</td>
<td>
<p>È necessario passare i valori per qualsiasi parametro del servizio Web usando la sezione WebServiceParameters del file JSON dell'attività.</p> 

<p>I BLOB verranno usati come percorsi di input e di output.</p>

</td>
<tr>

</table>

> [AZURE.NOTE]È possibile che il writer SQL di Azure incontri violazioni delle chiavi se sovrascrive una colonna Identity. È consigliabile assicurarsi di strutturare la tabella di output in modo da evitare questa situazione.
> 
> È possibile usare le tabelle di gestione temporanea con un'attività di stored procedure per unire le righe o per troncare i dati prima dell'assegnazione di punteggi. Se si usa questo approccio, configurare l'impostazione di concorrenza di executionPolicy su 1.

### Esempio di uso dei parametri del servizio Web
#### Pipeline con AzureMLBatchScoringActivity con parametri del servizio Web

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
	    	],

			"start": "2015-02-13T00:00:00Z",
        	"end": "2015-02-14T00:00:00Z"
		}
	}
 
Nell'esempio JSON precedente:

- Il modello di Azure ML usa sia il reader che il writer SQL di Azure.
- In caso di esposizione tramite il servizio Web, vengono usati i nomi predefiniti per i parametri.
	- Per il **reader**: Database server name, Database name, Server user account name e Server user account password.
	- Per il **writer**: Database server name1, Database name1, Server user account name1 e Server user account password1.
	
		Si noti che in questo caso il reader e il writer non condividono i parametri.  
- Il servizio Data factory genera automaticamente i valori per i parametri del servizio Web con i nomi **Database server name**, **Database name**, **Server user account name** e **Server user account password**, che corrispondono ai nomi del reader di input. Non è quindi necessario passare esplicitamente i valori per questi parametri tramite **webServiceParameters** nel file JSON dell'attività seguente.  
- I parametri per il writer \(con suffisso "1"\) non vengono completati automaticamente dal servizio Data factory. È quindi necessario specificare i valori per questi parametri nella sezione **webServiceParameters** del file JSON dell'attività.  
- I valori **Customer ID**, **Scored Labels** e **Scored Probabilities** vengono salvati come colonne separate da virgola. 
- Il valore **Data table name** di questo esempio corrisponde a una tabella nel database di output.
- Per la data e ora di **inizio** e **fine** è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), Ad esempio: 2014-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione verrà usato. Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "\*\*start + 48 hours\*\*". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835050.aspx).




## Vedere anche

Articolo | Descrizione
------ | ---------------
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori include informazioni complete per cmdlet, script JSON, libreria di classi .NET, funzioni e così via. 

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

 

<!---HONumber=58_postMigration-->