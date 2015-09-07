<properties 
	pageTitle="Creare pipeline predittive tramite l'attività AzureMLBatchExecution di Azure Machine Learning | Microsoft Azure"
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
	ms.date="08/24/2015"
	ms.author="spelluru"/>

# Creare pipeline predittive tramite l'attività AzureMLBatchExecution di Azure Machine Learning   
## Panoramica

> [AZURE.NOTE]Per una rapida introduzione al servizio Data factory di Azure, vedere gli articoli [Introduzione al servizio Data factory di Azure](data-factory-introduction.md) e [Creare la prima pipeline con Data factory di Azure](data-factory-build-your-first-pipeline.md).

Data factory di Azure consente di creare facilmente pipeline che si avvalgono del servizio Web [Azure Machine Learning][azure-machine-learning] per l'analisi predittiva. Con Data factory di Azure è possibile usare pipeline di Big Data, ad esempio Pig e Hive, per elaborare i dati inserirti da diverse origini dati e usare i servizi Web Azure Machine Learning per eseguire stime in batch sui dati.

È possibile usare Data factory di Azure per gestire l'elaborazione e lo spostamento dei dati e quindi effettuare un'esecuzione batch tramite Azure Machine Learning. A tale scopo, è necessario eseguire le operazioni seguenti:

1. Creare un servizio collegato di Azure Machine Learning. Sarà necessario quanto indicato di seguito:
	1. **URI della richiesta** per l'API di esecuzione del batch. È possibile trovare l'URI della richiesta facendo clic sul collegamento **ESECUZIONE BATCH** nella pagina servizi Web (come illustrato di seguito).
	1. **Chiave API** per il servizio Web Azure Machine Learning pubblicato. È possibile trovare la chiave API facendo clic sul servizio Web pubblicato. 
 2. Usare l'attività **AzureMLBatchExecution**.

	![Dashboard di Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## Scenario: Esperimenti di uso degli input/output del servizio Web che fanno riferimento ai dati presenti nell'archiviazione BLOB di Azure
Il questo scenario il servizio Web Azure Machine Learning esegue stime usando dati provenienti da un file dell'archiviazione BLOB di Azure e archivia i risultati nell'archiviazione BLOB. Lo snippet JSON seguente definisce una pipeline di Data factory di Azure con un'attività AzureMLBatchExecution. L'attività dispone del set di dati **DecisionTreeInputBlob** come input e del set di dati **DecisionTreeResultBlob** come output. Il set di dati **DecisionTreeInputBlob** viene passato come input al servizio Web mediante la proprietà JSON **webServiceInput**, mentre il set di dati **DecisionTreeResultBlob** viene passato come output per il servizio Web mediante la proprietà JSON **webServiceOutputs**. È possibile passare come input e output del servizio Web solo i set di dati che sono input e output per l'attività.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE]Possono essere passati come parametri per il servizio Web solo input e output dell'attività AzureMLBatchExecution. Nel precedente snippet JSON, ad esempio, DecisionTreeInputBlob è un input per l'attività AzureMLBatchExecution e viene passato come input al servizio Web tramite il parametro webServiceInput.

### Esempio

Questo esempio usa Archiviazione di Azure per archiviare i dati di input e di output.

È consigliabile eseguire l'esercitazione [Creare la prima pipeline con Data factory di Azure][adf-build-1st-pipeline] prima di esaminare questo esempio e usare l'editor di Data factory per creare elementi di Data factory (servizi collegati, set di dati e pipeline) nell'esempio.
 

1. Creare un **servizio collegato** per **Archiviazione di Azure**. Se i file di input e output si trovano in account di archiviazione diversi, sarà necessario disporre di due servizi collegati. Di seguito è fornito un esempio JSON:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Creare il **set di dati** di **input** di Data factory di Azure. Si noti che, a differenza di altri set di dati di Data factory, queste tabelle devono includere entrambi i valori **folderPath** e **fileName**. È possibile usare il partizionamento per fare in modo che ogni esecuzione di batch (ogni sezione di dati) elabori o produca file di input e di output univoci. Potrebbe essere necessario includere alcune attività upstream per trasformare il file di input in formato CSV e collocarlo nell'account di archiviazione per ogni sezione. In tal caso, non è necessario includere le impostazioni **external** e **externalData** illustrate nell'esempio seguente e DecisionTreeInputBlob sarà il set di dati di output di un'attività diversa.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}
	
	Il file con estensione csv di input deve avere una riga di intestazione di colonna. Se si usa l'**attività di copia** per creare/spostare il file con estensione csv nell'archivio BLOB, è consigliabile impostare la proprietà **blobWriterAddHeader** del sink su **true**. Ad esempio:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se il file con estensione csv non ha la riga di intestazione, è possibile che venga visualizzato un messaggio di errore analogo al seguente: **Errore nell'attività: Errore di lettura della stringa. Token imprevisto: StartObject. Percorso '', riga 1, posizione 1**.
3. Creare il **set di dati** di **output** di Data factory di Azure. In questo esempio il file di output usa il partizionamento per creare un percorso di output univoco per l'esecuzione di ciascuna sezione. Senza questa opzione, l'attività sovrascriverebbe il file.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Creare un **servizio collegato** di tipo **AzureMLLinkedService** che fornisce la chiave API e un modello di URL per l'esecuzione batch.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Creare infine una pipeline contenente un'attività **AzureMLBatchExecution**. La pipeline otterrà il percorso del file di input dai set di dati di input, chiamerà l'API per l'esecuzione batch di Azure Machine Learning e copierà il file di output dell'esecuzione batch nel BLOB specificato nel set di dati di output. 

	> [AZURE.NOTE]L'attività AzureMLBatchExecution può avere zero o più input e uno o più output.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	Per la data e ora di **inizio** e **fine** è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio: 2014-10-14T16:32:41Z. L'ora di **fine** è facoltativa. Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE]La specifica dell'input per l'attività AzureMLBatchExecution è opzionale.

## Scenario: Esperimenti di uso dei moduli Reader e Writer per fare riferimento ai dati in diversi archivi

Un altro scenario comune durante la creazione di esperimenti di Azure ML è quello relativo all'uso dei moduli Reader e Writer. Il modulo Reader consente di caricare i dati in un esperimento, mentre il modulo Writer consente di salvare i dati derivanti dagli esperimenti. Per informazioni dettagliate sui moduli Reader e Writer, vedere gli argomenti [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library.

Quando si usano i moduli Reader e Writer, è consigliabile usare un parametro del servizio Web per ciascuna proprietà di tali moduli. Questi parametri Web consentono di configurare i valori durante il runtime. Ad esempio, è possibile creare un esperimento con un modulo Reader che usa un database SQL di Azure: XXX.database.windows.net. Dopo aver distribuito il servizio Web, si desidera consentire ai consumer del servizio Web di specificare un altro server SQL di Azure denominato YYY.database.windows.net. È possibile usare un parametro del servizio Web per consentire la configurazione di questo valore.

> [AZURE.NOTE]L'input e l'output del servizio Web sono diversi dai parametri del servizio Web. Nel primo scenario è stato illustrato come è possibile specificare un input e un output per un servizio Web Azure ML. In questo scenario verranno passati i parametri per un servizio Web corrispondenti alle proprietà dei moduli Reader e Writer.

Viene preso in esame uno scenario relativo all'uso dei parametri del servizio Web. È stato distribuito un servizio Web Azure Machine Learning che usa un modulo Reader per leggere i dati da una delle origini dati di Azure Machine Learning supportate, ad esempio un database SQL di Azure. Dopo l'esecuzione batch, i risultati vengono scritti usando un modulo Writer (database SQL di Azure). Negli esperimenti non sono definiti input e output del servizio Web. In questo caso, si consiglia di impostare i parametri del servizio Web rilevanti per i moduli Reader e Writer. Questo consente di configurare i moduli Reader e Writer quando si usa l'attività AzureMLBatchExecution. Specificare i parametri del servizio Web nella sezione **globalParameters** dell'attività JSON come indicato di seguito.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

È anche possibile usare le [funzioni di Data factory](https://msdn.microsoft.com/library/dn835056.aspx) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \'{0:yyyy-MM-dd HH:mm:ss}\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.

### Uso di un modulo Reader per leggere dati da più file del BLOB di Azure
Le pipeline di Big Data, ad esempio Pig, Hive e così via, possono generare uno o più file di output senza estensione. Ad esempio, quando si specifica una tabella Hive esterna, i dati per tale tabella possono essere archiviati nell'archiviazione BLOB di Azure con il nome 000000\_0. È possibile usare il modulo Reader in un esperimento per la lettura di più file e usare questi ultimi per creare delle stime.

Quando si usa il modulo Reader in un esperimento di Azure Machine Learning, è possibile specificare il BLOB di Azure come input. I file nell'archiviazione BLOB di Azure possono essere file di output (ad esempio 000000\_0) generati da uno script Pig e Hive in esecuzione in HDInsight. Il modulo Reader consente di leggere i file (senza estensione) configurando la proprietà **Path to container, directory or blob** del modulo Reader in modo che punti al contenitore o alla cartella contenente i file, come mostrato di seguito. Si noti che l'asterisco (ad esempio *) **specifica che tutti i file contenitore o cartella (vale a dire dati/aggregateddata/anno = mese/2014-6 / *)** verranno letti come parte dell'esperimento.

![Proprietà del Blob Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### Esempio 
#### Pipeline con l'attività AzureMLBatchExecution con parametri del servizio Web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
Nell'esempio JSON precedente:

- Il servizio Web Azure Machine Learning distribuito usa un modulo Reader e un modulo Writer per leggere e scrivere i dati da e in un database SQL di Azure. Il servizio Web espone i quattro parametri seguenti: Database server name, Database name, Server user account name e Server user account password.  
- Per la data e ora di **inizio** e **fine** è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio: 2014-10-14T16:32:41Z. L'ora di **fine** è facoltativa. Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835050.aspx).
 

## Domande frequenti

**D:** Sto usando l'attività AzureMLBatchScoring. Dovrei passare all'attività AzureMLBatchExecution Activity?

**R:** Sì. Se si sta usando l'attività AzureMLBatchScoring per l'integrazione con Azure Machine Learning, si consiglia di passare alla più recente attività AzureMLBatchExecution. L'attività AzureMLBatchScoring è deprecata e verrà rimossa in una versione futura.

L'attività AzureMLBatchExecution è stata introdotta nella versione di Azure SDK e Azure PowerShell di agosto 2015.

L'attività AzureMLBatchExecution non richiede un input (se non sono necessarie dipendenze di input). Questo consente anche di indicare esplicitamente se si desidera usare o meno l'input e l'output del servizio Web. Se si sceglie di usare l'input e l'output del servizio Web, è possibile specificare i pertinenti set di dati del BLOB di Azure da usare. È inoltre possibile specificare chiaramente i valori dei parametri del servizio Web forniti da tale servizio.

Se si desidera continuare a usare l'attività AzureMLBatchScoring, fare riferimento all'articolo [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning](data-factory-create-predictive-pipelines.md) per informazioni dettagliate.


**D:** Dispongo di più file generati dalle pipeline di Big Data. Posso usare l'attività AzureMLBatchExecution per lavorare con tali file?

**R:** Sì. Per informazioni dettagliate, vedere la sezione **Uso di un modulo Reader per leggere dati da più file del BLOB di Azure**.








[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=August15_HO9-->