<properties title="Use Pig and Hive with Azure Data Factory" pageTitle="Usare Pig e Hive con Data factory di Azure" description="Informazioni su come elaborare i dati eseguendo script Pig e Hive in un cluster HDInsight di Azure da un'istanza di Data factory di Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Usare Pig e Hive con Data factory
Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue  una specifica operazione di elaborazione. Ad esempio, in un'attività di copia i dati vengono copiati da un'archiviazione di origine a un'archiviazione di destinazione, mentre un'attività HDInsight con trasformazioni Hive/Pig usa un cluster HDInsight di Azure per elaborare i dati usando gli script Hive/Pig. L'attività HDInsight può usare uno o più input e generare uno o più output. 

## Contenuto dell'articolo

Sezione | Descrizione
------- | -----------
[Esempio JSON con Pig](#PigJSON) | Questa sezione fornisce uno schema JSON per definire un'attività HDInsight che usa una trasformazione Pig. 
[Esempio JSON con Hive](#HiveJSON) | Questa sezione fornisce uno schema JSON per definire un'attività HDInsight che usa una trasformazione Hive. 
[Uso degli script Pig e Hive archiviati nell'archiviazione BLOB di Azure](#ScriptInBlob) | Descrive come fare riferimento agli script Pig/Hive archiviati in un'archiviazione BLOB di Azure da un'attività HDInsight mediante la trasformazione Pig/Hive.
[Query con parametri Pig e Hive](#ParameterizeQueries) | Descrive come specificare i valori per i parametri usati negli script Pig e Hive usando la proprietà **extendedProperties** in JSON.
[Procedura dettagliata: Usare Hive con Data factory di Azure](#Waltkthrough) | Fornisce istruzioni dettagliate per creare una pipeline che usa Hive per elaborare i dati.  



Quando si definisce un'attività Pig o Hive in un JSON della pipeline, la proprietà **type** deve essere impostata su: **HDInsightActivity**.

## <a name="PigJSON"></a> Esempio JSON con Pig

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
- **linkedServiceName** è impostato su **MyHDInsightLinkedService**. 
- L'oggetto **type** di **transformation** è impostato su **Pig**.
- È possibile specificare uno script Pig inline per la proprietà **script** o archiviare i file di script in un'archiviazione BLOB di Azure e fare riferimento al file mediante la proprietà **scriptPath**, descritta più avanti in questo articolo. 
- I parametri per lo script Pig vengono specificati mediante **extendedProperties**. Altri dettagli vengono forniti più avanti in questo articolo. 


## <a name="HiveJSON"></a> ## Esempio JSON con Hive


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
- **linkedServiceName** è impostato su **MyHDInsightLinkedService**. 
- L'oggetto **type** di **transformation** è impostato su **Hive**.
- È possibile specificare uno script Hive inline per la proprietà **script** o archiviare i file di script in un'archiviazione BLOB di Azure e fare riferimento al file mediante la proprietà **scriptPath**, descritta più avanti in questo articolo. 
- I parametri per lo script Hive vengono specificati mediante **extendedProperties**. Altri dettagli vengono forniti più avanti in questo articolo. 

> [WACOM.NOTE] Vedere la [guida di riferimento per gli sviluppatori](http://go.microsoft.com/fwlink/?LinkId=516908) per informazioni dettagliate su cmdlet, schemi JSON e proprietà nello schema. 


## <a name="ScriptInBlob"></a>Uso degli script Pig e Hive archiviati nell'archiviazione BLOB di Azure
È possibile archiviare gli script Pig/Hive in un'archiviazione BLOB di Azure associata al cluster HDInsight e fare riferimento a tali script dalle attività Pig/Hive usando le seguenti proprietà nel JSON: 

* **scriptPath** - Percorso del file di script Pig o Hive
* **scriptLinkedService** - Account di archiviazione di Azure contenente il file di script

Il seguente esempio JSON per una pipeline di esempio usa un'attività Hive che fa riferimento al file **transformdata.hql** archiviato nella cartella **scripts** nel contenitore **adfwalkthrough** nell'archiviazione BLOB di Azure rappresentata da **StorageLinkedService**.

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
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
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

  

> [WACOM.NOTE] Vedere la [guida di riferimento per gli sviluppatori](http://go.microsoft.com/fwlink/?LinkId=516908) per informazioni dettagliate su cmdlet, schemi JSON e proprietà nello schema.

## <a name="ParameterizeQueries"></a>Query con parametri Pig e Hive
Le attività Pig e Hive di Data factory consentono di specificare i valori dei parametri usati negli script Pig e Hive mediante **extendedProperties**. La sezione extendedProperties è formata dal nome e dal valore del parametro.

Vedere il seguente esempio per specificare i parametri per uno script Hive mediante **extendedProperties**. Per usare gli script con parametri Hive  , eseguire le operazioni seguenti:

1.	Definire i parametri in **extendedProperties**.
2.	Nello script Hive inline (o) nel file di script Hive archiviato nell'archiviazione BLOB, fare riferimento al parametro mediante **${hiveconf:parameterName}**.

   
    		
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


-  

## <a name="Walkthrough"></a>Procedura dettagliata: Usare Hive con Data factory di Azure
### Prerequisiti
1. Completare l'esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted].
2. Caricare il file **emp.txt** creato nell'esercitazione precedente come **hiveinput\emp.txt** nel contenitore adftutorial dell'archiviazione BLOB. La cartella **hiveinput** viene creata automaticamente nel contenitore **adftutorial** quando si carica il file emp.txt con questa sintassi. 
2. Creare il file **hivequery.hql** nella sottocartella denominata **Hive** in **C:\ADFGetStarted** con il seguente contenuto.
    		
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
		
3.  Caricare **hivequery.hql** nel contenitore **adftutorial** nell'archiviazione BLOB


### Procedura dettagliata

#### Creare la tabella di input
1. Creare un file JSON denominato **HiveInputBlobTable.json** nella cartella **C:\ADFGetStarted\Hive** con il seguente contenuto.
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
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
	
	- L'oggetto **type** di location è impostato su **AzureBlobLocation**.
	- **linkedServiceName** è impostato su **MyBlobStore**, che definisce un account di archiviazione di Azure.
	- **folderPath** specifica il contenitore/cartella BLOB per i dati di input. 
	- **frequency=Day** e **interval=1** indicano le sezioni disponibili quotidianamente
	- **waitOnExternal** indica che questi dati non sono generati da un'altra pipeline, ma esternamente rispetto all'istanza di Data factory. 
	

	Vedere la [guida di riferimento per gli sviluppatori di Data factory][developer-reference] per le descrizioni delle proprietà JSON.  

2. Avviare **Azure PowerShell** e passare in modalità **AzureResourceManager** se necessario.
    		
    	Switch-AzureMode AzureResourceManager

5. Passare alla cartella: **C:\ADFGetStarted\Hive**.
6. Eseguire il seguente comando per creare la tabella di input in **ADFTutorialDataFactory**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	Vedere [Riferimento ai cmdlet di Data factory][cmdlet-reference] per una panoramica dettagliata dei cmdlet di Data factory. 
#### Creare la tabella di output
        
1. Creare un file JSON denominato **HiveOutputBlobTable.json** con il seguente contenuto e salvarlo nella cartella **C:\ADFGetStarted\Hive**.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Eseguire il seguente comando per creare la tabella di output in **ADFTutorialDataFactory**.
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### Creare un servizio collegato per un cluster HDInsight
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, quando si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster. Ai fini dell'esempio, viene usato un cluster su richiesta. 

#### Per usare un cluster HDInsight su richiesta
1. Creare un file JSON denominato **HDInsightOnDemandCluster.json** con il seguente contenuto e salvarlo nella cartella **C:\ADFGetStarted\Hive**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Avviare **Azure PowerShell** ed eseguire il seguente comando per passare in modalità **AzureResourceManager**. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Passare alla cartella **C:\ADFGetstarted\Hive**.
4. Eseguire il seguente comando per creare il servizio collegato per il cluster HDInsight su richiesta.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. Vengono visualizzate le tabelle e i servizi collegati nel pannello **Data factory** del **portale di anteprima di Azure**.    
   
#### Per usare il proprio cluster HDInsight: 

1. Creare un file JSON denominato **MyHDInsightCluster.json** con il seguente contenuto e salvarlo nella cartella **C:\ADFGetStarted\Hive**. Sostituire clustername, username e password con i valori appropriati prima di salvare il file JSON.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Avviare **Azure PowerShell** ed eseguire il seguente comando per passare in modalità **AzureResourceManager**. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Passare alla cartella **C:\ADFGetstarted\Hive**.
4. Eseguire il seguente comando per creare il servizio collegato per il proprio cluster HDInsight.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### Creare e pianificare la pipeline
   
1. Creare un file JSON denominato **ADFTutorialHivePipeline.json** con il seguente contenuto e salvarlo nella cartella **C:\ADFGetStarted\Hive**. Per usare il proprio cluster dopo aver seguito i passaggi per creare il servizio collegato **MyHDInsightCluster**, sostituire **HDInsightOnDemandCluster** con **MyHDInsightCluster** nel seguente JSON. 


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
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
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

2. Eseguire il seguente comando per creare la pipeline.
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. Pianificare la pipeline.
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] Sostituire il valore **StartDateTime** con la data di tre giorni fa e il valore **EndDateTime** con il giorno corrente. StartDateTime ed EndDateTime sono orari UTC e devono essere in formato [ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. 
	> Se non si specifica **EndDateTime**, viene calcolato come "**StartDateTime + 48 ore**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come **EndDateTime**.
  	
	La tabella di output viene pianificata per essere generata tutti i giorni, quindi saranno generate tre sezioni. 

4. Vedere la sezione [Monitorare i set di dati e le pipeline][adfgetstartedmonitoring] dell'articolo [Introduzione a Data factory][adfgetstarted].   

## Vedere anche

Articolo | Descrizione
------ | ---------------
[Introduzione a Data factory di Azure][data-factory-introduction] | Questo articolo fornisce un'introduzione al servizio Data factory di Azure, ai concetti, al valore che offre e agli scenari che supporta.
[Introduzione a Data factory di Azure][adf-getstarted] | Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure.
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure.  
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 


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

[Guida di riferimento per gli sviluppatori]: http://go.microsoft.com/fwlink/?LinkId=516908
[Portale di Azure]: http://portal.azure.com
