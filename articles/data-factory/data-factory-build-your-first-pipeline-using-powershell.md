<properties
	pageTitle="Creare la prima data factory (PowerShell) | Microsoft Azure"
	description="In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory usando Azure PowerShell."
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
	ms.date="08/16/2016"
	ms.author="spelluru"/>  

# Esercitazione: Creare la prima data factory di Azure con Azure PowerShell
> [AZURE.SELECTOR]
- [Portale di Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modello di Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


[AZURE.INCLUDE [data-factory-tutorial-prerequisites](../../includes/data-factory-tutorial-prerequisites.md)]

## Ulteriori prerequisiti
Oltre ai prerequisiti elencati nell'argomento di panoramica dell'esercitazione, sarà necessario installare quanto segue:

- **Azure PowerShell**. Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer.
- (facoltativo) Questo articolo non illustra tutti i cmdlet di Data factory. Vedere [Riferimento ai cmdlet di Data factory](https://msdn.microsoft.com/library/dn820234.aspx) per la documentazione completa sui cmdlet di Data factory.

Se si usa una **versione precedente alla 1.0** di Azure PowerShell, è necessario usare i cmdlet documentati [qui](https://msdn.microsoft.com/library/azure/dn820234.aspx). È anche necessario eseguire i comandi seguenti prima di usare i cmdlet di Data Factory:
 
1. Aprire Azure PowerShell ed eseguire i comandi seguenti. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	1. Eseguire `Add-AzureAccount` e immettere il nome utente e la password usati per accedere al portale di Azure.
	2. Eseguire `Get-AzureSubscription` per visualizzare tutte le sottoscrizioni per l'account.
	3. Eseguire `Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext` per selezionare la sottoscrizione da usare. Sostituire **NameOfAzureSubscription** con il nome della sottoscrizione di Azure.
4. Passare alla modalità Azure Resource Manager perché i cmdlet di Azure Data Factory sono disponibili in questa modalità: `Switch-AzureMode AzureResourceManager`.

## Creare un'istanza di Data Factory

In questo passaggio è possibile usare Azure PowerShell per creare una data factory di Azure denominata **FirstDataFactoryPSH**. Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, attività di copia per copiare dati da un'origine a un archivio dati di destinazione e attività Hive di HDInsight per eseguire uno script Hive e trasformare i dati di input. In questo passaggio iniziale viene creata la data factory.

1. Aprire Azure PowerShell ed eseguire il comando seguente. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	- Eseguire `Login-AzureRmAccount` e immettere il nome utente e la password usati per accedere al portale di Azure.
	- Eseguire `Get-AzureRmSubscription` per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire `Select-AzureRmSubscription <Name of the subscription>` per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
3. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo il comando seguente.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato ADFTutorialResourceGroup. Se si usa un gruppo di risorse diverso, è necessario usarlo al posto di ADFTutorialResourceGroup in questa esercitazione.
4. Eseguire il cmdlet **New-AzureRmDataFactory** che crea una data factory denominata **FirstDataFactoryPSH**.

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"


Tenere presente quanto segue:
 
- È necessario specificare un nome univoco globale per la Data factory di Azure. Se viene visualizzato l'errore **Il nome "FirstDataFactoryPSH" per la data factory non è disponibile**, modificare il nome (ad esempio, nomeutenteFirstDataFactoryPSH). Durante l'esecuzione di passaggi in questa esercitazione usare questo nome anziché ADFTutorialFactoryPSH. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md).
- Per creare istanze di data factory, è necessario essere un collaboratore/amministratore della sottoscrizione di Azure.
- Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.
- Se viene visualizzato l'errore "**La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory**", eseguire una di queste operazioni e provare a ripetere la pubblicazione:

	- In Azure PowerShell eseguire questo comando per registrare il provider di Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		È possibile eseguire questo comando per verificare che il provider di Data Factory sia registrato.
	
			Get-AzureRmResourceProvider
	- Accedere usando la sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com) e passare al pannello Data Factory oppure creare un'istanza di Data Factory nel portale di Azure. Questa azione registra automaticamente il provider.

Prima di creare una pipeline è necessario creare alcune entità di Data factory. Creare prima di tutto i servizi collegati per collegare archivi dati/servizi di calcolo all'archivio dati, definire i set di dati di input e di output per rappresentare i dati di input/output negli archivi dati collegati e quindi creare la pipeline con un'attività che usa questi set di dati.

## Creazione di servizi collegati 
In questo passaggio l'account di archiviazione di Azure e un cluster HDInsight su richiesta di Azure vengono collegati alla data factory. In questo esempio l'account di archiviazione di Azure contiene i dati di input e di output per la pipeline. In questo esempio il servizio collegato HDInsight viene usato per eseguire lo script Hive specificato nell'attività della pipeline. Identificare l'archivio dati/i servizi di calcolo usati nello scenario e collegare tali servizi alla data factory creando servizi collegati.

### Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. Viene usato lo stesso account di archiviazione di Azure per archiviare i dati di input/output e il file di script HQL.

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
3. È possibile usare il cmdlet **New-AzureRmDataFactoryLinkedService** che crea un servizio collegato. Questo cmdlet e altri cmdlet di Data factory usati in questa esercitazione richiedono il passaggio di valori per i parametri *ResourceGroupName* e *DataFactoryName*. In alternativa, è possibile usare **Get-AzureRmDataFactory** per ottenere un oggetto **DataFactory** e passarlo senza digitare *ResourceGroupName* e *DataFactoryName* ogni volta che si esegue un cmdlet. Eseguire il comando seguente per assegnare l'output del cmdlet **Get-AzureRmDataFactory** a una variabile **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Eseguire quindi il cmdlet **New-AzureRmDataFactoryLinkedService** che crea il servizio collegato **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se il cmdlet **Get-AzureRmDataFactory** non è stato eseguito e l'output non è stato assegnato alla variabile **$df**, sarà necessario specificare i valori per i parametri *ResourceGroupName* e *DataFactoryName* come indicato di seguito.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	Se si chiude Azure PowerShell durante l'esercitazione, per completarla è necessario eseguire il cmdlet **Get-AzureRmDataFactory** al successivo avvio di Azure PowerShell.

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
	| ClusterSize | Specifica le dimensioni del cluster HDInsight. | 
	| TimeToLive | Specifica il tempo di inattività del cluster HDInsight, prima che sia eliminato. |
	| linkedServiceName | Specifica l'account di archiviazione che viene usato per archiviare i log generati da HDInsight. |

	Tenere presente quanto segue:
	
	- Data Factory crea automaticamente un cluster HDInsight **basato su Windows** con il codice JSON. È anche possibile creare automaticamente un cluster HDInsight **basato su Linux**. Per i dettagli, vedere [Servizio collegato Azure HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
	- È possibile usare il **proprio cluster HDInsight** anziché un cluster HDInsight su richiesta. Per i dettagli, vedere [Servizio collegato Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
	- Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che viene elaborata una sezione, a meno che non esista un cluster attivo (**timeToLive**). Il cluster viene eliminato al termine dell'elaborazione.
	
		Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono questo schema: "adf**nomedatafactory**-**nomeserviziocollegato**-datetimestamp". Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Storage Explorer](http://storageexplorer.com/).

	Per i dettagli, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
2. Eseguire il cmdlet **New-AzureRmDataFactoryLinkedService** che crea il servizio collegato HDInsightOnDemandLinkedService.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto **StorageLinkedService** creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione di Azure e i set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.

### Creare set di dati di input
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

	Il codice JSON definisce un set di dati denominato **AzureBlobInput**, che rappresenta dati di input per un'attività nella pipeline. Specifica anche che i dati di input si trovano nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **inputdata**.

	La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

	| Proprietà | Descrizione |
	| :------- | :---------- |
	| type | La proprietà type è impostata su AzureBlob perché i dati si trovano nell'archiviazione BLOB di Azure. |  
	| linkedServiceName | Fa riferimento all'oggetto StorageLinkedService creato in precedenza. |
	| fileName | Questa proprietà è facoltativa. Se si omette questa proprietà, vengono prelevati tutti i file da folderPath. In tal caso viene elaborato solo il file input.log. |
	| type | I file di log sono in formato testo, quindi viene usato TextFormat. | 
	| columnDelimiter | Le colonne nei file di log sono delimitate da virgola (,). |
	| frequenza/intervallo | La frequenza è impostata su Month e l'intervallo è 1, ciò significa che le sezioni di input sono disponibili con cadenza mensile. | 
	| external | Questa proprietà è impostata su true se i dati di input non vengono generati dal servizio Data factory. | 

2. Eseguire il comando seguente in Azure PowerShell per creare il set di dati di Data factory.

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Creare il set di dati di output
Viene creato ora il set di dati di output per rappresentare i dati di output archiviati nell'archivio BLOB di Azure.

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

	Il codice JSON definisce un set di dati denominato **AzureBlobOutput**, che rappresenta dati di output per un'attività nella pipeline. Specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.

2. Eseguire il comando seguente in Azure PowerShell per creare il set di dati di Data factory.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Creare una pipeline
In questo passaggio viene creata la prima pipeline con un'attività **HDInsightHive**. La sezione di input è disponibile ogni mese (frequency: Month, interval: 1), la sezione di output viene generata ogni mese e anche la proprietà dell'utilità di pianificazione dell'attività è impostata su una frequenza mensile. Le impostazioni per il set di dati di output e l'utilità di pianificazione dell'attività devono corrispondere. In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. Le proprietà usate nel codice JSON seguente sono illustrate in fondo a questa sezione.


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
		        "start": "2016-04-01T00:00:00Z",
		        "end": "2016-04-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	Nel frammento di codice JSON si crea una pipeline costituita da una singola attività che usa Hive per elaborare i dati in un cluster HDInsight.
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **StorageLinkedService**) e nella cartella **script** nel contenitore **adfgetstarted**.

	La sezione **defines** è usata per specificare le impostazioni di runtime che vengono passate allo script Hive come valori di configurazione Hive, ad esempio, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}.

	Le proprietà **start** ed **end** della pipeline ne specificano il periodo attivo.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata da **linkedServiceName** - **HDInsightOnDemandLinkedService**.

	> [ACOM.NOTE] Per informazioni dettagliate sulle proprietà JSON usate nell'esempio, vedere [Anatomia di una pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline).
2.  Verificare che il file **input.log** si trovi nella cartella **adfgetstarted/inputdata** nell'archiviazione BLOB di Azure ed eseguire il comando seguente per distribuire la pipeline. Dal momento che gli orari di **inizio** e **fine** sono impostati nel passato e **isPaused** è impostato su false, la pipeline (l'attività nella pipeline) viene eseguita immediatamente dopo la distribuzione.

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. La creazione della prima pipeline tramite Azure PowerShell è così completata.

## Monitorare la pipeline
In questo passaggio viene usato Azure PowerShell per monitorare le attività in un'istanza di Azure Data Factory.

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

	È possibile continuare a eseguire questo cmdlet fino a quando la sezione non passa allo stato **Pronto** oppure **Operazione non riuscita**. Quando lo stato della sezione è Pronto, cercare i dati di output nella cartella **partitioneddata** del contenitore **adfgetstarted** nell'archivio BLOB. La creazione di un cluster HDInsight su richiesta di solito richiede tempo.

	![Dati di output](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png) 


> [AZURE.IMPORTANT] Il file di input viene eliminato quando la sezione viene elaborata correttamente. Per eseguire di nuovo la sezione o ripetere l'esercitazione, caricare quindi il file di input (input.log) nella cartella inputdata del contenitore adfgetstarted.

## Riepilogo 
In questa esercitazione è stata creata un'istanza di Azure Data Factory per elaborare i dati eseguendo lo script Hive in un cluster Hadoop di HDInsight. È stato usato l'editor di Data Factory nel portale di Azure per eseguire questa procedura:

1.	Creare un'istanza di Azure **Data Factory**.
2.	Creare due **servizi collegati**:
	1.	Il servizio collegato **Archiviazione di Azure** per collegare l'archivio BLOB di Azure che contiene i file di input/output alla data factory.
	2.	Il servizio collegato su richiesta **Azure HDInsight** per collegare un cluster Hadoop di HDInsight alla data factory. Azure Data Factory crea un cluster Hadoop di HDInsight JIT per elaborare i dati di input e generare i dati di output.
3.	Creare due **set di dati** che descrivono i dati di input e di output per l'attività Hive di HDInsight nella pipeline.
4.	Creare una **pipeline** con un'attività **Hive di HDInsight**.

## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta di Azure. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Vedere anche
| Argomento | Descrizione |
| :---- | :---- |
| [Informazioni di riferimento sui cmdlet di Data factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) | Vedere la documentazione completa sui cmdlet di Data factory |
| [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) | Questo articolo fornisce un elenco di attività di trasformazione dei dati (ad esempio, la trasformazione Hive di HDInsight usata in questa esercitazione) supportate da Azure Data Factory. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo fornisce informazioni sui set di dati in Azure Data Factory.
| [Monitorare e gestire le pipeline con i pannelli del portale di Azure.](data-factory-monitor-manage-pipelines.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline con i pannelli del portale di Azure. |
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. 

<!---HONumber=AcomDC_0914_2016-->