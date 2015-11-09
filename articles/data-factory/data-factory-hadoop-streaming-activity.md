<properties 
	pageTitle="Attività di Hadoop Streaming" 
	description="Informazioni su come usare l'attività di Hadoop Streaming in una Data factory di Azure per eseguire i programmi di Hadoop Streaming in un cluster HDInsight personalizzato o on demand." 
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
	ms.date="10/25/2015" 
	ms.author="spelluru"/>

# Attività di Hadoop Streaming
È possibile usare l'attività HDInsightStreamingActivity per richiamare un processo di Hadoop Streaming da una pipeline di Data factory di Azure. Il frammento JSON seguente illustra la sintassi per l'uso di HDInsightStreamingActivity in un file JSON della pipeline.

L'attività HDInsightStreamingActivity in una [pipeline](data-factory-create-pipelines.md) di una Data factory esegue i programmi di Hadoop Streaming nei cluster HDInsight [personalizzati](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o [on demand](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md), che presenta una panoramica generale della trasformazione dei dati e delle attività di trasformazione supportate.

## Esempio

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "getDebugInfo": "Failure",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
	               },
	               "policy":
	               {
	                   "concurrency": 1,
	                   "executionPriorityOrder": "NewestFirst",
	                   "retry": 1,
	                   "timeout": "01:00:00"
	               }
	            }
	        ]
	    }
	}

Tenere presente quanto segue:

1. Impostare **linkedServiceName** sul nome del servizio collegato che punta al cluster HDInsight in cui verrà eseguito il processo di streaming mapreduce.
2. Impostare il tipo di attività su **HDInsightStreaming**.
3. Per la proprietà **mapper** specificare il nome dell'eseguibile del mapper. Nell'esempio precedente cat.exe è l'eseguibile del mapper.
4. Per la proprietà **reducer** specificare il nome dell'eseguibile del reducer. Nell'esempio precedente wc.exe è l'eseguibile del reducer.
5. Per la proprietà **input** specificare il file di input (incluso il percorso) per il mapper. Nell'esempio: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample è il contenitore BLOB, example/data/Gutenberg è la cartella e davinci.txt è il BLOB.
6. Per la proprietà **output** specificare il file di output (incluso il percorso) per il reducer. L'output del processo di Hadoop Streaming verrà scritto nel percorso specificato per questa proprietà.
7. Nella sezione **filePaths** specificare i percorsi dei file eseguibili del mapper e del reducer. Nell'esempio: "adfsample/example/apps/wc.exe", adfsample è il contenitore BLOB, example/apps è la cartella e wc.exe è l'eseguibile.
8. Per la proprietà **fileLinkedService** specificare il servizio collegato Archiviazione di Azure che rappresenta l'archivio di Azure contenente i file specificati nella sezione filePaths.
9. Per la proprietà **arguments** specificare gli argomenti per il processo di streaming.
10. La proprietà **getDebugInfo** è un elemento facoltativo. Quando viene impostata su Failure, i log vengono scaricati solo in caso di errore. Quando viene impostata su All, i log vengono sempre scaricati indipendentemente dallo stato dell'esecuzione. 

	

<!---HONumber=Nov15_HO1-->