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
	ms.date="02/06/2016" 
	ms.author="spelluru"/>

# Creare pipeline predittive con le attività di Azure Machine Learning   
## Panoramica

> [AZURE.NOTE] Per una rapida introduzione al servizio Data factory di Azure, vedere gli articoli [Introduzione al servizio Data factory di Azure](data-factory-introduction.md) e [Creare la prima pipeline con Data factory di Azure](data-factory-build-your-first-pipeline.md).

## Introduzione

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) consente di compilare, testare e distribuire soluzioni di analisi predittiva. Da un punto di vista generale, questo avviene in tre passaggi:

1. **Creare un esperimento di training**. Questa operazione si esegue con Azure ML Studio un ambiente di sviluppo visivo di collaborazione usato per eseguire il training e il test di un modello di analisi predittiva con i dati di training forniti.
2. **Convertirlo in un esperimento predittivo**. Dopo aver eseguito il training del modello con i dati esistenti, preparare e semplificare l'esperimento di assegnazione dei punteggi quando si è pronti a usarlo per valutare nuovi dati.
3. **Distribuirlo come servizio Web**. È possibile pubblicare l'esperimento di assegnazione dei punteggi come servizio Web di Azure. Gli utenti possono inviare dati al modello tramite l'endpoint di questo servizio Web e ricevere le stime dei risultati dal modello.  

Data factory di Azure consente di creare facilmente pipeline che sfruttano un servizio Web di [Azure Machine Learning][azure-machine-learning] pubblicato per l'analisi predittiva. Con **Attività di esecuzione batch** in una pipeline di Data factory di Azure è possibile richiamare un servizio Web di Azure ML per eseguire stime dei dati in batch. Per altre informazioni, vedere la sezione [Richiamo di un servizio Web di Azure ML tramite Attività di esecuzione batch](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity).

Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Azure ML usando nuovi set di dati di input. È possibile ripetere il training di un modello di Azure ML da una pipeline di Data factory seguendo questa procedura:

1. Pubblicare l'esperimento di training, non l'esperimento predittivo, come servizio Web. Eseguire questa operazione in Azure ML Studio come si è fatto per esporre l'esperimento predittivo come servizio Web nello scenario precedente.
2. Usare Attività di esecuzione batch di Azure ML per richiamare il servizio Web per l'esperimento di training. In sostanza, è possibile usare Attività di esecuzione batch di Azure ML per richiamare sia il servizio Web di training che il servizio Web di assegnazione dei punteggi. 
  
Una volta ripetuto il training, aggiornare il servizio Web di assegnazione dei punteggi, cioè l'esperimento predittivo esposto come servizio Web, con il nuovo modello sottoposto a training. A questo scopo, seguire questa procedura:

1. Aggiungere un endpoint non predefinito al servizio Web di assegnazione dei punteggi. L'endpoint predefinito del servizio Web non può esser aggiornato, quindi sarà necessario creare un nuovo endpoint non predefinito tramite il portale di Azure classico. Per informazioni concettuali e passaggi procedurali, vedere l'articolo [Creare endpoint](../machine-learning/machine-learning-create-endpoint.md).
2. Aggiornare i servizi collegati di Azure ML per l'assegnazione dei punteggi esistenti perché usino l'endpoint non predefinito. È consigliabile iniziare con il nuovo endpoint per usare il servizio Web aggiornato.
3. Usare **Attività della risorsa di aggiornamento di Azure ML** per aggiornare il servizio Web con il nuovo modello sottoposto a training.  

Per altre informazioni, vedere la sezione [Aggiornamento dei modelli di Azure ML con Attività della risorsa di aggiornamento](#updating-azure-ml-models-using-the-update-resource-activity).

## Richiamo di un servizio Web di Azure ML tramite Attività di esecuzione batch

È possibile usare Data factory di Azure per gestire l'elaborazione e lo spostamento dei dati e quindi effettuare un'esecuzione batch tramite Azure Machine Learning. A tale scopo, è necessario eseguire le operazioni seguenti:

1. Creare un servizio collegato di Azure Machine Learning. Sarà necessario quanto indicato di seguito:
	1. **URI della richiesta** per l’API di esecuzione batch. È possibile trovare l'URI della richiesta facendo clic sul collegamento **ESECUZIONE BATCH** nella pagina servizi Web (come illustrato di seguito).
	1. **API key** per il servizio Web di Azure Machine Learning pubblicato. È possibile trovare la chiave API facendo clic sul servizio Web pubblicato. 
 2. Usare l'attività **AzureMLBatchExecution**.

	![Dashboard di Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![Batch URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### Scenario: Esperimenti di uso degli input/output del servizio Web che fanno riferimento ai dati presenti nell'archiviazione BLOB di Azure
Il questo scenario il servizio Web Azure Machine Learning esegue stime usando dati provenienti da un file dell'archiviazione BLOB di Azure e archivia i risultati nell'archiviazione BLOB. Lo snippet JSON seguente definisce una pipeline di Data factory di Azure con un'attività AzureMLBatchExecution. L'attività include il set di dati **DecisionTreeInputBlob** come input e il set di dati **DecisionTreeResultBlob** come output. Il set di dati **DecisionTreeInputBlob** viene passato come input al servizio Web tramite la proprietà JSON **webServiceInput**, mentre il set di dati **DecisionTreeResultBlob** viene passato come output al servizio Web tramite la proprietà JSON **webServiceOutputs**.

> [AZURE.NOTE] I set di dati a cui fanno riferimento le proprietà **webServiceInput** e **webServiceOutputs** (in **typeProperties**) devono essere inclusi anche negli **input** e **output** dell'attività.


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

> [AZURE.NOTE] Possono essere passati come parametri per il servizio Web solo input e output dell'attività AzureMLBatchExecution. Nel precedente snippet JSON, ad esempio, DecisionTreeInputBlob è un input per l'attività AzureMLBatchExecution e viene passato come input al servizio Web tramite il parametro webServiceInput.

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
	
	Il file con estensione csv di input deve avere una riga di intestazione di colonna. Se si usa l'**attività di copia** per creare/spostare il file CSV nell'archivio BLOB, è consigliabile impostare la proprietà **blobWriterAddHeader** del sink su **true**. Ad esempio:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se il file CSV non ha la riga di intestazione, potrebbe essere visualizzato un messaggio di errore analogo al seguente: **Errore nell'attività: Errore di lettura della stringa. Token imprevisto: StartObject. Percorso '', riga 1, posizione 1**.
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

	> [AZURE.NOTE] L'attività AzureMLBatchExecution può avere zero o più input e uno o più output.

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

	Per la data e ora di **inizio** e **fine** è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio: 2014-10-14T16:32:41Z. L'ora di fine, **end**, è facoltativa. Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 ore**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE] La specifica dell'input per l'attività AzureMLBatchExecution è opzionale.

### Scenario: Esperimenti di uso dei moduli Reader e Writer per fare riferimento ai dati in diversi archivi

Un altro scenario comune durante la creazione di esperimenti di Azure ML è quello relativo all'uso dei moduli Reader e Writer. Il modulo Reader consente di caricare i dati in un esperimento, mentre il modulo Writer consente di salvare i dati derivanti dagli esperimenti. Per informazioni dettagliate sui moduli Reader e Writer, vedere gli argomenti [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library.

Quando si usano i moduli Reader e Writer, è consigliabile usare un parametro del servizio Web per ciascuna proprietà di tali moduli. Questi parametri Web consentono di configurare i valori durante il runtime. Ad esempio, è possibile creare un esperimento con un modulo Reader che usa un database SQL di Azure: XXX.database.windows.net. Dopo aver distribuito il servizio Web, si desidera consentire ai consumer del servizio Web di specificare un altro server SQL di Azure denominato YYY.database.windows.net. È possibile usare un parametro del servizio Web per consentire la configurazione di questo valore.

> [AZURE.NOTE] L'input e l'output del servizio Web sono diversi dai parametri del servizio Web. Nel primo scenario è stato illustrato come è possibile specificare un input e un output per un servizio Web Azure ML. In questo scenario verranno passati i parametri per un servizio Web corrispondenti alle proprietà dei moduli Reader e Writer.

Viene preso in esame uno scenario relativo all'uso dei parametri del servizio Web. È stato distribuito un servizio Web Azure Machine Learning che usa un modulo Reader per leggere i dati da una delle origini dati di Azure Machine Learning supportate, ad esempio un database SQL di Azure. Dopo l'esecuzione batch, i risultati vengono scritti usando un modulo Writer (database SQL di Azure). Negli esperimenti non sono definiti input e output del servizio Web. In questo caso, si consiglia di impostare i parametri del servizio Web rilevanti per i moduli Reader e Writer. Questo consente di configurare i moduli Reader e Writer quando si usa l'attività AzureMLBatchExecution. Specificare i parametri del servizio Web nella sezione **globalParameters** del codice JSON dell'attività come indicato di seguito.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

È anche possibile usare le [funzioni di Data factory](https://msdn.microsoft.com/library/dn835056.aspx) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.

### Uso di un modulo Reader per leggere dati da più file del BLOB di Azure
Le pipeline di Big Data, ad esempio Pig, Hive e così via, possono generare uno o più file di output senza estensione. Ad esempio, quando si specifica una tabella Hive esterna, i dati per tale tabella possono essere archiviati nell'archiviazione BLOB di Azure con il nome 000000\_0. È possibile usare il modulo Reader in un esperimento per la lettura di più file e usare questi ultimi per creare delle stime.

Quando si usa il modulo Reader in un esperimento di Azure Machine Learning, è possibile specificare il BLOB di Azure come input. I file nell'archiviazione BLOB di Azure possono essere file di output (ad esempio 000000\_0) generati da uno script Pig e Hive in esecuzione in HDInsight. Il modulo Reader consente di leggere i file, senza estensione, configurando la proprietà **Path to container, directory or blob** del modulo Reader in modo che punti al contenitore o alla cartella contenente i file, come mostrato di seguito. Si noti che l'asterisco, ad esempio *, **specifica che tutti i file nel contenitore o nella cartella, vale a dire data/aggregateddata/year=2014/month-6/***, verranno letti come parte dell'esperimento.

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
- Per la data e ora di **inizio** e **fine** è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio: 2014-10-14T16:32:41Z. L'ora di fine, **end**, è facoltativa. Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 ore**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**. Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835050.aspx).

### Altri scenari

#### Servizio Web non richiede un input

I servizi Web per l'esecuzione batch di Azure ML possono essere usati per eseguire flussi di lavoro, ad esempio script R o Python, che possono non richiedere alcun input. In alternativa, l'esperimento può essere configurato con un modulo Reader che non espone proprietà GlobalParameters. In tal caso l'attività AzureMLBatchExecution verrà configurata come segue:

	{
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### Servizio Web non richiede un input/output
Il servizio Web per l'esecuzione batch di Azure ML può non disporre di alcun output del servizio Web configurato. In questo esempio non sono disponibili input o output del servizio Web, né alcuna proprietà GlobalParameters configurata. Si noti che è ancora presente un output configurato nell'attività, ma non viene fornito come webServiceOutput.

	{
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### Il servizio Web usa moduli Reader e Writer e l'attività viene eseguita solo quando le altre attività hanno avuto esito positivo

I moduli Reader e Writer del servizio Web di Azure ML possono essere configurati per l'esecuzione con o senza proprietà GlobalParameters. È possibile comunque incorporare le chiamate di servizio in una pipeline di elaborazione che usa le dipendenze del set di dati per richiamare il servizio solo al termine dell'elaborazione upstream e quindi per attivare un'altra azione dopo il completamento dell'esecuzione batch. In tal caso è possibile esprimere le dipendenze tramite gli input e gli output dell'attività, senza denominarli input o output del servizio Web.

	{
	    "name": "retraining",
	    "type": "AzureMLBatchExecution",
	    "inputs": [
	        {
	            "name": "upstreamData1"
	        },
	        {
	            "name": "upstreamData2"
	        }
	    ],
	    "outputs": [
	        {
	            "name": "downstreamData"
	        }
	    ],
	    "typeProperties": {
	     },
	    "linkedServiceName": "mlEndpoint",
	    "policy": {
	        "concurrency": 1,
	        "executionPriorityOrder": "NewestFirst",
	        "retry": 1,
	        "timeout": "02:00:00"
	    }
	},

Le **informazioni chiave** sono:

-   Se l'endpoint dell'esperimento usa la proprietà webServiceInput, questa è rappresentata da un set di dati del BLOB ed è inclusa negli input dell'attività, nonché nella proprietà webServiceInput. In caso contrario, la proprietà webServiceInput viene omessa. 
-   Se l'endpoint dell'esperimento usa le proprietà webServiceOutputs, queste sono rappresentate da un set di dati del BLOB e sono incluse negli output dell'attività nonché nella proprietà webServicepOutputs (mappata in base al nome di ogni output presente nell'esperimento). In caso contrario, la proprietà webServiceOutputs viene omessa.
-   Se l'endpoint dell'esperimento espone le proprietà globalParameters, vengono assegnate alla proprietà globalParameters dell'attività come coppie chiave-valore. In caso contrario, la proprietà globalParameters viene omessa. Le chiavi distinguono tra maiuscole e minuscole. Le [funzioni di Data factory di Azure](data-factory-scheduling-and-execution.md#data-factory-functions-reference) possono essere usate nei valori. 
- Nelle proprietà di input e output delle dell'attività possono essere inclusi set di dati aggiuntivi, senza che vi si faccia riferimento nella sezione typeProperties dell'attività. Questi set di dati regoleranno l'esecuzione tramite le dipendenze delle sezioni, ma in caso contrario vengono ignorati dall'attività AzureMLBatchExecution. 


## Aggiornamento dei modelli di Azure ML con Attività della risorsa di aggiornamento
Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Azure ML usando nuovi set di dati di input. Una volta ripetuto il training, aggiornare il servizio Web di assegnazione dei punteggi con il modello ML di cui è stato ripetuto il training. Questa è la procedura tipica per abilitare la ripetizione del training e l'aggiornamento dei modelli di Azure ML tramite i servizi Web:

1. Creare un esperimento in [Azure ML Studio](https://studio.azureml.net). 
2. Quando si è soddisfatti del modello, usare Azure ML Studio per pubblicare i servizi Web sia per l'**esperimento di training** che per l'**esperimento predittivo** o di assegnazione dei punteggi.

La tabella seguente descrive i servizi Web usati in questo esempio. Per altre informazioni, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](../machine-learning/machine-learning-retrain-models-programmatically.md).

| Tipo di servizio Web | description 
| :------------------ | :---------- 
| **Training del servizio Web** | Riceve i dati di training e genera i modelli con training. L'output della ripetizione del training è un file con estensione ilearner in un archivio BLOB di Azure. L'**endpoint predefinito** viene creato automaticamente quando si pubblica l'esperimento di training come servizio Web. È possibile creare più endpoint, ma l'esempio usa solo quello predefinito. |
| **Servizio Web di assegnazione dei punteggi** | Riceve esempi di dati senza etichetta ed esegue stime. L'output della stima può avere diversi formati, ad esempio un file con estensione csv o righe in un database SQL di Azure, a seconda della configurazione dell'esperimento. L'endpoint predefinito viene creato automaticamente quando si pubblica l'esperimento predittivo come servizio Web. Sarà necessario creare il secondo **endpoint aggiornabile e non predefinito** tramite il [portale di Azure classico](https://manage.windowsazure.com). È possibile creare più endpoint, ma questo esempio usa solo quello aggiornabile non predefinito. Per la procedura, vedere l'articolo [Creare endpoint](../machine-learning/machine-learning-create-endpoint.md).       
 
L'immagine seguente illustra la relazione tra gli endpoint di training e di assegnazione dei punteggi in Azure ML.

![Servizi Web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


È possibile richiamare il **servizio Web di training** usando **Attività di esecuzione batch di Azure ML**. È la stessa operazione che si esegue per richiamare un servizio Web di Azure ML, il servizio Web di assegnazione dei punteggi, per la valutazione dei dati. Le sezioni precedenti descrivono in dettaglio come richiamare un servizio Web di Azure ML da una pipeline di Data factory di Azure.
  
È possibile richiamare il **servizio Web di assegnazione dei punteggi** usando **Attività della risorsa di aggiornamento di Azure ML** per aggiornare il servizio Web con il nuovo modello con training. Come indicato nella tabella precedente, è necessario creare e usare l'endpoint aggiornabile non predefinito. È anche consigliabile aggiornare i servizi collegati esistenti nella data factory perché usino l'endpoint non predefinito, così che sia sempre usato l'ultimo modello di cui è stato ripetuto il training.

Lo scenario seguente fornisce altri dettagli con un esempio per la ripetizione del training e l'aggiornamento dei modelli di Azure ML da una pipeline di Data factory di Azure.
 
### Scenario: ripetizione del training e aggiornamento di un modello di Azure ML
Questa sezione include una pipeline di esempio che usa **Attività di esecuzione batch di Azure ML** per ripetere il training di un modello e **Attività della risorsa di aggiornamento di Azure ML** per aggiornare il modello nel servizio Web di assegnazione dei punteggi. Fornisce anche frammenti di codice JSON per tutti i servizi collegati, i set di dati e la pipeline inclusi nell'esempio.

Ecco la vista diagramma della pipeline di esempio. Come si può vedere, Attività di esecuzione batch di Azure ML accetta l'input di training e genera l'output di training, un file iLearner. Attività della risorsa di aggiornamento di Azure ML accetta questo output di training e aggiorna il modello nell'endpoint di servizio Web di assegnazione dei punteggi. Attività della risorsa di aggiornamento non produce un output. placeholderBlob è solo un set di dati di output fittizio richiesto dal servizio Data factory di Azure per eseguire la pipeline.

![Diagramma della pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### Servizio collegato di archiviazione BLOB di Azure:
Archiviazione di Azure include i dati seguenti:

- Dati di training. Sono i dati di input per il servizio Web di training di Azure ML.  
- File iLearner. È l'output del servizio Web di training di Azure ML. È anche l'input per Attività della risorsa di aggiornamento.  
   
Ecco la definizione JSON di esempio del servizio collegato:

	{
		"name": "StorageLinkedService",
	  	"properties": {
	    	"type": "AzureStorage",
			"typeProperties": {
	    		"connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
			}
		}
	}


#### Set di dati di input di training:
Il set di dati seguente rappresenta i dati di training di input per il servizio Web di training di Azure ML. Attività di esecuzione batch di Azure ML accetta questo set di dati come input.

	{
	    "name": "trainingData",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "labeledexamples",
	            "fileName": "labeledexamples.arff",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
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

#### Set di dati di output di training:
Il set di dati seguente rappresenta il file iLearner di output del servizio Web di training di Azure ML. Attività di esecuzione batch di Azure ML genera questo set di dati. Questo set di dati è anche l'input per Attività della risorsa di aggiornamento.

	{
	    "name": "trainedModelBlob",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "trainingoutput",
	            "fileName": "model.ilearner",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        }
	    }
	}

#### Servizio collegato per l'endpoint di training di Azure ML 
Il frammento di codice JSON seguente definisce un servizio collegato di Azure Machine Learning che punta all'endpoint predefinito del servizio Web di training.

	{	
		"name": "trainingEndpoint",
	  	"properties": {
	    	"type": "AzureML",
	    	"typeProperties": {
	    		"mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
	      		"apiKey": "myKey"
	    	}
	  	}
	}

In **Azure ML Studio** eseguire queste operazioni per ottenere i valori per **mlEndpoint** e **apiKey**:

1. Fare clic su **SERVIZI WEB** nel menu a sinistra.
2. Fare clic su **servizio Web di training** nell'elenco dei servizi Web. 
3. Fare clic su copy accanto alla casella di testo **API key** per copiare la chiave API negli Appunti. Incollare la chiave nell'editor JSON di Data factory.
4. In **Azure ML Studio** fare clic sul collegamento **ESECUZIONE BATCH**, copiare il valore di **URI della richiesta** dalla sezione **Richiesta** e incollarlo nell'editor JSON di Data factory.   


#### Servizio collegato per l'endpoint di assegnazione dei punteggi aggiornabile di Azure ML:
Il frammento di codice JSON seguente definisce un servizio collegato di Azure Machine Learning che punta all'endpoint aggiornabile non predefinito del servizio Web di assegnazione dei punteggi.

	{
	    "name": "updatableScoringEndpoint2",
	    "properties": {
	        "type": "AzureML",
	        "typeProperties": {
	            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
	            "apiKey": "endpoint2Key",
	            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
	        }
	    }
	}


Prima di creare e distribuire un servizio collegato di Azure ML, seguire la procedura descritta nell'articolo [Creazione di endpoint](../machine-learning/machine-learning-create-endpoint.md) per creare un secondo endpoint, aggiornabile e non predefinito, per il servizio Web di assegnazione dei punteggi.

Dopo avere creato l'endpoint aggiornabile e non predefinito, fare clic su **BATCH EXECUTION** per ottenere il valore dell'URI per la proprietà JSON **mlEndpoint** e fare clic sul collegamento **UPDATE RESOURCE** per ottenere il valore dell'URI per la proprietà JSON **updateResourceEndpoint**. La chiave API si trova nell'angolo in basso a destra della pagina dell'endpoint.

![Endpoint aggiornabile](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### Set di dati di output del segnaposto:
Attività della risorsa di aggiornamento di Azure ML non genera alcun output, ma in Data factory di Azure è richiesto un set di dati di output per gestire la pianificazione della pipeline, quindi in questo esempio viene usato un set di dati segnaposto fittizio.

	{
	    "name": "placeholderBlob",
	    "properties": {
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        },
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "any",
	            "format": {
	                "type": "TextFormat"
	            }
	        }
	    }
	}


#### Pipeline
La pipeline include due attività: **AzureMLBatchExecution** e **AzureMLUpdateResource**. Attività di esecuzione batch di Azure ML accetta i dati di training come input e genera il file con estensione iLearner come output. L'attività richiama il servizio Web di training, l'esperimento di training esposto come servizio Web, con i dati di training di input e riceve il file iLearner dal servizio Web. placeholderBlob è solo un set di dati di output fittizio richiesto dal servizio Data factory di Azure per eseguire la pipeline.

![Diagramma della pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


	{
	    "name": "pipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "retraining",
	                "type": "AzureMLBatchExecution",
	                "inputs": [
	                    {
	                        "name": "trainingData"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "typeProperties": {
	                    "webServiceInput": "trainingData",
	                    "webServiceOutputs": {
	                        "output1": "trainedModelBlob"
	                    }              
	                 },
	                "linkedServiceName": "trainingEndpoint",
	                "policy": {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "02:00:00"
	                }
	            },
	            {
	                "type": "AzureMLUpdateResource",
	                "typeProperties": {
	                    "trainedModelName": "Training Exp for ADF ML [trained model]",
	                    "trainedModelDatasetName" :  "trainedModelBlob"
	                },
	                "inputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "placeholderBlob"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "name": "AzureML Update Resource",
	                "linkedServiceName": "updatableScoringEndpoint2"
	            }
	        ],
	    	"start": "2015-02-13T00:00:00Z",
	   		"end": "2015-02-14T00:00:00Z"
	    }
	}


### Moduli Reader e Writer

Uno scenario comune per l'uso dei parametri del servizio Web è costituito dai reader e dai writer SQL di Azure. Il modulo reader viene usato per caricare dati in un esperimento da servizi di gestione dati esterni ad Azure Machine Learning Studio e il modulo writer consente di salvare i dati degli esperimenti in servizi di gestione dati esterni ad Azure Machine Learning Studio.

Per informazioni dettagliate sui moduli reader/writer di BLOB di Azure/SQL di Azure, vedere gli argomenti [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in MSDN Library. L'esempio della sezione precedente usa un reader e un writer di BLOB di Azure. Questa sezione illustra l'uso di un reader e un writer SQL di Azure.


## Domande frequenti

**D:** Ho più file generati dalle pipeline di Big Data. Posso usare l'attività AzureMLBatchExecution per lavorare con tali file?

**R:** Sì. Per informazioni dettagliate, vedere la sezione **Uso di un modulo Reader per leggere dati da più file del BLOB di Azure**.

## Attività di assegnazione dei punteggi di batch di Azure ML
Se si sta usando l'attività **AzureMLBatchScoring** per l'integrazione con Azure Machine Learning, si consiglia di passare alla più recente attività **AzureMLBatchExecution**.

L'attività AzureMLBatchExecution è stata introdotta nella versione di Azure SDK e Azure PowerShell di agosto 2015.

Se si vuole continuare a usare l'attività AzureMLBatchScoring, continuare a leggere questa sezione.

### Attività di assegnazione dei punteggi di batch di Azure ML che usa Archiviazione di Azure per l'input/output 

	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchScoring",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "ScoringInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "ScoringResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
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

### Parametri del servizio Web
Aggiungere una sezione **typeProperties** alla sezione **AzureMLBatchScoringActivity** nel file JSON della pipeline per specificare i valori per i parametri del servizio Web in tale sezione, come illustrato nell'esempio seguente:

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


È anche possibile usare le [funzioni di Data factory](https://msdn.microsoft.com/library/dn835056.aspx) per passare i valori per i parametri del servizio Web, come illustrato nell'esempio seguente:

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] I parametri del servizio Web applicano la distinzione tra maiuscole e minuscole. È quindi necessario assicurarsi che i nomi specificati nel file JSON dell'attività corrispondano ai nomi esposti dal servizio Web.

## Vedere anche

- [Post di blog di Azure: Guida introduttiva a Data Factory di Azure e ad Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=AcomDC_0224_2016-->