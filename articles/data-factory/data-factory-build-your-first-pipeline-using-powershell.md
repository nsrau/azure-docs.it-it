<properties
	pageTitle="Creare la prima pipeline con Data factory di Azure"
	description="Questa esercitazione mostra come creare una pipeline di dati di esempio che trasforma i dati usando Azure HDInsight con Azure PowerShell"
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
	ms.topic="get-started-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Creare la prima pipeline di Data factory di Azure con Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Questo articolo descrive come usare Azure PowerShell per creare la prima pipeline. Questa esercitazione include i passaggi seguenti:

1.	Creazione della data factory
2.	Creazione dei servizi collegati (archivi dati, risorse di calcolo) e set di dati
3.	Creazione della pipeline

Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Per una panoramica dettagliata del servizio, vedere l'articolo [Introduzione al servizio Data factory di Azure](data-factory-introduction.md).

## Passaggio 1: Creazione della data factory

In questo passaggio è possibile usare Azure PowerShell per creare una data factory di Azure denominata **ADFTutorialDataFactoryPSH**.

1. Avviare **Azure PowerShell** ed eseguire i comandi seguenti. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	- Eseguire **Add-AzureAccount** e immettere il nome utente e la password usati per accedere al portale di anteprima di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di anteprima di Azure. 
2. Passare alla modalità **AzureResourceManager** perché i cmdlet di Data factory di Azure sono disponibili in questa modalità.

		Switch-AzureMode AzureResourceManager 
3. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo il comando seguente.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato **ADFTutorialResourceGroup**. Se si usa un gruppo di risorse diverso, sarà necessario usarlo al posto di ADFTutorialResourceGroup in questa esercitazione. 
4. Eseguire il cmdlet **New-AzureDataFactory** per creare una data factory denominata **DataFactoryMyFirstPipelinePSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"


	È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato un errore analogo a **Il nome "DataFactoryMyFirstPipelinePSH" per la data factory non è disponibile**, cambiare il nome, ad esempio, nomeutenteADFTutorialDataFactoryPSH. Durante l'esecuzione di passaggi in questa esercitazione usare questo nome anziché ADFTutorialFactoryPSH.

Nei passaggi successivi si apprenderà come creare i servizi collegati, i set di dati e la pipeline da usare in questa esercitazione.

## Passaggio 2: Creare servizi collegati e set di dati
In questo passaggio si procederà al collegamento dell'account di archiviazione di Azure e di un cluster HDInsight di Azure su richiesta alla data factory e quindi alla creazione di un set di dati per rappresentare i dati di output dell'elaborazione Hive.

### Creare il servizio collegato Archiviazione di Azure
1.	Creare un file JSON denominato **StorageLinkedService.json** in **C:\\ADFGetStartedPSH** con i contenuti seguenti. Creare la cartella ADFGetStartedPSH, se non esiste già.

		{
		    "name": "StorageLinkedService",
		    "properties": {
		        "type": "AzureStorage",
		        "description": "",
		        "typeProperties": {
		            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		        }
		    }
		}

	Sostituire **accountname** con il nome dell'account di archiviazione di Azure e **accountkey** con la chiave di accesso dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)

2.	In **Azure PowerShell** passare alla cartella **ADFGetStartedPSH**.
3.	È possibile usare il cmdlet **New-AzureDataFactoryLinkedService** per creare un servizio collegato. Questo cmdlet e altri cmdlet di Data factory usati in questa esercitazione richiedono il passaggio di valori per i parametri **ResourceGroupName** e **DataFactoryName**. In alternativa, è possibile usare **Get-AzureDataFactory** per ottenere un oggetto DataFactory e passare l'oggetto senza digitare ResourceGroupName e DataFactoryName ogni volta che si esegue un cmdlet. Eseguire il comando seguente per assegnare l'output del cmdlet **Get-AzureDataFactory** a una variabile **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	Eseguire quindi il cmdlet **New-AzureDataFactoryLinkedService** per creare il servizio collegato **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se il cmdlet **Get-AzureDataFactory** non è stato eseguito e l'output non è stato assegnato alla variabile **$df**, sarà necessario specificare i valori per i parametri ResourceGroupName e DataFactoryName, come indicato di seguito.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Se si chiude Azure PowerShell a metà esercitazione, sarà necessario eseguire il cmdlet Get-AzureDataFactory al successivo avvio di Azure PowerShell per completare l'esercitazione.

### Creare un servizio collegato Azure HDInsight
Si creerà ora un servizio collegato per il cluster HDInsight su richiesta che sarà usato per eseguire lo script Hive.

1.	Creare un file JSON denominato **HDInsightOnDemandLinkedService.json** in **C:\\ADFGetStartedPSH** con i contenuti seguenti. 


		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:05:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:
		
	Proprietà | Descrizione
	-------- | -----------
	Versione | Specifica che la versione di HDInsight creata è 3.1. 
	ClusterSize | Crea un cluster HDInsight con un nodo. 
	TimeToLive | Specifica il tempo di inattività del cluster HDInsight, prima che sia eliminato.
	JobsContainer | Specifica il nome del contenitore di processo che sarà creato per archiviare i log generati da HDInsight.
	linkedServiceName | Specifica l'account di archiviazione che sarà usato per archiviare i log generati da HDInsight.
2. Eseguire il cmdlet **New-AzureDataFactoryLinkedService** per creare il servizio collegato **HDInsightOnDemandLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### Creare il set di dati di output
Si creerà ora il set di dati di output per rappresentare i dati archiviati nell'archivio BLOB di Azure.

1.	Creare un file JSON denominato **OutputTable.json** nella cartella **C:\\ADFGetStartedPSH** con i contenuti seguenti.

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}
	Nel frammento di codice JSON si crea un set di dati denominato **AzureBlobOutput** e si specifica la struttura dei dati che saranno generati dallo script Hive. Si specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **data** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.

2. Eseguire il comando seguente in Azure PowerShell per creare la tabella di Data factory.

		New-AzureDataFactoryTable $df -File .\OutputTable.json

## Passaggio 3: Creazione della prima pipeline
In questo passaggio si creerà la prima pipeline.

1.	Creare un file JSON denominato **MyFirstPipelinePSH.json** nella cartella **C:\\ADFGetStartedPSH** con i contenuti seguenti: 

	> [AZURE.IMPORTANT]Nel codice JSON sostituire **storageaccountname** con il nome dell'account di archiviazione.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}
		
	Nel frammento di codice JSON si crea una pipeline costituita da una singola attività che usa Hive per elaborare i dati in un cluster HDInsight.
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure specificato da scriptLinkedService, denominato **StorageLinkedService**, e in un contenitore denominato **script**.

	La sezione **extendedProperties** è usata per specificare le impostazioni di runtime che verranno passate allo script Hive come valori di configurazione Hive (ad esempio, ${hiveconf:PartitionedData}).

	Le proprietà **start** e **end** della pipeline specificano il periodo attivo della pipeline.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata dal servizio collegato, **HDInsightOnDemandLinkedService**.
2. Eseguire il comando seguente per creare la tabella di Data factory. 
	
		New-AzureDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. La creazione della prima pipeline tramite Azure PowerShell è così completata.

### <a name="MonitorDataSetsAndPipeline"></a> Monitorare i set di dati e le pipeline
In questo passaggio viene usato Azure PowerShell per monitorare le attività in un'istanza di Data factory di Azure.

1.	Eseguire **Get-AzureDataFactory** e assegnare l'output a una variabile $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH
 
2.	Eseguire **Get-AzureDataFactorySlice** per ottenere dettagli su tutte le sezioni di **EmpSQLTable**, ovvero la tabella di output della pipeline.

		Get-AzureDataFactorySlice $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Si noti che il valore di StartDateTime specificato qui corrisponde all'ora di inizio specificata nel codice JSON della pipeline. L'output dovrebbe essere simile al seguente:

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0
	
3.	Eseguire **Get-AzureDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione **specifica**.

		Get-AzureDataFactoryRun $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	L'output dovrebbe essere simile al seguente:

		Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : DataFactoryMyFirstPipelinePSH
		TableName           : AzureBlobOutput
		ProcessingStartTime : 7/7/2015 1:14:18 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 1/1/2014 12:00:00 AM
		DataSliceEnd        : 2/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 7/7/2015 1:14:18 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	È possibile continuare a eseguire questo cmdlet fino a quando la sezione non passa allo stato Pronto o di operazione non riuscita. Quando lo stato sella sezione è Pronto, cercare i dati di output nella cartella partitioneddata del contenitore data nell'archivio BLOB. La creazione di un cluster HDInsight su richiesta di solito richiede tempo.

Vedere [Riferimento ai cmdlet di Data factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) per la documentazione completa sui cmdlet di Data factory.

 

## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati da un BLOB di Azure ad Azure SQL](./data-factory-get-started.md).
  

<!---HONumber=August15_HO6-->