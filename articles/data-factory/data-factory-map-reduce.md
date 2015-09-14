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
	ms.date="07/31/2015"
	ms.author="spelluru"/>

# Richiamare i programmi MapReduce da Data factory
Questo articolo descrive come richiamare un programma **MapReduce** da una pipeline di Data factory di Azure usando l'**attività MapReduce di HDInsight**.

## Introduzione 
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Questo articolo descrive l'uso della trasformazione MapReduce dell'attività HDInsight.
 
Per informazioni dettagliate sull'esecuzione di script Pig/Hive in un cluster HDInsight da una pipeline di Data Factory di Azure mediante le trasformazioni Pig/Hive dell'attività HDInsight, vedere l'articolo relativo a [Pig](data-factory-pig-activity) e [Hive](data-factory-hive-activity.md).

## JSON per l'attività HDInsight con la trasformazione MapReduce 

Nella definizione JSON per l'attività HDInsight:
 
1. Impostare l'oggetto **type** di **activity** su **HDInsight**.
3. Specificare il nome della classe per la proprietà **className**.
4. Specificare il percorso del file JAR, incluso il nome di file per la proprietà **jarFilePath**.
5. Specificare il servizio collegato che fa riferimento all'archivio BLOB di Azure contenente il file JAR per la proprietà **jarLinkedService**.   
6. Specificare gli eventuali argomenti per il programma MapReduce nella sezione **arguments**. 

 

		{
		  "name": "MahoutMapReduceSamplePipeline",
		  "properties": {
		    "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		    "activities": [
		      {
		        "name": "MyMahoutActivity",
		        "description": "Custom Map Reduce to generate Mahout result",
		        "inputs": [
		          {
		            "Name": "MahoutInput"
		          }
		        ],
		        "outputs": [
		          {
		            "Name": "MahoutOutput"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "type": "HDInsightMapReduce",
		        "typeProperties": {
		          "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		          "jarFilePath": "<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		          "jarLinkedService": "StorageLinkedService",
		          "arguments": [
		            "-s",
		            "SIMILARITY_LOGLIKELIHOOD",
		            "--input",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Input/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--output",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Output/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--maxSimilaritiesPerItem",
		            "500",
		            "--tempDir",
		            "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		          ]
		        },
		        "policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "OldestFirst",
		          "retry": 3,
		          "timeout": "01:00:00"
		        }
		      }
		    ]
		  }
		}

È possibile usare la trasformazione MapReduce per l'esecuzione di file JAR di MapReduce in un cluster HDInsight. Nella definizione JSON seguente di una pipeline di esempio l'attività HDInsight è configurata per eseguire un file JAR di Mahout.

## Esempio
È possibile scaricare un esempio relativo all'uso dell'attività HDInsight con la trasformazione MapReduce dagli [esempi di Data factory in GitHub](data-factory-samples.md).


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=September15_HO1-->