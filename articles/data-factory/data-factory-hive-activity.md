<properties 
	pageTitle="Attività Hive" 
	description="Informazioni su come usare l'attività Hive in una data factory di Azure per eseguire query Hive in un cluster HDInsight su richiesta o nel proprio cluster HDInsight." 
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

# Attività Hive

L'attività Hive di HDInsight in una [pipeline](data-factory-create-pipelines.md) di Data factory esegue query Hive sul [proprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight o sul cluster HDInsight [su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basato su Windows o Linux. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

## Sintassi

	{
		"name": "Hive Activity",
	    "description": "description",
	    "type": "HDInsightHive",
	    "inputs": [
	      {
	        "name": "input tables"
	      }
	    ],
	    "outputs": [
	      {
	        "name": "output tables"
	      }
	    ],
	    "linkedServiceName": "MyHDInsightLinkedService",
	    "typeProperties": {
	      "script": "Hive script",
	      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
	      "defines": {
	        "param1": "param1Value"
	      }
	    },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
	}
	
## Dettagli sintassi

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
name | Nome dell'attività | Sì
description | Testo descrittivo per lo scopo dell'attività | No
type | HDinsightHive | Sì
inputs | Input utilizzati dall'attività Hive | No
outputs | Output generati dall'attività Hive | Sì 
linkedServiceName | Riferimento al cluster HDInsight registrato come servizio collegato in Data factory | Sì 
script | Specificare lo script Hive inline | No
script path | Archiviare lo script Hive in un archivio BLOB di Azure e immettere il percorso del file. Usare la proprietà "script" o "scriptPath", tenendo presente che non è possibile usare entrambe le proprietà contemporaneamente | No 
defines | Specificare i parametri come coppie chiave/valore per fare riferimento ad essi nello script Hive usando "hiveconf" | No

## Esempio

Si prenda ad esempio l'analisi di log di giochi, in cui si desidera verificare il tempo che gli utenti hanno dedicato a giocare alle partite avviate dalla società.

Di seguito è riportato il log di esempio di un gioco in cui i valori sono separati da virgola (,) e che contiene i campi ProfileID, SessionStart, Duration, SrcIPAddress e GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

Lo **script Hive** per elaborare tali dati sarà simile al seguente:

	DROP TABLE IF EXISTS HiveSampleIn; 
	CREATE EXTERNAL TABLE HiveSampleIn 
	(
		ProfileID		string, 
	    SessionStart 	string, 
	    Duration 		int, 
	    SrcIPAddress 	string, 
	    GameType 		string
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
	
	DROP TABLE IF EXISTS HiveSampleOut; 
	CREATE EXTERNAL TABLE HiveSampleOut 
	(	
		ProfileID 	string, 
	    Duration 	int
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
	
	INSERT OVERWRITE TABLE HiveSampleOut
	Select 
		ProfileID,
		SUM(Duration)
	FROM HiveSampleIn Group by ProfileID

Per eseguire lo script Hive in una pipeline di Data factory, è necessario seguire questa procedura:

1. Creare un servizio collegato per registrare [il proprio cluster di elaborazione di HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oppure configurare [un cluster di elaborazione di HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). In questo esempio il servizio collegato è denominato "HDInsightLinkedService".
2. Creare un [servizio collegato](data-factory-azure-blob-connector.md) per configurare la connessione all'archivio BLOB di Azure che ospita i dati. In questo esempio il servizio collegato è denominato "StorageLinkedService".
3. Creare [set di dati](data-factory-create-datasets.md) che puntano ai dati di input e output. In questo esempio il set di dati di input è denominato "HiveSampleIn", mentre il set di dati di output è denominato "HiveSampleOut".
4. Copiare la query Hive come file nell'archivio BLOB di Azure configurato nel passaggio 2. Se il servizio collegato che deve ospitare i dati è diverso da quello che ospita il file di query, creare un altro servizio collegato di Archiviazione di Azure e fare riferimento a quest'ultimo per la configurazione dell'attività. Usare **scriptPath** per specificare il percorso del file di query Hive e **scriptLinkedService** per specificare l'archivio di Azure contenente il file script.

	> [AZURE.NOTE] In alternativa, è possibile specificare lo script Hive inline nella definizione di attività usando la proprietà **script**. Questa, tuttavia, non è la procedura consigliata poiché tutti i caratteri speciali usati nello script all'interno del documento JSON devono essere preceduti da un carattere di escape e questo può causare problemi di debug. Si consiglia di seguire il passaggio 4.
5.	Creare la pipeline riportata di seguito con l'attività HDInsightHive per elaborare i dati.

		{
		  "name": "HiveActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "HiveActivitySample",
		        "type": "HDInsightHive",
		        "inputs": [
		          {
		            "name": "HiveSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "HiveSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Hour",
          			"interval": 1
        		}
		      }
		    ]
		  }
		}

6.	Distribuire la pipeline. Per informazioni dettagliate, vedere l'argomento relativo alla [creazione di pipeline](data-factory-create-pipelines.md).
7.	Monitorare la pipeline mediante le viste di monitoraggio e gestione delle pipeline di Data factory. Per informazioni dettagliate, vedere [Monitorare e gestire le pipeline di Data factory di Azure](data-factory-monitor-manage-pipelines.md). 


## Specifica dei parametri per uno script Hive mediante l'elemento defines 

Si consideri un esempio in cui i log di giochi vengono inseriti giornalmente nel sistema di archiviazione BLOB di Azure e memorizzati in una cartella partizionata con data e ora. Si desidera impostare i parametri per lo script Hive e passare il percorso della cartella di input in modo dinamico durante il runtime, generando l'output partizionato con data e ora.

Per impostare i parametri per lo script Hive, seguire questa procedura:

- Definire i parametri in **defines**.

		{
			"name": "HiveActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		     	{
		        	"name": "HiveActivitySample",
		        	"type": "HDInsightHive",
			        "inputs": [
			          	{
				            "name": "HiveSampleIn"
				          }
		        	],
		        	"outputs": [
		          		{
				            "name": "HiveSampleOut"
				        }
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            		"Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		},
       					"scheduler": {
          					"frequency": "Hour",
          					"interval": 1
        				}
		        	}
		      	}
		    ]
		  }
		}

- Nello script Hive fare riferimento al parametro tramite **${hiveconf:parameterName}**.

		DROP TABLE IF EXISTS HiveSampleIn; 
		CREATE EXTERNAL TABLE HiveSampleIn 
		(
			ProfileID 	string, 
		    SessionStart 	string, 
		    Duration 	int, 
		    SrcIPAddress 	string, 
		    GameType 	string
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
		
		DROP TABLE IF EXISTS HiveSampleOut; 
		CREATE EXTERNAL TABLE HiveSampleOut 
		(
		    ProfileID 	string, 
		    Duration 	int
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
		
		INSERT OVERWRITE TABLE HiveSampleOut
		Select 
			ProfileID,
			SUM(Duration)
		FROM HiveSampleIn Group by ProfileID

<!---HONumber=AcomDC_0204_2016-->