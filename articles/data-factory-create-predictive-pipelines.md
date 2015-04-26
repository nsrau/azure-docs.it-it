<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Data factory - Creazione di pipeline predittive tramite Data factory e Machine Learning | Azure" description="Informazioni su come creare pipeline predittive tramite Azure Data Factory e Azure Machine Learning." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Creazione di pipeline predittive tramite Data factory di Azure e Azure Machine Learning 
È possibile rendere operativi i modelli pubblicati di [Azure Machine Learning][azure-machine-learning] nelle pipeline di Data factory di Azure. Queste pipeline sono definite pipeline predittive. Per creare una pipeline predittiva, sono necessari gli elementi seguenti:

-	La chiave API del modello dell'area di lavoro pubblicato e l'URL per l'assegnazione dei punteggi di batch (vedere l'immagine seguente)
-	Una risorsa di archiviazione BLOB di Azure contenente il file con estensione CSV di input a cui assegnare il punteggio.
-	Una risorsa di archiviazione BLOB di Azure contenente il file con estensione CSV con i risultati dell'assegnazione del punteggio.

	![Machine Learning Dashboard][machine-learning-dashboard]

	L'URL per l'assegnazione dei punteggi di batch per AzureMLLinkedService viene ottenuto nel modo illustrato nella figura sopra riportata, meno '**help**':  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Una **pipeline predittiva** include gli elementi seguenti:

-	Tabelle di input e output
-	Servizi collegati di Archiviazione di Azure e Azure ML
-	Una pipeline con attività di assegnazione dei punteggi di batch di Azure ML

## Esempio



1. Creare un servizio collegato per Archiviazione di Azure Se i file di output e input di assegnazione del punteggio si trovano in account di archiviazione diversi, sarà necessario disporre di due servizi collegati. Di seguito è fornito un esempio JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Creare le tabelle di input e output di Data factory di Azure. Tenere presente che a differenza di altre tabelle di Data factory, queste devono contenere entrambi i valori di **folderPath** e **fileName**. È possibile usare il partizionamento per fare in modo che ogni esecuzione di batch (ogni sezione di dati) elabori o produca file di input e di output univoci. Potrebbe essere necessario includere alcune attività upstream per trasformare il file di input in formato CSV e collocarlo nell'account di archiviazione per ogni sezione. In tal caso, non è necessario includere le impostazioni "waitOnExternal" illustrate nell'esempio seguente e ScoringInputBlob è la tabella di output di un'attività diversa.

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

5. Infine, creare una pipeline contenente un'attività **AzureMLBatchScoringActivity**. La pipeline otterrà il percorso del file di input dalle tabelle di input, chiamerà l'API per l'assegnazione dei punteggi di batch di Azure ML e copierà il file di output dell'assegnazione dei punteggi di batch nel BLOB specificato nella tabella di output. A differenza di altre attività di Data factory, AzureMLBatchScoringActivity può avere una sola tabella di input e una sola tabella di output.

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

## Vedere anche

Articolo | Descrizione
------ | ---------------
[Introduzione a Data factory di Azure][adf-introduction] | In questo articolo viene fornita una panoramica del servizio Data factory di Azure e degli scenari supportati.
[Introduzione a Data factory di Azure][adf-getstarted] | In questo articolo viene fornita un'esercitazione di base con istruzioni dettagliate per la creazione e il monitoraggio di un'istanza di Data factory di esempio.
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database di SQL Server locale a un BLOB di Azure.
[Usare Pig e Hive con Data factory][use-pig-and-hive-with-data-factory] | Questo articolo contiene una procedura dettagliata che mostra come usare l'attività HDInsight per eseguire uno script hive/pig per elaborare i dati di input in modo da generare i dati di output. 
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure. È possibile provare la procedura dettagliata di questo articolo in ADFTutorialDataFactory introducendo un errore (eliminando la tabella nel database SQL di Azure). 
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/it-it/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
