<properties 
	pageTitle="Esercitazione: creare e monitorare una factory di dati di Azure tramite Azure PowerShell" 
	description="Informazioni su come utilizzare Azure PowerShell per creare e monitorare la factory di dati di Azure." 
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
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# Esercitazione: Creare e monitorare una factory di dati con Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

Il [iniziare a utilizzare Azure dati Factory][adf-get-started] esercitazione viene illustrato come creare e monitorare una factory di dati di Azure utilizzando il [portale Azure Preview][azure-preview-portal]. In questa esercitazione verranno creare e monitorare una factory di dati di Azure utilizzando i cmdlet PowerShell di Azure. La pipeline nell'ambiente di produzione di dati creato in questa esercitazione copia dati da un Azure blob a un database SQL Azure.

> [AZURE.NOTE]In questo articolo non copre tutti i cmdlet di Factory di dati. Vedere [dati Factory Cmdlet riferimento][cmdlet-reference] per una documentazione completa sui cmdlet di Factory di dati.

##Prerequisiti
A parte i prerequisiti elencati nell'argomento Panoramica dell'esercitazione, è necessario disporre di Azure PowerShell installata nel computer. Se non si possiede già, scaricare e installare [Azure PowerShell][download-azure-powershell] nel computer.

##Contenuto dell'esercitazione:
Nella tabella seguente sono elencati i passaggi che verranno eseguite come parte dell'esercitazione e le relative descrizioni.

Passaggio | Descrizione
-----| -----------
[Passaggio 1: Creare una Factory di dati di Azure](#CreateDataFactory) | In questo passaggio si creerà una factory di dati di Azure denominata **ADFTutorialDataFactoryPSH**. 
[Passaggio 2: Creare servizi collegati](#CreateLinkedServices) | In questo passaggio si creerà due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. Il StorageLinkedService collega una risorsa di archiviazione Azure e AzureSqlLinkedService collega un database SQL Azure per la ADFTutorialDataFactoryPSH.
[Passaggio 3: Creare input e output di set di dati](#CreateInputAndOutputDataSets) | In questo passaggio verranno definiti due set di dati (* * EmpTableFromBlob * * e **EmpSQLTable**) che vengono utilizzati come tabelle di input e output per il **attività Copia** in ADFTutorialPipeline che verrà creato nel passaggio successivo.
[Passaggio 4: Creare ed eseguire una pipeline](#CreateAndRunAPipeline) | In questo passaggio si creerà una pipeline denominata **ADFTutorialPipeline** nell'ambiente di produzione di dati: **ADFTutorialDataFactoryPSH**.. La pipeline avrà un **attività Copia** che copia i dati da un Azure blob a una tabella del database Azure di output.
[Passaggio 5: Monitorare i set di dati e della pipeline](#MonitorDataSetsAndPipeline) | In questo passaggio, monitorare i set di dati e la pipeline con Azure PowerShell in questo passaggio.

## <a name="CreateDataFactory"></a>Passaggio 1: Creare una Factory di dati di Azure
In questo passaggio, è possibile utilizzare Azure PowerShell per creare una factory di dati di Azure denominata **ADFTutorialDataFactoryPSH**.

1. Avviare **Azure PowerShell** ed eseguire i comandi seguenti. Tenere aperto Azure PowerShell fino alla fine di questa esercitazione. Se si chiude e riapre, è necessario eseguire di nuovo questi comandi.
	- Eseguire **Add-AzureAccount** e immettere il nome utente e password utilizzati per accedere al portale di anteprima di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per questo account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione che si desidera utilizzare. La sottoscrizione deve corrispondere a quella usata nel portale di anteprima di Azure. 
2. Passare a **AzureResourceManager** modalità cmdlet Factory di dati di Azure sono disponibili in questa modalità.

		Switch-AzureMode AzureResourceManager 
3. Creare un gruppo di risorse di Azure denominato: **ADFTutorialResourceGroup** eseguendo il comando riportato di seguito.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Alcuni dei passaggi in questa esercitazione si presuppone che si utilizzino il gruppo di risorse denominato **ADFTutorialResourceGroup**. Se si utilizza un gruppo di risorse diversi, sarà necessario utilizzarlo al posto di ADFTutorialResourceGroup in questa esercitazione. 
4. Eseguire il **Nuovo AzureDataFactory** cmdlet per creare una factory di dati denominata: **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato l'errore: **dati factory "ADFTutorialDataFactoryPSH" non è disponibile**, modificare il nome (ad esempio, yournameADFTutorialDataFactoryPSH). Utilizzare questo nome al posto di ADFTutorialFactoryPSH durante l'esecuzione di passaggi in questa esercitazione.

## <a name="CreateLinkedServices"></a>Passaggio 2: Creare servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere una risorsa di archiviazione di Azure, Database SQL di Azure o un database di SQL Server locale che contiene dati di input o vengono archiviati i dati di output per una pipeline di dati Factory. Un servizio di calcolo è il servizio che elabora i dati di input e produce l'output dei dati.

In questo passaggio si creerà due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService collegate collegamenti al servizio un Account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL Azure per la factory di dati: **ADFTutorialDataFactoryPSH**. Più avanti in questa esercitazione che copia dati da un contenitore di blob in StorageLinkedService in una tabella SQL AzureSqlLinkedService si creerà una pipeline.

### Creazione di un servizio collegato per un account di archiviazione di Azure
1.	Creare un file JSON denominato **StorageLinkedService.json** nel **C:\ADFGetStartedPSH** con il contenuto seguente. Creare la cartella ADFGetStartedPSH se non esiste già.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	Nel **Azure PowerShell**, passare al **ADFGetStartedPSH** cartella. 
3.	È possibile utilizzare il **Nuovo AzureDataFactoryLinkedService** cmdlet per creare un servizio collegato. Questo cmdlet e altri cmdlet di Factory di dati utilizzati in questa esercitazione richiedono per passare i valori il **ResourceGroupName** e **DataFactoryName** parametri. In alternativa, è possibile utilizzare **Get AzureDataFactory** per ottenere un oggetto DataFactory e passare l'oggetto senza digitare ResourceGroupName e DataFactoryName ogni volta che si esegue un cmdlet. Eseguire il comando seguente per assegnare l'output del **Get AzureDataFactory** cmdlet a una variabile: **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	A questo punto, eseguire il **Nuovo AzureDataFactoryLinkedService** cmdlet per creare il servizio collegato: **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se non è stato eseguito il **Get AzureDataFactory** cmdlet e assegnare l'output a **$df** variabile, è necessario specificare i valori per i parametri ResourceGroupName e DataFactoryName come indicato di seguito.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Se si chiude Azure PowerShell durante l'esercitazione, verrà hanno eseguito il cmdlet Get-AzureDataFactory successivo avvio di Azure PowerShell per completare l'esercitazione.

### Creazione di un servizio collegato per un database SQL di Azure
1.	Creare un file JSON denominato AzureSqlLinkedService.json con il contenuto riportato di seguito.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	Eseguire il comando seguente per creare un servizio collegato. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]Verificare che il **consentire l'accesso ai servizi Azure** è attivata per il server SQL Azure. Per verificare e attivarlo, procedere come segue:
	>
	> <ol>
<li>Fare clic su <b>Sfoglia</b> hub a sinistra e fare clic su <b>istanze di SQL Server</b>.</li>
<li>Selezionare il server, quindi fare clic su <b>impostazioni</b> sul <b>SQL SERVER</b> blade.</li>
<li>Nel <b>impostazioni</b> blade, fare clic su <b>Firewall</b>.</li>
<li>Nel <b>Firewalll impostazioni</b> blade, fare clic su <b>via</b> per <b>consentire l'accesso ai servizi Azure</b>.</li>
<li>Fare clic su <b>ACTIVE</b> hub a sinistra per passare al <b>dati Factory</b> blade si è in.</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>Passaggio 3: Creazione di input e output di tabelle

Nel passaggio precedente, creato servizi collegati **StorageLinkedService** e **AzureSqlLinkedService** per collegare un account di archiviazione Azure e un database di SQL Azure per la factory di dati: **ADFTutorialDataFactoryPSH**. In questo passaggio si creerà tabelle che rappresentano l'input e output dei dati per l'attività Copia nella pipeline che verrà creata nel passaggio successivo.

Una tabella è un set di dati rettangolare ed è l'unico tipo di set di dati supportato in questa fase. La tabella di input in questa esercitazione si riferisce a un contenitore di blob nell'archiviazione di Azure che StorageLinkedService punti a e la tabella di output fa riferimento a una tabella SQL nel database SQL Azure a cui punta la AzureSqlLinkedService.

### Preparazione dell'archiviazione BLOB di Azure e del database SQL Azure per l'esercitazione
Ignorare questo passaggio se hanno completato l'esercitazione dal [iniziare a utilizzare Azure dati Factory][adf-get-started] articolo.

È necessario attenersi alla seguente procedura per preparare il database di SQL Azure e archiviazione blob di Azure per questa esercitazione.
 
* Creare un contenitore blob denominato **adftutorial** di Azure nell'archiviazione blob che **StorageLinkedService** punta a. 
* Creare e caricare un file di testo **emp.txt**, come un blob per il **adftutorial** contenitore. 
* Creare una tabella denominata **emp** nel Database di SQL Azure in SQL Azure database **AzureSqlLinkedService** punta a.


1. Avviare Blocco note, incollare il testo seguente e salvarlo come **emp.txt** a **C:\ADFGetStartedPSH** cartella sul disco rigido. 

        John, Doe
		Jane, Doe
				
2. Utilizzare strumenti come [Esplora archivi Azure](https://azurestorageexplorer.codeplex.com/) per creare il **adftutorial** contenitore e per caricare il **emp.txt** file nel contenitore.

    ![Esplora archivi Azure][image-data-factory-get-started-storage-explorer]
3. Utilizzare il seguente script SQL per creare il **emp** tabella nel Database di SQL Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Se hai installato nel computer SQL Server 2014: seguire le istruzioni da [passaggio 2: connettersi al Database SQL di gestione di Database SQL di Azure con SQL Server Management Studio][sql-management-studio] articolo per connettersi al server SQL Azure ed eseguire lo script SQL.

	Se si dispone di Visual Studio 2013 installato nel computer: nel portale di anteprima di Azure ([http://portal.azure.com](http://portal.sazure.com)), fare clic su **Sfoglia** hub sulla sinistra fare clic su **istanze di SQL Server**, selezionare il database e fare clic su **aperto in Visual Studio** sulla barra degli strumenti per connettersi al server SQL Azure ed eseguire lo script. Se il client non è consentito accedere al server SQL Azure, sarà necessario configurare il firewall per il server SQL Azure consentire l'accesso dal computer (indirizzo IP). Vedere l'articolo sopra per le procedure per configurare il firewall per il server SQL Azure.
		
### Creazione della tabella di input 
Un tabella è un set di dati rettangolare che prevede uno schema. In questo passaggio si creerà una tabella denominata **EmpBlobTable** che punta a un contenitore di blob in Azure Storage rappresentata dal **StorageLinkedService** collegato del servizio. Questo contenitore di blob (* * adftutorial * *) contiene i dati nel file di input: **emp.txt**.

1.	Creare un file JSON denominato **EmpBlobTable.json** nel **C:\ADFGetStartedPSH** cartella con il contenuto seguente:

		{
	    	"name": "EmpTableFromBlob",
	        "properties":
	        {
	            "structure":  
	                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "StorageLinkedService"
	            },
	            "availability": 
	            {
	                "frequency": "hour",
	                "interval": 1,
	                "waitonexternal": {}
	                }
	        }
		}
	
	Tenere presente quanto segue:
	
	- percorso **tipo** è impostato su **AzureBlobLocation**.
	- **linkedServiceName** è impostato su **StorageLinkedService**. 
	- **folderPath** viene impostato il **adftutorial** contenitore. È anche possibile specificare il nome di un BLOB all'interno della cartella. Poiché non si specifica il nome del BLOB, i dati da tutti i BLOB nel contenitore sono considerati come dati di input.  
	- formato **tipo** è impostato su **FormatoTesto**
	- Sono disponibili due campi nel file di testo: **FirstName** e **LastName** separati da una virgola (* * columnDelimiter * *)	
	- Il **disponibilità** è impostato su **oraria** (* * frequenza * * è impostato su **ora** e **intervallo** è impostato su **1** ), in modo che il servizio dati Factory Cerca dati di input ogni ora nella cartella radice nel contenitore di blob (* * adftutorial * *) specificato.

	Se non si specifica un **fileName** per un **input** **tabella**, tutti i BLOB/file dalla cartella di input (* * folderPath * *) vengono considerati come input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Visualizzare i file di esempio nel [esercitazione][adf-tutorial] per gli esempi.
 
	Se non si specifica un **fileName** per un **tabella di output**, il generato il file nel **folderPath** denominato nel formato seguente: dati. < Guid >. txt (esempio: Data.0a405f8a 93ff di b3be 4c6f f69616f1df7a.txt.).

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

	> [AZURE.NOTE]Vedere [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) per informazioni dettagliate sulle proprietà JSON.

2.	Eseguire il comando seguente per creare la tabella dati Factory.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Creazione della tabella di output
In questa parte del passaggio, si creerà una tabella di output denominata **EmpSQLTable** che punta a una tabella SQL (* * emp * *) nel database SQL Azure è rappresentato dal **AzureSqlLinkedService** collegato del servizio. La pipeline di dati copiati dal blob di input per il **emp** tabella.

1.	Creare un file JSON denominato **EmpSQLTable.json** nel **C:\ADFGetStartedPSH** cartella con il contenuto seguente.
		
		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSQLTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

     Tenere presente quanto segue:
	
	* percorso **tipo** è impostato su **AzureSQLTableLocation**.
	* **linkedServiceName** è impostato su **AzureSqlLinkedService**.
	* **tablename** è impostato su **emp**.
	* Sono presenti tre colonne: **ID**, **FirstName**, e **LastName** : nella tabella emp nel database, ma l'ID è una colonna identity, pertanto è necessario specificare solo **FirstName** e **LastName** qui.
	* Il **disponibilità** è impostato su **oraria** (* * frequenza * * impostato su **ora** e **intervallo** impostato su **1**). Il servizio dati Factory genererà una porzione di dati di output ogni ora il **emp** tabella nel database SQL Azure.

2.	Eseguire il comando seguente per creare la tabella dati Factory.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Passaggio 4: Creare ed eseguire una pipeline
In questo passaggio, è possibile creare una pipeline con un **attività Copia** che utilizza **EmpTableFromBlob** come input e **EmpSQLTable** come output.

1.	Creare un file JSON denominato **ADFTutorialPipeline.json** nel **C:\ADFGetStartedPSH** cartella con il contenuto seguente: 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	Tenere presente quanto segue:

	- Nella sezione attività esiste una sola attività cui **tipo** è impostato su **CopyActivity**.
	- Input per l'attività è impostata su **EmpTableFromBlob** e di output per l'attività è impostata su **EmpSQLTable**.
	- Nel **trasformazione** sezione **BlobSource** viene specificato come tipo di origine e **SqlSink** viene specificato come tipo di sink.

	> [AZURE.NOTE]Sostituire il valore del **avviare** proprietà con il giorno corrente e **end** valore con il giorno successivo. Avviare entrambi e date e ore di fine deve essere [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. Il **end** ora è facoltativo, ma si utilizzerà in questa esercitazione. Se non si specifica il valore per il **end** proprietà, viene calcolato come "* * inizio + 48 ore * *". Per eseguire la pipeline a tempo indeterminato, specificare **9/9/9999** come valore per il **end** proprietà. Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
	
	> [Riferimento script JSON](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	Eseguire il comando seguente per creare la tabella dati Factory. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Congratulazioni**. Data factory di Azure, i servizi collegati, le tabelle e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.

## <a name="MonitorDataSetsAndPipeline"></a>Passaggio 5: Monitorare i set di dati e la pipeline
In questo passaggio, si utilizzerà Azure PowerShell per monitorare cosa sta succedendo in una factory di dati di Azure.

1.	Eseguire **Get AzureDataFactory** e assegnare l'output a una variabile $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Eseguire **Get AzureDataFactorySlice** per ottenere informazioni dettagliate su tutte le sezioni del **EmpSQLTable**, ovvero la tabella di output della pipeline.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]Sostituire l'anno, mese e parte data del **valore StartDateTime** parametro con l'anno corrente, mese e giorno. Deve corrispondere il **avviare** valore nella pipeline di JSON.

	Si noterà 24 sezioni, una per ogni ora, 24 ore del giorno corrente ore 12.00 del giorno successivo.
	
	**Prima sezione:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Ultima sezione:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Eseguire **Get AzureDataFactoryRun** per ottenere i dettagli dell'attività viene eseguita per un **specifico** sezione. Modificare il valore del **valore StartDateTime** parametro corrispondente il **avviare** tempo della sezione nell'output precedente. Il valore del **valore StartDateTime** deve essere [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-03-03T22:00:00Z.

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

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

> [AZURE.NOTE]Vedere [dati Factory Cmdlet riferimento][cmdlet-reference] per una documentazione completa sui cmdlet di Factory di dati.

## Passaggi successivi

Articolo | Descrizione
------ | ---------------
[Copiare i dati con Azure dati Factory - attività di copia][copy-activity] | Questo articolo vengono fornite informazioni dettagliate sul **attività Copia** utilizzato in questa esercitazione. 
[Abilitare le pipeline lavorare con dati locali][use-onpremises-datasources] | In questo articolo è una procedura dettagliata in cui viene illustrato come copiare dati da un **database di SQL Server on-premise** in un blob di Azure. 
[Utilizzare Pig e Hive con dati Factory][use-pig-and-hive-with-data-factory] | In questo articolo è una procedura dettagliata che illustra come utilizzare **attività HDInsight** per eseguire un **hive/pig** script per l'elaborazione dati di input per produrre dati di output.
[Esercitazione: Spostare ed elaborare i file di log utilizzando la Factory di dati][adf-tutorial] | In questo articolo fornisce un **procedura dettagliata end-to-end** che viene illustrato come implementare un **scenario reale** utilizza Azure dati Factory per trasformare i dati dai file di log in approfondimenti.
[Utilizzare le attività personalizzate in una Factory di dati][use-custom-activities] | In questo articolo fornisce una procedura dettagliata con istruzioni dettagliate per la creazione di un **attività personalizzata** e l'utilizzo in una pipeline. 
[Risoluzione dei problemi di Factory di dati][troubleshoot] | Questo articolo viene descritto come **risoluzione dei problemi** problemi Factory di dati di Azure. È possibile provare la procedura dettagliata di questo articolo in ADFTutorialDataFactory introducendo un errore (eliminando la tabella nel database SQL di Azure). 
[Riferimento ai Cmdlet di Factory di dati di Azure][cmdlet-reference] | Il contenuto di riferimento include dettagli su tutti i **cmdlet Factory dati**.
[Riferimenti per sviluppatori Factory di dati di Azure][developer-reference] | Il riferimento per sviluppatori presenta il contenuto di riferimento completo per i cmdlet, script JSON, funzioni e così via... 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: ../storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-create-sql-database]: ../sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir--> 