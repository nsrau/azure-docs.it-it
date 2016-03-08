<properties 
	pageTitle="Esercitazione: Creare una pipeline con l’attività Copia utilizzando Azure PowerShell" 
	description="In questa esercitazione si creerà una pipeline di Data factory di Azure con un'attività Copia utilizzando Azure PowerShell." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Esercitazione: Creare una pipeline con l’attività Copia utilizzando Azure PowerShell
> [AZURE.SELECTOR]
- [Panoramica dell'esercitazione](data-factory-get-started.md)
- [Con l'editor di Data factory](data-factory-get-started-using-editor.md)
- [Con Visual Studio](data-factory-get-started-using-vs.md)
- [Tramite PowerShell](data-factory-monitor-manage-using-powershell.md)


L'esercitazione [Introduzione a Data factory di Azure][adf-get-started] illustra come creare e monitorare una data factory di Azure mediante il [portale di Azure][azure-portal]. In questa esercitazione verrà creata e monitorata una data factory di Azure mediante i cmdlet di Azure PowerShell. La pipeline nella data factory creata in questa esercitazione usa un'attività di copia per copiare i dati da un BLOB di Azure e inserirli in un database SQL di Azure.

L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

> [AZURE.IMPORTANT] 
Leggere l'articolo [Panoramica dell'esercitazione](data-factory-get-started.md) e completare i prerequisiti prima di eseguire questa esercitazione.
>   
> Questo articolo non illustra tutti i cmdlet di Data factory. Vedere [Riferimento ai cmdlet di Data factory](https://msdn.microsoft.com/library/dn820234.aspx) per la documentazione completa sui cmdlet di Data factory.
  

##Prerequisiti
Oltre ai prerequisiti elencati nell'argomento di panoramica dell'esercitazione, sarà necessario installare quanto segue:

- **Azure PowerShell**. Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer.

Se si usa una **versione precedente alla 1.0** di Azure PowerShell, sarà necessario usare i cmdlet documentati [qui][old-cmdlet-reference]. Sarà anche necessario eseguire i comandi seguenti prima di usare i cmdlet di Data factory:

1. Aprire Azure PowerShell ed eseguire i comandi seguenti. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	1. Eseguire **Add-AzureAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.
	2. Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	3. Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
4. Passare alla modalità AzureResourceManager perché i cmdlet di Data factory di Azure sono disponibili in questa modalità: **Switch-AzureMode AzureResourceManager**.
  

##Contenuto dell'esercitazione:
La tabella seguente elenca i passaggi da eseguire come parte dell'esercitazione e le rispettive descrizioni.

Passaggio | Descrizione
-----| -----------
[Passaggio 1: Creare un'istanza di Data factory di Azure](#CreateDataFactory) | In questo passaggio si creerà una data factory di Azure denominata **ADFTutorialDataFactoryPSH**. 
[Passaggio 2: Creare servizi collegati](#CreateLinkedServices) | In questo passaggio verranno creati due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService collega un archivio di Azure e AzureSqlLinkedService collega un database SQL di Azure ad ADFTutorialDataFactoryPSH.
[Passaggio 3: Creare set di dati di input e di output](#CreateInputAndOutputDataSets) | In questo passaggio si definiranno due set di dati (**EmpTableFromBlob** e **EmpSQLTable**) usati come tabelle di input e output per l'**attività di copia** nella ADFTutorialPipeline creata nel passaggio seguente.
[Passaggio 4: Creare ed eseguire una pipeline](#CreateAndRunAPipeline) | In questo passaggio verrà creata una pipeline denominata **ADFTutorialPipeline** nella data factory **ADFTutorialDataFactoryPSH**. La pipeline avrà un'**attività di copia** che copia dati da un BLOB di Azure e li inserisce in una tabella di output del database di Azure.
[Passaggio 5: Monitorare i set di dati e la pipeline](#MonitorDataSetsAndPipeline) | In questo passaggio si monitoreranno i set di dati e la pipeline mediante Azure PowerShell.

## <a name="CreateDataFactory"></a>Passaggio 1: Creare un'istanza di Azure Data Factory
In questo passaggio è possibile usare Azure PowerShell per creare una data factory di Azure denominata **ADFTutorialDataFactoryPSH**.

1. Aprire Azure PowerShell ed eseguire il comando seguente. Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	- Eseguire **Login-AzureRmAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription <Name of the subscription>** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
3. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo il comando seguente.
   
		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato **ADFTutorialResourceGroup**. Se si usa un gruppo di risorse diverso, sarà necessario usarlo al posto di ADFTutorialResourceGroup in questa esercitazione. 
4. Eseguire il cmdlet **New-AzureRmDataFactory** per creare una data factory denominata **ADFTutorialDataFactoryPSH**.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

	È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato un errore analogo a **Il nome "ADFTutorialDataFactoryPSH" per la data factory non è disponibile**, cambiare il nome, ad esempio, nomeutenteADFTutorialDataFactoryPSH. Durante l'esecuzione di passaggi in questa esercitazione usare questo nome anziché ADFTutorialFactoryPSH. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md).

	> [AZURE.NOTE] Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.

## <a name="CreateLinkedServices"></a>Passaggio 2: Creare servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere una risorsa di archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale che include dati di input o archivia dati di output per una pipeline di Data factory. Un servizio di calcolo è un servizio che elabora dati di input e produce dati di output.

In questo passaggio verranno creati due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. Il servizio collegato StorageLinkedService collega un account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure alla data factory **ADFTutorialDataFactoryPSH**. Più avanti in questa esercitazione si creerà una pipeline che copia i dati da un contenitore BLOB di StorageLinkedService e li inserisce in una tabella SQL di AzureSqlLinkedService.

### Creare un servizio collegato per un account di archiviazione di Azure
1.	Creare un file JSON denominato **StorageLinkedService.json** in **C:\\ADFGetStartedPSH** con i contenuti seguenti. Creare la cartella ADFGetStartedPSH, se non esiste già.

			{
		  		"name": "StorageLinkedService",
		  		"properties": {
	    			"type": "AzureStorage",
		    		"typeProperties": {
		      			"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    		}
		  		}
			}

	Sostituire **accountname** con il nome dell'account di archiviazione e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure.
2.	In **Azure PowerShell** passare alla cartella **ADFGetStartedPSH**. 
3.	È possibile usare il cmdlet **New-AzureRmDataFactoryLinkedService** per creare un servizio collegato. Questo cmdlet e altri cmdlet di Data factory usati in questa esercitazione richiedono il passaggio di valori per i parametri **ResourceGroupName** e **DataFactoryName**. In alternativa, è possibile usare **Get-AzureRmDataFactory** per ottenere un oggetto DataFactory e passarlo senza digitare ResourceGroupName e DataFactoryName ogni volta che si esegue un cmdlet. Eseguire il comando seguente per assegnare l'output del cmdlet **Get-AzureRmDataFactory** a una variabile **$df**. 

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	Eseguire quindi il cmdlet **New-AzureRmDataFactoryLinkedService** per creare il servizio collegato **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se il cmdlet **Get-AzureRmDataFactory** non è stato eseguito e l'output non è stato assegnato alla variabile **$df**, sarà necessario specificare i valori per i parametri ResourceGroupName e DataFactoryName come indicato di seguito.
		
		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Se si chiude Azure PowerShell a metà esercitazione, sarà necessario eseguire il cmdlet Get-AzureRmDataFactory al successivo avvio di Azure PowerShell per completare l'esercitazione.

### Creare un servizio collegato per un database SQL di Azure
1.	Creare un file JSON denominato AzureSqlLinkedService.json con i contenuti seguenti.

			{
				"name": "AzureSqlLinkedService",
				"properties": {
					"type": "AzureSqlDatabase",
					"typeProperties": {
				      	"connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
					}
		  		}
			}

	Sostituire **servername**, **databasename**, ****username@servername** e **password** con i nomi del server, database, account utente e password SQL di Azure.

2.	Eseguire il comando seguente per creare un servizio collegato.
	
		New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Confermare che l'impostazione **Consenti accesso ai servizi Azure** sia attivata per il server SQL di Azure. Per verificare e attivare l'impostazione, eseguire le operazioni seguenti:

	1. Fare clic sull'hub **SFOGLIA** a sinistra, quindi su **Server SQL**.
	2. Selezionare il server e quindi fare clic su **IMPOSTAZIONI** nel pannello SERVER SQL.
	3. Nel pannello **IMPOSTAZIONI** fare clic su **Firewall**.
	4. Nel pannello **Impostazioni firewall** fare clic su **ATTIVA** per **Consenti accesso ai servizi Azure**.
	5. Fare clic sull'hub **ATTIVA** a sinistra per passare al pannello **Data factory** precedente.
	

## <a name="CreateInputAndOutputDataSets"></a>Passaggio 3: Creare tabelle di input e di output

Nel passaggio precedente sono stati creati i servizi collegati **StorageLinkedService** e **AzureSqlLinkedService** per collegare un account di archiviazione di Azure e un database SQL di Azure alla data factory **ADFTutorialDataFactoryPSH**. In questo passaggio verranno creati set di dati che rappresentano i dati di input e di output per l'attività di copia nella pipeline che verrà creata nel passaggio successivo.

Una tabella è un set di dati rettangolare ed è l'unico tipo di set di dati supportato in questa fase. La tabella di input di questa esercitazione fa riferimento a un contenitore BLOB nella risorsa di archiviazione di Azure a cui fa riferimento StorageLinkedService e la tabella di output fa riferimento a una tabella SQL nel database SQL di Azure a cui fa riferimento AzureSqlLinkedService.

### Preparare l'archiviazione BLOB di Azure e il database SQL Azure per l'esercitazione
Se è stata eseguita l'esercitazione disponibile nell'articolo [Introduzione ad Data factory di Azure][adf-get-started], ignorare questo passaggio.

È necessario eseguire i passaggi seguenti per preparare l'archivio BLOB di Azure e il database SQL di Azure per questa esercitazione.
 
* Creare un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure a cui fa riferimento **StorageLinkedService**. 
* Creare e caricare un file di testo **emp.txt** come BLOB per il contenitore **adftutorial**. 
* Creare una tabella denominata **emp** nel database SQL di Azure a cui fa riferimento **AzureSqlLinkedService**.


1. Avviare il Blocco note, incollare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\\ADFGetStartedPSH** sul disco rigido. 

        John, Doe
		Jane, Doe
				
2. Usare strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) per creare il contenitore **adftutorial** e per caricare il file **emp.txt** nel contenitore.

    ![Esplora archivi Azure][image-data-factory-get-started-storage-explorer]
3. Usare il seguente script SQL per creare la tabella **emp** nel database SQL di Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Se nel computer è installato SQL Server 2014, seguire le istruzioni disponibili nel [Passaggio 2: Connettersi al database SQL dell'articolo Gestione di database SQL di Azure tramite SQL Server Management Studio][sql-management-studio] per connettersi al server SQL di Azure ed eseguire lo script SQL.

	Se nel computer è installato Visual Studio 2013, nel portale di Azure ([http://portal.azure.com](http://portal.sazure.com)) fare clic sull'hub **SFOGLIA** a sinistra e quindi su **Server SQL**, selezionare il database e fare clic su **Apri in Visual Studio** sulla barra degli strumenti per connettersi al server di Azure SQL ed eseguire lo script. Se il client non è autorizzato ad accedere al server SQL di Azure, sarà necessario configurare il firewall per il server SQL di Azure in modo da consentire l'accesso dal computer (indirizzo IP). Per informazioni sulla procedura per configurare il firewall per il server SQL di Azure, vedere l'articolo precedente.
		
### Creare la tabella di input 
Un tabella è un set di dati rettangolare che prevede uno schema. In questo passaggio si creerà una tabella denominata **EmpBlobTable** che punta a un contenitore BLOB nella risorsa di archiviazione di Azure rappresentato dal servizio collegato **StorageLinkedService**. Questo contenitore BLOB (**adftutorial**) contiene i dati di input nel file: **emp.txt**.

1.	Creare un file JSON denominato **EmpBlobTable.json** nella cartella **C:\\ADFGetStartedPSH** con i contenuti seguenti:

			{
			  "name": "EmpTableFromBlob",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureBlob",
			    "linkedServiceName": "StorageLinkedService",
			    "typeProperties": {
				  "fileName": "emp.txt",
			      "folderPath": "adftutorial/",
			      "format": {
			        "type": "TextFormat",
			        "columnDelimiter": ","
			      }
			    },
			    "external": true,
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}
	
	Tenere presente quanto segue:
	
	- Il set di dati **type** è impostato su **AzureBlob**.
	- L'oggetto **linkedServiceName** è impostato su **StorageLinkedService**. 
	- L'oggetto **folderPath** è impostato sul contenitore **adftutorial**. 
	- **fileName** è impostato su **emp.txt**. Se non si specifica il nome del BLOB, i dati di tutti i BLOB nel contenitore sono considerati come dati di input.  
	- L'oggetto **type** di format è impostato su **TextFormat**.
	- Nel file di testo sono presenti due campi, **FirstName** e **LastName**, separati da una virgola (**columnDelimiter**).	
	- L'oggetto **availability** è impostato su **hourly** (**frequency** è impostato su **hour** e **interval** è impostato su **1**), in modo che il servizio Data factory cerchi dati di input ogni ora nella cartella radice del contenitore BLOB (**adftutorial**) specificato.

	Se non si specifica un oggetto **fileName** per una **tabella** di **input**, tutti i file/BLOB della cartella di input (**folderPath**) vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Per alcuni esempi, vedere i file nell'[esercitazione][adf-tutorial].
 
	Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà **partitionedBy**. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Ad esempio, se una sezione viene generata per 2014-10-20T08:00:00, folderName è impostato su wikidatagateway/wikisampledataout/2014/10/20 e fileName è impostato su 08.csv.

			"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	        "fileName": "{Hour}.csv",
	        "partitionedBy": 
	        [
	        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	        ],

	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2.	Eseguire il comando seguente per creare il set di dati di Data factory.

		New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Creazione della tabella di output
In questa parte del passaggio si creerà una tabella di output denominata **EmpSQLTable** che punta a una tabella SQL (**emp**) nel database SQL di Azure rappresentata dal servizio collegato **AzureSqlLinkedService**. La pipeline copia i dati dal BLOB di input e li inserisce nella tabella **emp**.

1.	Creare un file JSON denominato **EmpSQLTable.json** nella cartella **C:\\ADFGetStartedPSH** con i contenuti seguenti.
		
			{
			  "name": "EmpSQLTable",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureSqlTable",
			    "linkedServiceName": "AzureSqlLinkedService",
			    "typeProperties": {
			      "tableName": "emp"
			    },
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}

     Tenere presente quanto segue:
	
	* il set di dati **type** è impostato su **AzureSqlTable**.
	* L'oggetto **linkedServiceName** è impostato su **AzureSqlLinkedService**.
	* L'oggetto **tablename** è impostato su **emp**.
	* Nella tabella emp del database sono presenti tre colonne, **ID**, **FirstName** e **LastName**, ma ID è una colonna di identità, quindi è necessario specificare solo **FirstName** e **LastName**.
	* L'oggetto **availability** è impostato su **hourly** (l'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**). Il servizio Data factory genererà una porzione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure.

2.	Eseguire il comando seguente per creare il set di dati di Data factory.
	
		New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Passaggio 4: Creare ed eseguire una pipeline
In questo passaggio è possibile creare una pipeline con un'**attività di copia** che usa **EmpTableFromBlob** come input e **EmpSQLTable** come output.

1.	Creare un file JSON denominato **ADFTutorialPipeline.json** nella cartella **C:\\ADFGetStartedPSH** con i contenuti seguenti: 
	
			 {
			  "name": "ADFTutorialPipeline",
			  "properties": {
			    "description": "Copy data from a blob to Azure SQL table",
			    "activities": [
			      {
			        "name": "CopyFromBlobToSQL",
			        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
			        "type": "Copy",
			        "inputs": [
			          {
			            "name": "EmpTableFromBlob"
			          }
			        ],
			        "outputs": [
			          {
			            "name": "EmpSQLTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "BlobSource"
			          },
			          "sink": {
			            "type": "SqlSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2015-12-09T00:00:00Z",
			    "end": "2015-12-10T00:00:00Z",
			    "isPaused": false
			  }
			}

	Tenere presente quanto segue:

	- Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**.
	- L'input per l'attività è impostato su **EmpTableFromBlob** e l'output per l'attività è impostato su **EmpSQLTable**.
	- Nella sezione **transformation** **BlobSource** viene specificato come tipo di origine e **SqlSink** come tipo di sink.

	Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2014-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione verrà usato.
	
	Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come valore per la proprietà **end**.
	
	Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
	
	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2.	Eseguire il comando seguente per creare la tabella di Data factory. 
		
		New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Congratulazioni.** Data factory di Azure, i servizi collegati, le tabelle e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.

## <a name="MonitorDataSetsAndPipeline"></a>Passaggio 5: Monitorare i set di dati e la pipeline
In questo passaggio viene usato Azure PowerShell per monitorare le attività in un'istanza di Data factory di Azure.

1.	Eseguire **Get-AzureRmDataFactory** e assegnare l'output a una variabile $df.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Eseguire **Get-AzureRmDataFactorySlice** per ottenere dettagli su tutte le sezioni di **EmpSQLTable**, ovvero la tabella di output della pipeline.

		Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	Sostituire la parte relativa ad anno, mese e data del parametro **StartDateTime** con l'anno, il mese e la data correnti. Questi valori dovrebbero corrispondere al valore **Start** nel file JSON della pipeline.

	Dovrebbero essere visualizzate 24 sezioni, una per ogni ora a partire dalle 00.00 del giorno corrente alle 00.00 del giorno successivo.
	
	**Prima sezione:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     :
		LongRetryCount    : 0

	**Seconda sezione:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Eseguire **Get-AzureRmDataFactoryRun** per ottenere i dettagli delle esecuzioni di attività per una sezione **specifica**. Cambiare il valore del parametro **StartDateTime** in modo che corrisponda all'orario specificato per **Start** per la sezione dall'output precedente. Il valore di **StartDateTime** deve essere in [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-03-03T22:00:00Z.

		Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	L'output dovrebbe essere simile al seguente:

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

Vedere [Riferimento ai cmdlet di Data factory][cmdlet-reference] per la documentazione completa sui cmdlet di Data factory.

## Vedere anche
Vedere l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per informazioni dettagliate sull'**Attività di copia** in Azure Data Factory.


[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[adf-get-started]: data-factory-get-started.md
[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0302_2016-->