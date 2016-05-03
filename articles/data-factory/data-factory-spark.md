<properties 
	pageTitle="Chiamare i programmi Spark da Azure Data Factory" 
	description="È possibile chiamare i programmi Spark da una data factory di Azure usando l'attività MapReduce." 
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
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Chiamare i programmi Spark da Data Factory
## Introduzione
È possibile usare l'attività MapReduce in una pipeline di Data Factory per eseguire programmi Spark nel cluster HDInsight Spark. Prima di leggere questo articolo, vedere l'articolo relativo all'[attività MapReduce](data-factory-map-reduce.md) per informazioni dettagliate sull'uso dell'attività.

## Esempio di Spark in GitHub
L'[esempio Spark - Data Factory in GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) spiega come usare l'attività MapReduce per chiamare un programma Spark. Il programma Spark si limita a copiare i dati da un contenitore BLOB di Azure a un altro.

## Entità di Data factory
La cartella **Spark-ADF/src/ADFJsons** contiene i file per le entità di Data Factory, ad esempio servizi collegati, set di dati e pipeline.

Esistono due **servizi collegati** in questo esempio: Archiviazione di Azure e Azure HDInsight. È necessario specificare il nome di archiviazione di Azure e i valori chiave in **StorageLinkedService.json** e URI del cluster, nome utente e password in **HDInsightLinkedService.json**.

Esistono due **set di dati** in questo esempio: **input.json** e **output.json**. Questi file si trovano nella cartella dei **set di dati**. I file rappresentano i set di dati di input e output per l'attività MapReduce.

Nell'esempio esiste una sola **pipeline** nella cartella **ADFJsons/Pipeline**. Si tratta dell'entità più importante ed è necessario esaminarla per capire in che modo chiamare un programma Spark usando l'attività MapReduce.

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

Come si può notare, l'attività MapReduce è configurata per chiamare **spark-adf-job-bin.jar** nel contenitore **libs** dell'archiviazione Azure (specificato in StorageLinkedService.json). Il codice sorgente per questo programma si trova nella cartella Spark-ADF/src/main/java/com/adf/spark ed esegue processi Spark e chiamate a spark-submit.

Il programma MapReduce (spark-adf-job-bin.jar) in esecuzione nel cluster HDInsight Spark chiama il programma Spark **sparkdemoapp\_2.10-1.0.jar** e passa gli argomenti ricevuti tramite l'attività MapReduce, come indicato nel JSON precedente, al programma Spark. **sparkdemoapp\_2.10-1.0.jar** contiene codice sorgente Scala che copia i dati da un contenitore BLOB di Azure a un altro. È possibile sostituire il file jar dell'app demo con qualsiasi altro file jar contenente i processi che si sta tentando di eseguire usando Spark.

In breve, l'**attività MapReduce** chiama il programma MapReduce **spark-adf-job-bin.jar** che chiama il programma Spark **sparkdemoapp\_2.10-1.0. jar**. Per eseguire un programma Spark in uso, sostituire sparkdemoapp\_2.10-1.0.jar con il proprio programma.

> [AZURE.NOTE] È necessario usare il proprio cluster HDInsight Spark con questo approccio per chiamare i programmi Spark usando l'attività MapReduce. L'uso di un cluster HDInsight su richiesta non è supportato.


## Vedere anche
- [Attività Hive](data-factory-hive-activity.md)
- [Attività di Pig](data-factory-pig-activity.md)
- [Attività MapReduce](data-factory-map-reduce.md)
- [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
- [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0420_2016-->