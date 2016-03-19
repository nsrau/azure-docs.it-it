<properties
	pageTitle="Creare la prima data factory (PowerShell) | Microsoft Azure"
	description="In questa esercitazione si creerà una pipeline di esempio di Data factory di Azure usando Azure PowerShell."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"
/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/03/2016"
	ms.author="spelluru"/>

# Creare la prima data factory di Azure con Azure PowerShell
> [AZURE.SELECTOR]
- [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md)
- [Con l'editor di Data factory](data-factory-build-your-first-pipeline-using-editor.md)
- [Tramite PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Con Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Con il modello di Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)


Questo articolo descrive come usare Azure PowerShell per creare la prima data factory di Azure.


## Prerequisiti
Oltre ai prerequisiti elencati nell'argomento di panoramica dell'esercitazione, sarà necessario installare quanto segue:

- Prima di procedere è **necessario** leggere l'articolo [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) ed eseguire i passaggi richiesti.
- **Azure PowerShell**. Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer.
- (facoltativo) Questo articolo non illustra tutti i cmdlet di Data factory. Vedere [Riferimento ai cmdlet di Data factory](https://msdn.microsoft.com/library/dn820234.aspx) per la documentazione completa sui cmdlet di Data factory. 

Se si usa una **versione precedente alla 1.0** di Azure PowerShell, sarà necessario usare i cmdlet documentati [qui][cmdlet-reference]. Sarà anche necessario eseguire i comandi seguenti prima di usare i cmdlet di Data factory:
 
1. Aprire Azure PowerShell ed eseguire i comandi seguenti. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	1. Eseguire **Add-AzureAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.
	2. Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	3. Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
4. Passare alla modalità AzureResourceManager perché i cmdlet di Data factory di Azure sono disponibili in questa modalità: **Switch-AzureMode AzureResourceManager**.

Per informazioni dettagliate, vedere la pagina relativa alla [deprecazione di Switch-AzureMode in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


## Passaggio 1: Creare la data factory

In questo passaggio è possibile usare Azure PowerShell per creare una data factory di Azure denominata **FirstDataFactoryPSH**. Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, attività di copia per copiare dati da un'origine a un archivio dati di destinazione e attività Hive di HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. In questo passaggio iniziale viene creata la data factory.

1. Aprire Azure PowerShell ed eseguire il comando seguente. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	- Eseguire **Login-AzureRmAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription <Name of the subscription>** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
3. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo il comando seguente.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato ADFTutorialResourceGroup. Se si usa un gruppo di risorse diverso, sarà necessario usarlo al posto di ADFTutorialResourceGroup in questa esercitazione.
4. Eseguire il cmdlet **New-AzureRmDataFactory** per creare una data factory denominata **FirstDataFactoryPSH**.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT] È necessario specificare un nome univoco globale per la Data factory di Azure. Se viene visualizzato l'errore **Il nome "FirstDataFactoryPSH" per la data factory non è disponibile**, modificare il nome (ad esempio, nomeutenteFirstDataFactoryPSH). Durante l'esecuzione di passaggi in questa esercitazione usare questo nome anziché ADFTutorialFactoryPSH. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md).
	> 
	> Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.

Prima di creare una pipeline è necessario creare alcune entità di Data factory. Creare prima di tutto i servizi collegati per collegare archivi dati/servizi di calcolo all'archivio dati, definire i set di dati di input e di output per rappresentare i dati negli archivi dati collegati e quindi creare la pipeline con un'attività che usa questi set di dati.

## Passaggio 2: Creare servizi collegati 
In questo passaggio l'account di archiviazione di Azure e un cluster HDInsight su richiesta di Azure vengono collegati alla data factory. In questo esempio l'account di archiviazione di Azure contiene i dati di input e di output per la pipeline. In questo esempio il servizio collegato HDInsight viene usato per eseguire lo script Hive specificato nell'attività della pipeline. È necessario identificare l'archivio dati/i servizi di calcolo usati nello scenario e collegare tali servizi alla data factory creando servizi collegati.

### Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. Ai fini dell'esercitazione, usare lo stesso account di archiviazione di Azure per archiviare i dati di input/output e il file script HQL.

1. Creare un file JSON denominato StorageLinkedService.json nella cartella C:\\ADFGetStarted con il contenuto seguente: Creare la cartella ADFGetStarted, se non esiste già.

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

	Sostituire **account name** con il nome dell'account di archiviazione di Azure e **account key** con la chiave di accesso dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere la sezione [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2. In Azure PowerShell passare alla cartella ADFGetStarted.
3. È possibile usare il cmdlet **New-AzureRmDataFactoryLinkedService** per creare un servizio collegato. Questo cmdlet e altri cmdlet di Data factory usati in questa esercitazione richiedono il passaggio di valori per i parametri *ResourceGroupName* e *DataFactoryName*. In alternativa, è possibile usare **Get-AzureRmDataFactory** per ottenere un oggetto **DataFactory** e passarlo senza digitare *ResourceGroupName* e *DataFactoryName* ogni volta che si esegue un cmdlet. Eseguire il comando seguente per assegnare l'output del cmdlet **Get-AzureRmDataFactory** a una variabile **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Eseguire quindi il cmdlet **New-AzureRmDataFactoryLinkedService** per creare il servizio collegato **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se il cmdlet **Get-AzureRmDataFactory** non è stato eseguito e l'output non è stato assegnato alla variabile **$df**, sarà necessario specificare i valori per i parametri *ResourceGroupName* e *DataFactoryName* come indicato di seguito.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	Se si chiude Azure PowerShell durante l'esercitazione, per completarla sarà necessario eseguire il cmdlet **Get-AzureRmDataFactory** al successivo avvio di Azure PowerShell.

### Creare un servizio collegato Azure HDInsight
In questo passaggio viene collegato un cluster HDInsight su richiesta alla data factory. Il cluster HDInsight viene creato automaticamente in fase di esecuzione ed eliminato al termine dell'elaborazione, se rimane inattivo per il periodo di tempo specificato. È possibile usare il proprio cluster HDInsight anziché un cluster HDInsight su richiesta. Per informazioni dettagliate, vedere [Servizi collegati di calcolo](data-factory-compute-linked-services.md).

1. Creare un file JSON denominato **HDInsightOnDemandLinkedService.json** nella cartella **C:\\ADFGetStarted** con il contenuto seguente:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

	| Proprietà | Descrizione |
	| :------- | :---------- |
	| Versione | Specifica che la versione di HDInsight creata è 3.2. | 
	| ClusterSize | Crea un cluster HDInsight con un nodo. | 
	| TimeToLive | Specifica il tempo di inattività del cluster HDInsight, prima che sia eliminato. |
	| linkedServiceName | Specifica l'account di archiviazione che sarà usato per archiviare i log generati da HDInsight. |

	Tenere presente quanto segue:
	
	- Data Factory crea automaticamente un cluster HDInsight **basato su Windows** con il codice JSON precedente. È anche possibile creare automaticamente un cluster HDInsight **basato su Linux**. Per informazioni dettagliate, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
	- È possibile usare **il proprio cluster HDInsight** invece di un cluster HDInsight su richiesta. Per informazioni dettagliate, vedere [Servizio collegato HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
	- Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON **linkedServiceName**. HDInsight non elimina il contenitore quando viene eliminato il cluster. Si tratta di un comportamento previsto da progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo, **timeToLive**, che viene eliminato al termine dell'elaborazione.
	
		Man mano che vengono elaborate sempre più sezioni, verranno visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. Il nome di questi contenitori segue uno schema: "adf**nomedatafactory**-**nomeserviziocollegato**-datatimestamp". Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Storage Explorer](http://storageexplorer.com/).

	Per informazioni dettagliate, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
2. Eseguire il cmdlet **New-AzureRmDataFactoryLinkedService** per creare il servizio collegato HDInsightOnDemandLinkedService.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Passaggio 3: Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto **StorageLinkedService** creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione di Azure e i set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.

### Creare il set di dati di input
1. Creare un file JSON denominato **InputTable.json** nella cartella **C:\\ADFGetStarted** con il contenuto seguente:

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	Il codice JSON precedente definisce un set di dati denominato **AzureBlobInput**, che rappresenta dati di input per un'attività nella pipeline. Specifica anche che i dati di input si trovano nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **inputdata**.

	La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

	| Proprietà | Descrizione |
	| :------- | :---------- |
	| type | La proprietà type è impostata su AzureBlob perché i dati si trovano nell'archiviazione BLOB di Azure. |  
	| linkedServiceName | Fa riferimento all'oggetto StorageLinkedService creato in precedenza. |
	| fileName | Questa proprietà è facoltativa. Se si omette questa proprietà, vengono prelevati tutti i file da folderPath. In tal caso viene elaborato solo il file input.log. |
	| type | I file di log sono in formato testo, si usa quindi TextFormat. | 
	| columnDelimiter | Le colonne nei file di log sono delimitate da virgola (,). |
	| frequenza/intervallo | La frequenza è impostata su Month e l'intervallo è 1, ciò significa che le sezioni di input sono disponibili con cadenza mensile. | 
	| external | Questa proprietà è impostata su true se i dati di input non vengono generati dal servizio Data factory. | 

2. Eseguire il comando seguente in Azure PowerShell per creare il set di dati di Data factory.

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Creare il set di dati di output
Viene creato ora il set di dati di output per rappresentare i dati di output archiviati nell'archiviazione BLOB di Azure.

1. Creare un file JSON denominato **OutputTable.json** nella cartella **C:\\ADFGetStarted** con il contenuto seguente:

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
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

	Il codice JSON precedente definisce un set di dati denominato **AzureBlobOutput**, che rappresenta dati di output per un'attività nella pipeline. Specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.

2. Eseguire il comando seguente in Azure PowerShell per creare il set di dati di Data factory.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Passaggio 3: Creare la prima pipeline
In questo passaggio viene creata la prima pipeline con un'attività **HDInsightHive**. Si noti che la sezione di input è disponibile ogni mese (frequenza: Month, intervallo: 1), la sezione di output viene generata ogni mese e anche la proprietà dell'utilità di pianificazione dell'attività è impostata su una frequenza mensile (vedere sotto). Le impostazioni per il set di dati di output e l'utilità di pianificazione dell'attività devono corrispondere. In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. Le proprietà usate nel codice JSON seguente sono illustrate in fondo a questa sezione.


1. Creare un file JSON denominato MyFirstPipelinePSH.json nella cartella C:\\ADFGetStarted con il contenuto seguente:

	> [AZURE.IMPORTANT] Nel codice JSON sostituire **storageaccountname** con il nome dell'account di archiviazione.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	Nel frammento di codice JSON si crea una pipeline costituita da una singola attività che usa Hive per elaborare i dati in un cluster HDInsight.
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **StorageLinkedService**) e nella cartella **script** nel contenitore **adfgetstarted**.

	La sezione **defines** è usata per specificare le impostazioni di runtime che verranno passate allo script Hive come valori di configurazione Hive (ad esempio, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Le proprietà **start** ed **end** della pipeline ne specificano il periodo attivo.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata da **linkedServiceName** - **HDInsightOnDemandLinkedService**.

	> [ACOM.NOTE] Per informazioni dettagliate sulle proprietà JSON usate nell'esempio precedente, vedere [Anatomia di una pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline). 
2.  Verificare che il file **input.log** si trovi nella cartella **adfgetstarted/inputdata** nell'archiviazione BLOB di Azure ed eseguire il comando seguente per distribuire la pipeline. Dal momento che gli orari di **inizio** e **fine** sono impostati nel passato e **isPaused** è impostato su false, la pipeline (l'attività nella pipeline) viene eseguita immediatamente dopo la distribuzione. 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. La creazione della prima pipeline tramite Azure PowerShell è così completata.

### <a name="MonitorDataSetsAndPipeline"></a> Monitorare i set di dati e la pipeline
In questo passaggio viene usato Azure PowerShell per monitorare le attività in un'istanza di Data factory di Azure.

1. Eseguire **Get-AzureRmDataFactory** e assegnare l'output a una variabile **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. Eseguire **Get-AzureRmDataFactorySlice** per ottenere dettagli su tutte le sezioni di **EmpSQLTable**, ovvero la tabella di output della pipeline.

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Si noti che il valore di StartDateTime specificato qui corrisponde all'ora di inizio specificata nel codice JSON della pipeline. L'output dovrebbe essere simile al seguente.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. Eseguire **Get-AzureRmDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione specifica.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	L'output dovrebbe essere simile al seguente.
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	È possibile continuare a eseguire questo cmdlet fino a quando la sezione non passa allo stato **Pronto** oppure **Operazione non riuscita**. Quando lo stato della sezione è Pronto, cercare i dati di output nella cartella **partitioneddata** del contenitore **adfgetstarted** nell'archivio BLOB. Tenere presente che la creazione di un cluster HDInsight su richiesta di solito richiede tempo.

	![Dati di output](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta di Azure. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL](./data-factory-get-started.md).

### Riferimenti
| Argomento | Descrizione |
| :---- | :---- |
| [Informazioni di riferimento sui cmdlet di Data factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) | Vedere la documentazione completa sui cmdlet di Data factory |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo consentirà di conoscere le pipeline e le attività in Data factory di Azure e su come sfruttarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo illustra i set di dati in Data factory di Azure.
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline. Contiene anche informazioni su come creare avvisi e ricevere notifiche sugli errori. |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_0309_2016-->