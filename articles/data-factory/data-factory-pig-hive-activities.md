<properties 
	pageTitle="Usare Pig e Hive con Data factory di Azure" 
	description="Informazioni su come elaborare i dati eseguendo script Pig e Hive in un cluster HDInsight di Azure da un'istanza di Data factory di Azure." 
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
	ms.date="06/19/2015" 
	ms.author="spelluru"/>

# Usare Pig e Hive con Data factory
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. In questo articolo viene descritto l'utilizzo dell'attività HDInsight con la trasformazione Pig o Hive in una pipeline di Data factory di Azure. Vedere [Richiamare i programmi MapReduce da Data factory][data-factory-map-reduce] per informazioni dettagliate sull'esecuzione dei programmi MapReduce in un cluster HDInsight da una pipeline di Data factory di Azure.

## Procedura dettagliata: Usare Hive con Data factory di Azure
Questa procedura dettagliata fornisce istruzioni dettagliate per l'utilizzo di un'attività di HDInsight con la trasformazione Hive in una pipeline di Data factory.

### Prerequisiti
1. Completare l'esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted].
2. Creare il file **hivequery.hql** in una sottocartella denominata **Hive** in **C:\ADFGetStarted** con il contenuto seguente.
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;

	> [AZURE.NOTE]Utilizzare il motore **Tez** per eseguire query Hive nel file HQL, aggiungere "**set hive.execution.engine=tez**;" all'inizio del file.
		
3.  Caricare **hivequery.hql** nel contenitore **adftutorial** dell'archivio BLOB


### Procedura dettagliata

#### Creazione della tabella di output
        
1. Nell'**editor di Data factory**, fare clic su **Nuovo set di dati**, quindi fare clic su **Archivio BLOB di Azure** nella barra dei comandi.
2. Sostituire lo script JSON nel riquadro a destra con il seguente script JSON:

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la tabella.


### Creazione di un servizio collegato per un cluster HDInsight
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Il cluster HDInsight su richiesta viene creato e gestito dal servizio Data factory di Azure per elaborare i dati. Per informazioni dettagliate sul servizio collegato HDInsight su richiesta, vedere [Servizio collegato per HDInsight su richiesta](https://msdn.microsoft.com/library/dn893526.aspx). Ai fini dell'esempio, viene usato un cluster su richiesta. Notare che la creazione su richiesta del cluster HDInsight richiede più di 15 minuti e verrà addebitato solo il tempo in cui tale cluster è attivo ed esegue processi.

#### Per usare un cluster HDInsight su richiesta
1. Fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi selezionare **Cluster HDInsight su richiesta** dal menu.
2. Nello script JSON procedere come segue: 
	1. Per la proprietà **clusterSize**, specificare le dimensioni del cluster HDInsight.
	2. Per la proprietà **jobsContainer**, specificare il nome del contenitore predefinito in cui verranno archiviati i log del cluster. Ai fini di questa esercitazione, specificare **adfjobscontainer**.
	3. Per la proprietà **timeToLive**, specificare il tempo di inattività consentito per il cluster prima dell'eliminazione. 
	4. Per la proprietà **version**, specificare la versione di HDInsight da utilizzare. Se si esclude questa proprietà, verrà usata la versione più recente.  
	5. Per **linkedServiceName**, specificare l'oggetto **StorageLinkedService** creato nell'esercitazione introduttiva. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.
   
   
#### Per usare il proprio cluster HDInsight: 

1. Fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi selezionare **Cluster HDInsight** dal menu.
2. Nello script JSON procedere come segue: 
	1. Per la proprietà **clusterUri**, immettere l'URL di HDInsight. Ad esempio: https://<clustername>.azurehdinsight.net/     
	2. Per la proprietà **UserName**, immettere il nome dell'utente che ha accesso al cluster HDInsight.
	3. Per la proprietà **Password**, immettere la password dell'utente. 
	4. Per la proprietà **LinkedServiceName**, immettere **StorageLinkedService**, ossia il servizio collegato creato nell'esercitazione introduttiva. 

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

### Creazione e pianificazione della pipeline
   
1. Fare clic su **Nuova pipeline** sulla barra dei comandi. Se non viene visualizzato il comando, fare clic su **... (puntini di sospensione)** per visualizzarlo. 
2. Sostituire lo script JSON nel riquadro a destra con lo script JSON seguente. Per usare il proprio cluster dopo aver seguito i passaggi per creare il servizio collegato **HDInsightLinkedService**, sostituire **HDInsightOnDemandLinkedService** con **HDInsightLinkedService** nel codice JSON seguente. 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]Sostituire il valore **StartDateTime** con la data di tre giorni fa e il valore **EndDateTime** con il giorno corrente. StartDateTime ed EndDateTime devono essere in [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. La tabella di output viene pianificata per essere generata tutti i giorni, quindi saranno generate tre sezioni.
	> 
	> Sostituire **l'account di archiviazione** nel codice JSON con il nome dell'account di archiviazione usato.
	
	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.
4. Vedere la sezione [Monitorare i set di dati e della pipeline][adfgetstartedmonitoring] nell'articolo [Introduzione a Data factory di Azure][adfgetstarted]. 

	> [AZURE.NOTE]Nel pannello **DETTAGLI ESECUZIONE ATTIVITÀ** per una sezione di una tabella di output (selezionare la tabella di output -> una sezione -> un'esecuzione attività nel portale), verranno visualizzati i collegamenti ai log creati dal cluster HDInsight. È possibile esaminarli nel portale stesso o scaricarli nel computer.
  

## Esempio JSON con Pig
Quando si definisce un'attività Pig o Hive in una pipeline di JSON, la proprietà **type** deve essere impostata su: **HDInsightActivity**.

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Tenere presente quanto segue:**
	
- L'oggetto **type** dell'attività è impostato su **HDInsightActivity**.
- L'oggetto **linkedServiceName** è impostato su **MyHDInsightLinkedService**. Per informazioni dettagliate sulla creazione di un servizio collegato di HDInsight, vedere la sezione corrispondente più avanti.
- L'oggetto **type** di **transformation** è impostato su **Pig**.
- È possibile specificare uno script Pig inline per la proprietà **script** o archiviare i file di script in un archivio BLOB di Azure e fare riferimento al file mediante la proprietà **scriptPath**, descritta più avanti in questo articolo. 
- I parametri per lo script Pig vengono specificati mediante **extendedProperties**. Altri dettagli vengono forniti più avanti in questo articolo. 


## Esempio JSON con Hive


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**Tenere presente quanto segue:**
	
- L'oggetto **type** dell'attività è impostato su **HDInsightActivity**.
- L'oggetto **linkedServiceName** è impostato su **MyHDInsightLinkedService**. 
- L'oggetto **type** di **transformation** è impostato su **Hive**.
- È possibile specificare uno script Hive inline per la proprietà **script** o archiviare i file di script in un archivio BLOB di Azure e fare riferimento al file mediante la proprietà **scriptPath**, descritta più avanti in questo articolo. 
- È possibile specificare i parametri dello script Hive mediante **extendedProperties**. Altri dettagli vengono forniti più avanti in questo articolo. 

> [AZURE.NOTE]Vedere la [Guida di riferimento per gli sviluppatori](http://go.microsoft.com/fwlink/?LinkId=516908) per informazioni dettagliate su cmdlet, schemi JSON e proprietà nello schema.


## Uso di script Pig e Hive nell'Attività di HDInsight
È possibile archiviare gli script Pig/Hive in un'archiviazione BLOB di Azure associata al cluster HDInsight e fare riferimento a tali script dalle attività Pig/Hive usando le seguenti proprietà nel JSON:

* **scriptPath** - Percorso del file di script Pig o Hive
* **scriptLinkedService** – Account di archiviazione di Azure contenente il file di script

Il seguente esempio JSON per una pipeline di esempio usa un'attività Hive che fa riferimento al file **transformdata.hql** archiviato nella cartella **script** nel contenitore **adfwalkthrough** nell'archivio BLOB di Azure rappresentato da **StorageLinkedService**.

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
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


> [AZURE.NOTE]Per usare il motore **Tez** per eseguire una query Hive, eseguire "**set hive.execution.engine=tez**;" prima di eseguire la query Hive.
> 
> Vedere la [Guida di riferimento per gli sviluppatori](http://go.microsoft.com/fwlink/?LinkId=516908) per informazioni dettagliate su cmdlet, schemi JSON e proprietà nello schema.

## Query con parametri Pig e Hive
Le attività Pig e Hive di Data factory consentono di specificare i valori dei parametri utilizzati negli script Pig e Hive mediante **extendedProperties**. La sezione extendedProperties è formata dal nome e dal valore del parametro.

Vedere il seguente esempio per specificare i parametri per uno script Hive mediante **extendedProperties**. Per usare gli script con parametri Hive, eseguire le operazioni seguenti:

1.	Definire i parametri in **extendedProperties**.
2.	Nello script Hive inline (o) nel file di script Hive archiviato nell'archivio BLOB, fare riferimento al parametro mediante **${hiveconf:parameterName}**.

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


## Vedere anche

Articolo | Descrizione
------ | ---------------
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | In questo articolo viene illustrata una procedura dettagliata end-to-end in cui viene spiegato come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log in informazioni accurate.
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete su cmdlet, script JSON, funzioni e così via. 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=62-->