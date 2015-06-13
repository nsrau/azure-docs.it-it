<properties 
	pageTitle="Richiamare il programma MapReduce da Data factory di Azure" 
	description="Informazioni su come elaborare i dati tramite l'esecuzione di programmi MapReduce su un cluster HDInsight di Azure da una factory di dati di Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Richiamare i programmi MapReduce da Data factory
In questo articolo viene descritto come richiamare un **MapReduce** programma da una pipeline di Factory di dati di Azure tramite il **attività HDInsight** con **MapReduce trasformazione**.

## Introduzione 
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Questo articolo descrive l'uso della trasformazione MapReduce dell'attività HDInsight.
 
Vedere [utilizzare Pig e Hive con dati Factory][data-factory-pig-hive-activities] per informazioni dettagliate sull'esecuzione di Pig o Hive script in un HDInsight cluster da una pipeline di factory di dati di Azure utilizzando le trasformazioni Pig o Hive dell'attività HDInsight.

## JSON per attività di HDInsight mediante trasformazione MapReduce 

Nella definizione JSON per l'attività HDInsight:
 
1. Impostare il **tipo** del **attività** a **HDInsightActivity**.
2. Impostare il **tipo** del **trasformazione** a **MapReduce**.
3. Specificare il nome della classe per **className** proprietà.
4. Specificare il percorso di file con estensione JAR incluso il nome del file per **jarFilePath** proprietà.
5. Specificare il servizio collegato che fa riferimento nell'archiviazione Blob di Azure che contiene il file JAR per **jarLinkedService** proprietà.   
6. Specificare gli argomenti per il programma MapReduce nel **argomenti** sezione. 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

È possibile usare la trasformazione MapReduce per l'esecuzione di file JAR di MapReduce in un cluster HDInsight. Nella definizione JSON seguente di una pipeline di esempio l'attività HDInsight è configurata per eseguire un file JAR di Mahout.

## Esempio
È possibile scaricare un esempio per l'utilizzo dell'attività HDInsight con MapReduce trasformazione da: [dati di esempio Factory su GitHub][data-factory-samples].

## Vedere anche

Articolo | Descrizione
------ | ---------------
[Esercitazione: Spostare ed elaborare i file di log utilizzando la Factory di dati][adf-tutorial] | Questo articolo viene fornita una procedura dettagliata end-to-end in cui viene illustrato come implementare una reale quasi utilizza Azure dati Factory per trasformare i dati dai file di log in approfondimenti scenario del mondo. In questa esercitazione si utilizzerà entrambe le trasformazioni di Pig e Hive per elaborare i dati. 
[Riferimenti per sviluppatori Factory di dati di Azure][developer-reference] | Il riferimento per sviluppatori presenta il contenuto di riferimento completo per i cmdlet, script JSON, funzioni e così via... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir--> 