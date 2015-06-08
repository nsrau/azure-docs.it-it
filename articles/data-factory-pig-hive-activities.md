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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Usare Pig e Hive con Data factory
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Questo articolo viene descritto l'utilizzo dell'attività HDInsight con Pig o Hive trasformazione in una pipeline di Factory di dati di Azure. Vedere [richiamare i programmi MapReduce dalla Factory dati][data-factory-map-reduce] per informazioni dettagliate sull'esecuzione di MapReduce programmi in un HDInsight cluster da una pipeline di factory di dati di Azure.

## Procedura dettagliata: Utilizzo di Hive con dati di Azure Factory
Questa procedura dettagliata vengono fornite istruzioni dettagliate per l'utilizzo di un'attività di HDInsight con Hive trasformazione in una pipeline di dati Factory.

### Prerequisiti
1. Completare l'esercitazione dal [iniziare a utilizzare Azure dati Factory][adfgetstarted] articolo.
2. Caricare **emp.txt** file creato nell'esercitazione precedente come **hiveinput\emp.txt** al contenitore adftutorial nell'archiviazione blob. Il **hiveinput** cartella viene creata automaticamente nel **adftutorial** contenitore quando si carica il file emp.txt con la seguente sintassi.

	> [AZURE.NOTE]Il file emp.txt è solo un file fittizio per questa procedura dettagliata. I dati di input effettivi provengono dal **hivesampletable** che esiste già nel cluster HDInsight. La pipeline non utilizza il file emp.txt.
	
2. Creare **hivequery.hql** file in una sottocartella denominata **Hive** in **C:\ADFGetStarted** con il contenuto seguente.
    		
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

	> [AZURE.NOTE]Utilizzare il **Tez** motore per eseguire query Hive nel file HQL, aggiungere "* * impostare hive.execution.engine=tez**;" nella parte superiore del file.
		
3.  Caricare il **hivequery.hql** per il **adftutorial** contenitore nell'archiviazione blob


### Procedura dettagliata

#### Creazione della tabella di input
1. Nel **dati FACTORY** blade per il **ADFTutorialDataFactory**, fare clic su **autore e distribuire** per avviare l'Editor dei dati di Factory.
	
	![Dati Factory Blade][data-factory-blade]

2. Nel **editor dati Factory**, fare clic su **nuovo set di dati**, quindi fare clic su **archiviazione Blob di Azure** dalla barra dei comandi.
3. Sostituire lo script JSON nel riquadro di destra con il seguente script JSON:    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**Tenere presente quanto segue:**
	
	- percorso **tipo** è impostato su **AzureBlobLocation**.
	- **linkedServiceName** è impostato su **StorageLinkedService** che definisce un account di archiviazione Azure.
	- **folderPath** specifica container\folder il blob per i dati di input. 
	- **frequenza = giorno** e **intervallo = 1** significa che le sezioni sono disponibili ogni giorno
	- **waitOnExternal** significa che questi dati non viene generati dalla pipeline di un altro, viene invece generato esternamente della factory di dati. 
	

	Vedere [dati di riferimento per sviluppatori Factory][developer-reference] per le descrizioni delle proprietà JSON.

2. Fare clic su **Distribuisci** sulla barra dei comandi per la distribuzione della tabella.
  
#### Creazione della tabella di output
        
1. Nel **editor dati Factory**, fare clic su **nuovo set di dati**, quindi fare clic su **archiviazione Blob di Azure** dalla barra dei comandi.
2. Sostituire lo script JSON nel riquadro di destra con il seguente script JSON:

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

2. Fare clic su **Distribuisci** sulla barra dei comandi per la distribuzione della tabella.


### Creazione di un servizio collegato per un cluster HDInsight
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, quando si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster. Ai fini dell'esempio, viene usato un cluster su richiesta.

#### Per usare un cluster HDInsight su richiesta
1. Fare clic su **nuovo calcolo** dalla barra dei comandi e selezionare **cluster HDInsight su richiesta** dal menu.
2. Eseguire le operazioni seguenti nello script JSON: 
	1. Per il **clusterSize** proprietà, specificare le dimensioni del cluster HDInsight.
	2. Per il **jobsContainer** proprietà, specificare il nome del contenitore predefinito in cui verranno archiviati i registri cluster. Ai fini di questa esercitazione, specificare **adfjobscontainer**.
	3. Per il **timeToLive** proprietà, specificare quanto tempo il cluster può rimanere inattivo prima che venga eliminato. 
	4. Per il **versione** proprietà, specificare la versione di HDInsight si desidera utilizzare. Se si esclude questa proprietà, viene utilizzata la versione più recente.  
	5. Per il **linkedServiceName**, specificare **StorageLinkedService** era stata creata in Get esercitazione introduttiva. 

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

1. Fare clic su **nuovo calcolo** dalla barra dei comandi e selezionare **cluster HDInsight** dal menu.
2. Eseguire le operazioni seguenti nello script JSON: 
	1. Per il **clusterUri** proprietà, immettere l'URL per il HDInsight. Ad esempio: https://<clustername>.azurehdinsight.net/     
	2. Per il **nome utente** proprietà, immettere il nome utente che ha accesso al cluster HDInsight.
	3. Per il **Password** proprietà, immettere la password dell'utente. 
	4. Per il **LinkedServiceName** proprietà, immettere **StorageLinkedService**. Si tratta del servizio collegato che era stata creata nell'esercitazione introduttiva Get. 

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

### Creazione e pianificazione della pipeline
   
1. Fare clic su **nuova pipeline** sulla barra dei comandi. Se non viene visualizzato il comando, fare clic su **... (Puntini di sospensione)** Per visualizzarla. 
2. Sostituire il JSON nel riquadro di destra con il seguente script JSON. Se si desidera utilizzare il proprio cluster e seguiti i passaggi per creare il **HDInsightLinkedService** collegato del servizio, sostituire **HDInsightOnDemandLinkedService** con **HDInsightLinkedService** in JSON seguente. 


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
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightLinkedService",
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

	> [AZURE.NOTE]Sostituire **valore StartDateTime** valore con tre giorni prima della data odierna e **EndDateTime** valore con il giorno corrente. Il valore StartDateTime ed EndDateTime devono trovarsi in [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. La tabella di output viene pianificata per essere generata tutti i giorni, quindi saranno generate tre sezioni.
	
	> [AZURE.NOTE]Sostituire **l'account di archiviazione** in JSON con il nome dell'account di archiviazione.
	
	Vedere [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) per informazioni dettagliate sulle proprietà JSON.
2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.
4. Vedere [monitorare i set di dati e della pipeline][adfgetstartedmonitoring] sezione di [Introduzione a Data Factory][adfgetstarted] articolo. 

	> [AZURE.NOTE]Nel **dettagli di esecuzione di attività** blade per una sezione di una tabella di output (selezionare la tabella di output -> selezionare slice -> selezionare un'attività eseguita nel portale), si vedrà collegamenti per i log creati dal cluster HDInsight. È possibile visualizzarle nel portale stesso o eseguirne il download nel computer.
  

## Esempio JSON con Pig
Quando si definisce un'attività Pig o Hive in una pipeline di JSON, il **tipo** proprietà deve essere impostata su: **HDInsightActivity**.

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
	
- Attività **tipo** è impostato su **HDInsightActivity**.
- **linkedServiceName** è impostato su **MyHDInsightLinkedService**. Vedere la sezione di servizio HDInsight collegati sotto per ulteriori informazioni sulla creazione di un servizio HDInsight collegato.
- Il **tipo** del **trasformazione** è impostato su **Pig**.
- È possibile specificare Pig script inline per il **script** proprietà o l'archivio file di script in un Azure nell'archiviazione blob e fare riferimento al file utilizzando **scriptPath** proprietà, illustrato più avanti in questo articolo. 
- È possibile specificare parametri per lo script Pig utilizzando il **extendedProperties**. Altri dettagli vengono forniti più avanti in questo articolo. 


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
	
- Attività **tipo** è impostato su **HDInsightActivity**.
- **linkedServiceName** è impostato su **MyHDInsightLinkedService**. 
- Il **tipo** del **trasformazione** è impostato su **Hive**.
- È possibile specificare Hive script inline per il **script** proprietà o l'archivio file di script in un Azure nell'archiviazione blob e fare riferimento al file utilizzando **scriptPath** proprietà, illustrato più avanti in questo articolo. 
- È possibile specificare parametri dello script Hive utilizzando il **extendedProperties**. Altri dettagli vengono forniti più avanti in questo articolo. 

> [AZURE.NOTE]Vedere [di riferimento per sviluppatori](http://go.microsoft.com/fwlink/?LinkId=516908) per ulteriori informazioni sui cmdlet, gli schemi JSON e proprietà nello schema.


## Utilizzo di script Pig e Hive in HDInsight attività
È possibile archiviare gli script Pig/Hive in un'archiviazione BLOB di Azure associata al cluster HDInsight e fare riferimento a tali script dalle attività Pig/Hive usando le seguenti proprietà nel JSON:

* **scriptPath** : percorso del file di script Pig o Hive
* **scriptLinkedService** -account di archiviazione di Azure che contiene il file di script

Nell'esempio seguente JSON per una pipeline di esempio utilizza un'attività di Hive che fa riferimento a **transformdata.hql** file archiviato nel **script** cartella il **adfwalkthrough** contenitore nell'archiviazione blob di Azure rappresentato dal **StorageLinkedService**.

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


> [AZURE.NOTE]Utilizzare il **Tez** motore per eseguire una query Hive, eseguire "* * impostare hive.execution.engine=tez**;" prima di eseguire query Hive.
> 
> Vedere [di riferimento per sviluppatori](http://go.microsoft.com/fwlink/?LinkId=516908) per ulteriori informazioni sui cmdlet, gli schemi JSON e proprietà nello schema.

## Query con parametri Pig e Hive
Le attività dati Factory Pig e Hive consentono di specificare i valori per parametri utilizzati negli script Pig e Hive utilizzando **extendedProperties**. La sezione extendedProperties è formata dal nome e dal valore del parametro.

Vedere l'esempio riportato di seguito per specificare i parametri per un utilizzo di script Hive **extendedProperties**. Per usare gli script con parametri Hive, eseguire le operazioni seguenti:

1.	Definire i parametri in **extendedProperties**.
2.	Nel script Hive in linea (o) i file di script Hive memorizzati nell'archiviazione blog, fare riferimento al parametro utilizzando **${hiveconf:parameterName}**.

   
    		
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
[Esercitazione: Spostare ed elaborare i file di log utilizzando la Factory di dati][adf-tutorial] | Questo articolo viene fornita una procedura dettagliata end-to-end in cui viene illustrato come implementare una reale quasi utilizza Azure dati Factory per trasformare i dati dai file di log in approfondimenti scenario del mondo.
[Riferimenti per sviluppatori Factory di dati di Azure][developer-reference] | Il riferimento per sviluppatori presenta il contenuto di riferimento completo per i cmdlet, script JSON, funzioni e così via... 

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

<!---HONumber=GIT-SubDir-->