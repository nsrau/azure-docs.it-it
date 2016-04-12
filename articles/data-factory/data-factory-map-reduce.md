<properties 
	pageTitle="Richiamare il programma MapReduce da Data factory di Azure" 
	description="Informazioni su come elaborare i dati eseguendo programmi MapReduce in un cluster Azure HDInsight da un'istanza di Data factory di Azure." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Richiamare i programmi MapReduce da Data factory
L'attività HDInsight MapReduce in una [pipeline](data-factory-create-pipelines.md) di Data factory esegue i programmi di MapReduce nei cluster HDInsight [personalizzati](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basati su Windows/Linux. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

## Introduzione 
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. In questo articolo viene descritto l'utilizzo dell'attività MapReduce di HDInsight.
 
Vedere l’articolo su [Pig](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) per informazioni dettagliate sull'esecuzione di script Pig/Hive in un cluster HDInsight basato su Windows/Linux da una pipeline di Data Factory di Azure mediante le attività Pig e Hive di HDInsight.

## JSON per attività MapReduce di HDInsight 

Nella definizione JSON per l'attività HDInsight:
 
1. Impostare l'oggetto **type** di **activity** su **HDInsight**.
3. Specificare il nome della classe per la proprietà **className**.
4. Specificare il percorso del file JAR, incluso il nome di file per la proprietà **jarFilePath**.
5. Specificare il servizio collegato che fa riferimento all'archivio BLOB di Azure contenente il file JAR per la proprietà **jarLinkedService**.   
6. Specificare gli eventuali argomenti per il programma MapReduce nella sezione **arguments**. In fase di esecuzione, verranno visualizzati alcuni argomenti aggiuntivi (ad esempio: mapreduce.job.tags) dal framework di MapReduce. Per differenziare gli argomenti con gli argomenti di MapReduce, è consigliabile utilizzare sia l’opzione che il valore come argomenti, come illustrato nell'esempio seguente (- s, --input - output e così via sono opzioni immediatamente seguite dai valori).

		{
		    "name": "MahoutMapReduceSamplePipeline",
		    "properties": {
		        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		        "activities": [
		            {
		                "type": "HDInsightMapReduce",
		                "typeProperties": {
		                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
		                    "jarLinkedService": "StorageLinkedService",
		                    "arguments": [
		                        "-s",
		                        "SIMILARITY_LOGLIKELIHOOD",
		                        "--input",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
		                        "--output",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
		                        "--maxSimilaritiesPerItem",
		                        "500",
		                        "--tempDir",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
		                    ]
		                },
		                "inputs": [
		                    {
		                        "name": "MahoutInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "MahoutOutput"
		                    }
		                ],
		                "policy": {
		                    "timeout": "01:00:00",
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "MahoutActivity",
		                "description": "Custom Map Reduce to generate Mahout result",
		                "linkedServiceName": "HDInsightLinkedService"
		            }
		        ],
		        "start": "2014-01-03T00:00:00Z",
		        "end": "2014-01-04T00:00:00Z",
		        "isPaused": false,
		        "hubName": "mrfactory_hub",
		        "pipelineMode": "Scheduled"
		    }
		}
	
	

È possibile usare l’attività MapReduce di HDInsight per l'esecuzione di file JAR di MapReduce in un cluster HDInsight. Nella definizione JSON seguente di una pipeline di esempio l'attività HDInsight è configurata per eseguire un file JAR di Mahout.

## Esempio in GitHub
È possibile scaricare un esempio relativo all'uso dell'attività MapReduce di HDInsight da: [esempi di Data factory in GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).

## Esecuzione del programma di conteggio delle parole
La pipeline in questo esempio esegue il programma di mapping e riduzione del conteggio delle parole sul cluster HDInsight di Azure.

### Servizi collegati
In primo luogo, si crea un servizio collegato per collegare l'archiviazione di Azure utilizzata dal cluster HDInsight di Azure per la factory di dati di Azure. Se si copia e incolla il codice seguente, non dimenticare di sostituire **nome account** e **chiave account** con il nome e la chiave di archiviazione di Azure.

#### Servizio collegato Archiviazione di Azure

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Servizio collegato Azure HDInsight
Successivamente, si crea un servizio collegato per collegare il cluster HDInsight di Azure alla factory di dati di Azure. Se si copia e incolla il codice seguente, sostituire **nome del cluster HDInsight** con il nome del cluster HDInsight e modificare i valori nome utente e password.

	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### Set di dati

#### Set di dati di output
La pipeline in questo esempio non accetta alcun input. È necessario specificare un set di dati di output per l'attività MapReduce di HDInsight. Questo è solo un set di dati fittizio che è necessario per la pianificazione della pipeline.

	{
	    "name": "MROutput",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "WordCountOutput1.txt",
	            "folderPath": "example/data/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Pipeline
La pipeline in questo esempio contiene una sola attività che è di tipo: HDInsightMapReduce. Alcune delle proprietà importanti in JSON sono:

Proprietà | Note
:-------- | :-----
type | Il tipo deve essere impostato su **HDInsightMapReduce**. 
className | Il nome della classe è: **wordcount**
jarFilePath | Percorso del file jar contenente la classe precedente. Se si copia e incolla il codice seguente, non dimenticare di modificare il nome del cluster. 
jarLinkedService | Servizio collegato di Archiviazione di Azure che contiene il file jar. Questo è lo spazio di archiviazione associato al cluster HDInsight. 
argomenti | Il programma wordcount accetta due argomenti, un input e un output. Il file di input è il file davinci.txt.
frequenza/intervallo | I valori per queste proprietà corrispondono al set di dati di output. 
linkedServiceName | fa riferimento al servizio collegato di HDInsight creato in precedenza.   

	{
	    "name": "MRSamplePipeline",
	    "properties": {
	        "description": "Sample Pipeline to Run the Word Count Program",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "wordcount",
	                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "/example/data/gutenberg/davinci.txt",
	                        "/example/data/WordCountOutput1"
	                    ]
	                },
	                "outputs": [
	                    {
	                        "name": "MROutput"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "MRActivity",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-03T00:00:00Z",
	        "end": "2014-01-04T00:00:00Z"
	    }
	}

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#monitor-pipelines
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Classic Portal]: http://portal.azure.com
 

<!---HONumber=AcomDC_0323_2016-->