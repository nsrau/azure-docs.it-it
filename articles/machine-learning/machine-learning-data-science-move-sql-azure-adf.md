<properties
	pageTitle="Spostare i dati da un Server SQL locale a SQL Azure con data factory di Azure | Azure"
	description="È necessario impostare una pipeline ADF che comprende due attività di migrazione di dati che insieme spostano i dati su base giornaliera tra un database locale e un database nel cloud."
	services="machine-learning"
	documentationCenter=""
	authors="fashah"
	manager="jacob.spoelstra"
	editor=""
	videoId=""
	scriptId="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="fashah;bradsev" />


# Spostare i dati da un server SQL locale a SQL Azure con il Data factory di Azure

Questo argomento descrive come spostare i dati da un Database di SQL Server locale a un Database di SQL Azure tramite l'archiviazione BLOB di Azure tramite il Data factory di Azure (ADF).

## <a name="intro"></a>Introduzione: che cos'è l’ADF e quando deve essere utilizzato per la migrazione dei dati?

Il Data factory è un servizio di integrazione delle informazioni basato sul cloud che permette di automatizzare lo spostamento e la trasformazione dei dati. Il concetto chiave nel modello ADF è pipeline. Una pipeline è un raggruppamento logico di attività, ognuna delle quali definisce le azioni da eseguire sui dati contenuti nel set di dati. I servizi collegati vengono utilizzati per definire le informazioni necessarie affinché il servizio Data factory si connetta a risorse dati esterne.

Con ADF, i servizi di elaborazione dei dati esistenti possono essere composti in pipeline di dati, altamente disponibili e gestiti nel cloud. Queste pipeline di dati possono essere pianificate per inserire, preparare, trasformare, analizzare e pubblicare i dati e l’ADF gestirà e organizzerà tutti i dati complessi e le dipendenze di elaborazione. Le soluzioni possono essere compilate e distribuite nel cloud, collegando un numero crescente di origini dati locali e cloud.

È consigliabile utilizzare ADF quando i dati devono essere continuamente migrati in uno scenario ibrido che accede a risorse locali e cloud, nonché quando i dati vengono trasferiti, devono essere modificati o presentano logiche di business aggiunte nel corso della migrazione. L’ADF consente la pianificazione e il monitoraggio dei processi utilizzando semplici script JSON che gestiscono lo spostamento dei dati su base periodica. L’ADF dispone anche di altre funzionalità quali il supporto per operazioni complesse. Per ulteriori informazioni sul file ADF, vedere la documentazione di [Data factory di Azure (ADF)](http://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scenario

È necessario impostare una pipeline ADF che comprende due attività di migrazione di dati che insieme spostano i dati su base giornaliera tra un database SQL locale e un database di SQL Azure nel cloud. Le due attività sono:

* Copiare dati da un database di SQL Server locale su un BLOB Azure;
* Copiare i dati dall'account di archiviazione BLOB di Azure a un Database di SQL Azure.

**Riferimenti**: i passaggi illustrati di seguito sono stati adattati dal tutorial specifico [Consentire alle pipeline di usare dati locali](data-factory-use-onpremises-datasources.md) forniti dal team ADF e i riferimenti nelle sezioni pertinenti all'argomento sono disponibili quando necessario.


## <a name="prereqs"></a>Prerequisiti
Il tutorial presuppone:

* Un **sottoscrizione Azure**. Se non si dispone di una sottoscrizione, è possibile iscriversi per provare la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un **account di archiviazione Azure**. In questa esercitazione si userà un account di archiviazione di Azure per archiviare i dati. Se non si dispone di un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account). Dopo avere creato l'account di archiviazione, sarà necessario ottenere la chiave dell'account usata per accedere alla risorsa di archiviazione. Vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accedere a un **database SQL di Azure**. Se è necessario impostare un database di SQL Azure, la [Guida introduttiva al database SQL di Microsoft Azure](sql-database-get-started.md) fornisce informazioni su come eseguire il provisioning di una nuova istanza di un database di SQL Azure.
* Installazione e configurazione di **Azure PowerShell** in locale. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

> [AZURE.NOTE]In questa procedura viene utilizzato il [portale di anteprima di Azure](https://ms.portal.azure.com/).

##<a name="upload-data"></a> Caricare i dati sul SQL Server locale

Utilizziamo il [set di dati NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/) per illustrare il processo di migrazione. Il set di dati NYC Taxi è disponibile, come indicato nel post, sull'archiviazione BLOB di Azure [qui](http://www.andresmh.com/nyctaxitrips/). I dati dispongono di due file, il file trip\_data.csv che contiene i dettagli relativi alle corse e il file trip\_far.csv che contiene i dettagli della tariffa pagata per ogni corsa. Un esempio e una descrizione di questi file sono inclusi in [Descrizione del set di dati relativo alle corse dei taxi di New York](machine-learning-data-science-process-sql-walkthrough.md#dataset).


È possibile adattare le procedure riportate di seguito a un set di dati personalizzati o seguire i passaggi come descritto utilizzando il set di dati NYC Taxi. Per caricare il set di dati NYC Taxi nel database di SQL Server locale, attenersi alla procedura descritta in [Importazione in blocco dei dati nel database SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Queste istruzioni sono per un Server SQL in una macchina virtuale di Azure, ma la procedura per il caricamento nel Server SQL locale è la stessa.

##<a name="create-adf"></a> Creare un data factory di Azure

Le istruzioni per la creazione di una nuova data factory di Azure e un gruppo di risorse nel [portale di anteprima di Azure](https://ms.portal.azure.com/) sono disponibili [qui](data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Denominare la nuova istanza ADF *adfdsp*e assegnare il nome *adfdsprg* al gruppo di risorse creato.

## Installare e configurare i Gateway di gestione dati

Per abilitare la pipeline in una data factory di Azure per il funzionamento con un Server SQL locale, è necessario aggiungere la data factory come servizio collegato. Per creare un servizio collegato per SQL Server locale, è innanzitutto necessario scaricare e installare il Gateway di gestione dati di Microsoft nel computer locale e configurare il servizio collegato per l'origine dati locale per utilizzare il gateway. Gateway di gestione dati serializza e deserializza i dati di origine e sink sul computer in cui è ospitato.  


Per le istruzioni di configurazione e i dettagli sul Gateway di gestione dati, vedere [Consentire alle pipeline di usare dati locali](data-factory-use-onpremises-datasources.md)


## <a name="adflinkedservices"></a>Creare servizi collegati per connettersi alle risorse di dati

I servizi collegati definiscono le informazioni necessarie affinché il servizio data factory si connetta a risorse dati. In [Creazione di servizi collegati](data-factory-use-onpremises-datasources.md#step-2-create-linked-services) viene fornita la procedura dettagliata per la creazione di servizi collegati.

Sono disponibili tre risorse in questo scenario per il quale sono necessari servizi collegati.

1. [Servizio collegato per SQL Server locale](#adf-linked-service-onprem-sql)
2. [Servizio collegato per archiviazione BLOB di Azure](#adf-linked-service-blob-store)
3. [Servizio collegato per il database SQL Azure](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Servizio collegato per il database SQL Server locale
Per creare il servizio collegato per SQL Server locale, fare clic su **Archivio dati** nella pagina di destinazione ADF del portale Azure, selezionare*SQL* e immettere le credenziali per il *nome utente* e la *password* del SQL Server locale. È necessario immettere il nome del server nella forma **nome dell'istanza nomeserver completa barra rovesciata (nomeserver\\nomeistanza)**. Nome servizio collegato *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Servizi collegati per BLOB
Per creare il servizio collegato per l'account di archiviazione BLOB di Azure, fare clic su **Archivio dati** nella pagina di destinazione ADF del portale di Azure, selezionare *Account di archiviazione Azure* e immettere la chiave dell’account e il nome del contenitore di archiviazione BLOB di Azure. Assegnare il nome *adfds* al servizio di collegamento.

###<a name="adf-linked-service-azure-sql"></a>Servizio collegato per il database SQL Azure
Per creare il servizio collegato per database SQL Azure, fare clic su **Archivio dati** nella pagina di destinazione ADF del portale Azure, selezionare*SQL* e immettere le credenziali per il *nome utente* e la *password* del database SQL Azure. Il *nome utente* deve essere specificato come **user@servername*.


##<a name="adf-tables"></a>Definire e creare tabelle per specificare la modalità di accesso al set di dati

Creare tabelle che specificano la struttura, la posizione e la disponibilità dei set di dati con le seguenti procedure basate su script. I file JSON vengono utilizzati per definire le tabelle. Per ulteriori informazioni sulla struttura di questi file, vedere[Set di dati](data-factory-create-datasets.md).

> [AZURE.NOTE]È necessario eseguire il cmdlet `Add-AzureAccount` prima di eseguire il cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) per verificare che sia selezionata la sottoscrizione di Azure giusta per l'esecuzione del comando. Per la documentazione di questo cmdlet, vedere [Add-AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

Le definizioni basate su JSON nelle tabelle utilizzano i nomi seguenti:

* il **nome tabella** nel Server SQL locale è*nyctaxi\_data*
* il **nome del contenitore** nell'account di archiviazione Blob di Azure è *nomecontenitore*  

Per questa pipeline ADF sono necessarie tre definizioni di tabella:

1. [Tabella SQL locale](#adf-table-onprem-sql)
2. [Tabella BLOB](#adf-table-blob-store)
3. [Tabella SQL Azure](#adf-table-azure-sql)

> [AZURE.NOTE]Le procedure seguenti utilizzano Azure PowerShell per definire e creare le attività del file ADF. Tuttavia, queste attività possono inoltre essere eseguite tramite il portale di anteprima Azure. Per ulteriori informazioni, vedere[Creazione di set di dati di input e di output](data-factory-use-onpremises-datasources.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Tabella SQL locale

La definizione della tabella di SQL Server locale viene specificata nel file JSON seguente.

    	{
	    	"name": "OnPremSQLTable",
	    	"properties":
	    	{
		    	"location":
		    	{
		    	"type": "OnPremisesSqlServerTableLocation",
		    	"tableName": "nyctaxi_data",
		    	"linkedServiceName": "adfonpremsql"
		    	},
		    	"availability":
		    	{
		    	"frequency": "Day",
		    	"interval": 1,   
		    	"waitOnExternal":
		    	{
		    	"retryInterval": "00:01:00",
		    	"retryTimeout": "00:10:00",
		    	"maximumRetry": 3
		    	}

		    	}
	    	}
    	}
I nomi delle colonne non sono stati inclusi qui. Sottoselezionare i nomi delle colonne includendoli qui (per ulteriori informazioni controllare la [documentazione ADF](data-factory-copy-activity.md)).

Copiare la definizione JSON della tabella in un file denominata *onpremtabledef.json* e salvarlo in una posizione nota (generalmente *C:\\temp\\onpremtabledef.json*). Creare la tabella nel file ADF con il seguente cmdlet PowerShell di Azure.

	New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Tabella BLOB
Definizione della tabella per la posizione di output del BLOB è il seguente (questa mappa associa i dati acquisiti da locale con il BLOB Azure):

	    {
		    "name": "OutputBlobTable",
		    "properties":
		    {
			    "location":
			    {
			    "type": "AzureBlobLocation",
			    "folderPath": "containername",
			    "format":
			    {
			    "type": "TextFormat",
			    "columnDelimiter": "\t"
			    },
			    "linkedServiceName": "adfds"
			    },
			    "availability":
			    {
			    "frequency": "Day",
			    "interval": 1
			    }
		    }
	    }

Copiare la definizione JSON della tabella in un file denominata *bloboutputtabledef.json* e salvarlo in una posizione nota (generalmente *C:\\temp\\bloboutputtabledef.json*). Creare la tabella nel file ADF con il seguente cmdlet PowerShell di Azure.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>Tabella SQL Azure
La definizione della tabella per l’output SQL Azure è la seguente (questo schema associa i dati provenienti dal blob):

	{
	    "name": "OutputSQLAzureTable",
	    "properties":
	    {
	        "structure":
	        [
				{ "name": "column1", type": "String"},
				{ "name": "column2", type": "String"}                
	        ],
	        "location":
	        {
	            "type": "AzureSqlTableLocation",
	            "tableName": "your_db_name",
	            "linkedServiceName": "adfdssqlazure_linked_servicename"
	        },
	        "availability":
	        {
	            "frequency": "Day",
	            "interval": 1            
	        }
	    }
	}

Copiare la definizione JSON della tabella in un file denominata *AzureSqlTable.json* e salvarlo in una posizione nota (generalmente *C:\\temp\\AzureSqlTable.json*). Creare la tabella nel file ADF con il seguente cmdlet PowerShell di Azure.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  

##<a name="adf-pipeline"></a>Definire e creare la pipeline

Specificare le attività che appartengono alla pipeline e creare la pipeline con le seguenti procedure basate su script. Un file JSON viene utilizzato per definire le proprietà della pipeline.

* Lo script presuppone che il **nome della pipeline** sia *AMLDSProcessPipeline*.
* Si noti inoltre che abbiamo impostato la periodicità della pipeline per l’esecuzione su base giornaliera e utilizza il tempo di esecuzione predefinito per il processo (12 am UTC).

> [AZURE.NOTE]Le procedure seguenti utilizzano Azure PowerShell per definire e creare la pipeline del file ADF. Tuttavia, questa attività può inoltre essere eseguita tramite il portale di anteprima Azure. Per ulteriori informazioni, vedere [Creazione ed esecuzione di una pipeline](data-factory-use-onpremises-datasources.md#step-4-create-and-run-a-pipeline).

Se si utilizzano le definizioni di tabella sopra riportate, la definizione della pipeline per il file ADF viene specificata come segue:

		{
		    "name": "AMLDSProcessPipeline",
		    "properties":
		    {
		        "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
		         "activities":
		        [
		            {
		                "name": "CopyFromSQLtoBlob",
		                "description": "Copy data from on-premise SQL server to blob",     
		                "type": "CopyActivity",
		                "inputs": [ {"name": "OnPremSQLTable"} ],
		                "outputs": [ {"name": "OutputBlobTable"} ],
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "SqlSource",
		                        "sqlReaderQuery": "select * from nyctaxi_data"
		                    },
		                    "sink":
		                    {
		                        "type": "BlobSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 3,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       

		             },

					{
						"name": "CopyFromBlobtoSQLAzure",
						"description": "Push data to Sql Azure",		
						"type": "CopyActivity",
						"inputs": [ {"name": "OutputBlobTable"} ],
						"outputs": [ {"name": "OutputSQLAzureTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink",
								"WriteBatchTimeout": "00:5:00",				
							}			
						},
						"Policy":
						{
							"concurrency": 3,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 2,
							"timeout": "02:00:00"
						}
				     }
		        ]
		    }
		}

Copiare la definizione JSON della pipeline in un file denominato *pipelinedef.json* e salvarlo in una posizione nota (generalmente *C:\\temp\\pipelinedef.json*). Creare la pipeline ADF con il seguente cmdlet PowerShell di Azure.

	New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Accertarsi che sia possibile visualizzare la pipeline nel file ADF sul portale Azure come indicato di seguito (quando si fa clic sul diagramma)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)

##<a name="adf-pipeline-start"></a>Avviare la pipeline
La pipeline è avviabile con il comando seguente:

	Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

I valori del parametro *startdate* ed *enddate* devono essere sostituiti con le date effettive tra le quali si desidera eseguire la pipeline.

Una volta eseguita la pipeline, si dovrebbe poter visualizzare i dati visualizzati nel contenitore selezionato per il blob, un file al giorno.

Si noti che non abbiamo utilizzato la funzionalità fornita da ADF per dirigere i dati in modo incrementale. Per ulteriori informazioni su come eseguire questa e altre funzionalità fornite da ADF, vedere la [documentazione ADF](http://azure.microsoft.com/services/data-factory/).

<!---HONumber=Oct15_HO2-->