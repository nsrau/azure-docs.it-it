<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Richiamare il programma MapReduce da Data factory di Azure" description="Informazioni su come elaborare dati eseguendo programmi MapReduce in un cluster HDInsight di Azure da una data factory di Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Richiamare i programmi MapReduce da Data factory
Questo articolo descrive come richiamare un programma **MapReduce** da una pipeline di Data factory di Azure usando l'**attività HDInsight** con la **trasformazione MapReduce**. 

## Introduzione 
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. 

- L'**attività di copia** consente di copiare dati da un archivio di origine a un archivio di destinazione. Per altre informazioni sull'attività di copia, vedere [Copia di dati con Data factory][data-factory-copy-activity]. 
- L'**attività HDInsight** elabora i dati tramite l'esecuzione di script Hive/Pig o di programmi MapReduce in un cluster HDInsight. L'attività HDInsight supporta tre trasformazioni: **Hive**, **Pig** e **MapReduce**. L'attività HDInsight può utilizzare uno o più input e generare uno o più output.
 
Vedere [Usare Pig e Hive con Data factory][data-factory-pig-hive-activities] per altre informazioni sull'esecuzione di script Pig/Hive in un cluster HDInsight da una pipeline di Data factory di Azure mediante le trasformazioni Pig/Hive dell'attività HDInsight. Questo articolo descrive l'uso della trasformazione MapReduce dell'attività HDInsight.

## Pipeline JSON
Nel file JSON per una pipeline:
 
1. Impostare il **tipo** di **attività** su **HDInsightActivity**.
2. Impostare il **tipo** di **trasformazione** su **MapReduce**.
3. Specificare il nome della classe per la proprietà **className**.
4. Specificare il percorso del file JAR, incluso il nome di file per la proprietà **jarFilePath**.
5. Specificare il servizio collegato che fa riferimento all'archiviazione BLOB di Azure che contiene il file JAR per la proprietà **jarLinkedService**.   
6. Specificare gli argomenti per il programma MapReduce nella sezione **arguments**. 

È possibile usare la trasformazione MapReduce per l'esecuzione di file JAR di MapReduce in un cluster HDInsight. Nella definizione JSON seguente di una pipeline di esempio l'attività HDInsight è configurata per eseguire un file JAR di Mahout.   
 

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

## Esempio
È possibile scaricare un esempio per l'uso dell'attività HDInsight con la trasformazione MapReduce da: [Esempi di Data factory in GitHub][data-factory-samples].  

## Vedere anche

Articolo | Descrizione
------ | ---------------
[Introduzione a Data factory di Azure][data-factory-introduction] | Questo articolo fornisce un'introduzione al servizio Data factory di Azure, ai concetti, al valore che offre e agli scenari che supporta.
[Introduzione a Data factory di Azure][adf-getstarted] | Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database di SQL Server locale a un BLOB di Azure.
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure.  
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
