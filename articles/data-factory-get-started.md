<properties 
	pageTitle="Introduzione all'uso di Data factory di Azure" 
	description="Questa esercitazione illustra come creare una pipeline di dati di esempio che consente di copiare dati da un BLOB a un'istanza del database SQL di Azure." 
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
	ms.date="02/13/2015" 
	ms.author="spelluru"/>

# Introduzione a Data factory di Azure
Questo articolo consente di iniziare a usare Data factory di Azure. L'esercitazione contenuta in questo articolo mostra come creare un'istanza di Data factory di Azure e una pipeline nell'istanza di Data factory per copiare i dati di esempio da un'archiviazione BLOB di Azure a un database SQL di Azure.

L'elenco seguente illustra le operazioni tipiche che gli sviluppatori devono eseguire: 

1.	Creare un'istanza di **Data factory di Azure**.
2.	Creare **servizi collegati** per collegare gli archivi dati e i servizi di calcolo all'istanza di Data factory.  Ad esempio, un servizio collegato può collegare un database SQL di Azure o un cluster HDInsight all'istanza di Data factory.
3.	Creare **tabelle** che descrivono dati di input e dati di output per le pipeline. Le tabelle specificano anche la posizione effettiva dei dati negli archivi dati collegati a una data factory. Ad esempio, una tabella può specificare il nome della tabella SQL in un database SQL di Azure (o) un contenitore BLOB in un BLOB di Azure. 
4.	Creare **pipeline**. Una pipeline è costituita da una o più attività che usano dati di input e producono dati di output. Un'attività di copia consente di copiare i dati da un archivio dati di origine a un archivio dati di destinazione e un'attività HDInsight elabora dati di input usando gli script Hive/Pig.  
5.	Specificare il **periodo attivo** delle pipeline per l'elaborazione dati. Il periodo attivo definisce l'intervallo di tempo in cui verranno prodotte sezioni di dati. È possibile specificare la data e l'ora di inizio e di fine di una pipeline oppure è possibile mantenerla continuamente in esecuzione.

In questa esercitazione si apprenderà come: 

1.	Usare il **portale di anteprima di Azure** per creare un'istanza di Data factory di Azure e servizi collegati per gli archivi dati.
2.	Usare **Azure PowerShell** per creare tabelle e una pipeline. Il portale non supporta la creazione di tabelle e pipeline in questa versione


##Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto] [azure-purchase-options], [Offerte per i membri][azure-member-offers] o [Versione di valutazione gratuita][azure-free-trial].
- Scaricare e installare [Azure PowerShell][download-azure-powershell] nel computer.
- Leggere attentamente l'[introduzione al servizio Data factory di Azure][data-factory-introduction].
- Account di archiviazione di Azure In questa esercitazione si userà l'archiviazione BLOB come archivio dati di origine. Vedere [Informazioni sull'account di archiviazione][data-factory-create-storage] per la procedura di creazione di una risorsa di archiviazione di Azure.
- Database SQL di Azure. In questa esercitazione si creerà un database di esempio che verrà usato come archivio dati di destinazione. Vedere [Come creare e configurare un database SQL][data-factory-create-sql-database] per la procedura di creazione di un database SQL di Azure.

##Contenuto dell'esercitazione:

Passaggio | Descrizione
-----| -----------
[Passaggio 1: Creazione di un'istanza di Data factory di Azure](#CreateDataFactory) | In questo passaggio si creerà un'istanza di Data factory di Azure denominata **ADFTutorialDataFactory**. 
[Passaggio 2: Creazione di servizi collegati](#CreateLinkedServices) In questo passaggio si creeranno due servizi collegati: **MyBlobStore** e **MyAzureSQLStore**. MyBlobStore collega una risorsa di archiviazione Azure e MyAzureSQLStore collega un database SQL di Azure all'istanza di Data factory ADFTutorialDataFactory.
[Passaggio 3: Creazione di set di dati di input e di output](#CreateInputAndOutputDataSets) | In questo passaggio si definiranno due set di dati (**EmpTableFromBlob** e**EmpSQLTable**) usati come input e output per l'**attività di copia** nella pipeline ADFTutorialPipeline che verrà creata nel passaggio successivo.
[Passaggio 4: Creazione ed esecuzione di una pipeline](#CreateAndRunAPipeline) | In questo passaggio si creerà una pipeline denominata **ADFTutorialPipeline**. La pipeline avrà un'**attività di copia** che copia dati da un BLOB di Azure a una tabella di output del database di Azure.
[Passaggio 5: Monitoraggio dei set di dati e della pipeline](#MonitorDataSetsAndPipeline) | In questo passaggio si monitoreranno i set di dati e la pipeline usando Azure Management Studio.
 


## <a name="CreateDataFactory"></a>Passaggio 1: Creazione di un'istanza di Data factory di Azure
In questo passaggio è possibile usare il portale di anteprima di Azure per creare un'istanza di Data factory di Azure denominata **ADFTutorialDataFactory**.

1.	Dopo aver eseguito l'accesso al [portale di anteprima di Azure][azure-preview-portal], fare clic su **NUOVO** dall'angolo inferiore sinistro, quindi su **Data factory** nel pannello **Nuovo**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Se **Data factory** non è visualizzato nel pannello **Nuovo**, scorrere verso il basso.  


6. Nel pannello **Nuova istanza di Data factory**:
	1. Immettere **ADFTutorialDataFactory** come **nome**. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Fare clic su **NOME GRUPPO DI RISORSE** ed effettuare le seguenti operazioni:
		1. Fare clic su **Crea un nuovo gruppo di risorse**.
		2. Nel pannello **Crea gruppo di risorse** immettere **ADFTutorialResourceGroup** come **nome** del gruppo di risorse e fare clic su **OK**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato **ADFTutorialResourceGroup**. Se si usa un gruppo di risorse diverso, sarà necessario usare il gruppo di risorse selezionato al posto di ADFTutorialResourceGroup.  
7. Nel pannello **Nuova istanza di Data factory** si noti che **Aggiungi alla schermata iniziale** è selezionato.
8. Fare clic su **Crea** nel pannello **Nuova istanza di Data factory**.

	> [WACOM.NOTE] È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se si visualizza l'errore **Il nome dell'istanza di Data factory "ADFTutorialDataFactory" non è disponibile**, modificare il nome (ad esempio, yournameADFTutorialDataFactory). Durante l'esecuzione di passaggi in questa esercitazione usare questo nome anziché ADFTutorialFactory.  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. Fare clic sull'hub **NOTIFICHE** a sinistra e cercare le notifiche dal processo di creazione.
10. Dopo aver completato la creazione, viene visualizzato il pannello di Data factory come mostrato di seguito:
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. È inoltre possibile aprirlo dalla **Schermata iniziale** come illustrato di seguito facendo clic su **ADFTutorialFactory** 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>Passaggio 2: Creazione di servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere un'archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale.

In questo passaggio vengono creati due servizi collegati: **MyBlobStore** e **MyAzureSQLStore**. Il servizio collegato MyBlobStore collega un account di archiviazione Azure e MyAzureSQLStore collega un database SQL di Azure all'istanza di Data factory **ADFTutorialDataFactory**. Più avanti in questa esercitazione si creerà una pipeline che copia i dati da un contenitore BLOB in MyBlobStore in una tabella SQL in MyAzureSQLStore.

### Creazione di un servizio collegato per un account di archiviazione di Azure
1.	Nel pannello **DATA FACTORY** fare clic sul riquadro **Servizi collegati** per avviare il pannello **Servizi collegati**.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. Nel pannello **Servizi collegati** fare clic su **Aggiungi archivio dati**.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. Nel pannello **Nuovo archivio dati**:  
	1. Immettere un **nome** per l'archivio dati. Ai fini dell'esercitazione, immettere **MyBlobStore** come **nome**.
	2. Immettere la **descrizione** (facoltativa) per l'archivio dati.
	3. Fare clic su **Tipo**.
	4. Selezionare **Account di archiviazione di Azure** e fare clic su **OK**.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  A questo punto si visualizzerà di nuovo il pannello **Nuovo archivio dati** simile a quello riportato di seguito:

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Immettere il **Nome account** e la **Chiave account** per l'account di archiviazione di Azure, quindi fare clic su **OK**.   

6. Dopo aver fatto clic su **OK** nel pannello **Nuovo archivio dati**, si dovrebbe visualizzare **myblobstore** nell'elenco di **ARCHIVI DATI** del pannello **Servizi collegati**. Controllare l'hub **NOTIFICHE** sulla sinistra per eventuali messaggi.

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Creazione di un servizio collegato per un database SQL di Azure
1. Nel pannello **Servizi collegati** fare clic su **Aggiungi archivio dati** sulla barra degli strumenti per aggiungere un'altra origine dati (database SQL di Azure).
2. Nel pannello Nuovo archivio dati:
	1. Immettere un **nome** per l'archivio dati. Ai fini dell'esercitazione, immettere **MyAzureSQLStore** come **NOME**. 
	2. Immettere la **DESCRIZIONE (facoltativa)** per l'archivio.
	3. Fare clic su **Tipo** e selezionare **Database SQL di Azure**.
3. Immettere **Server**, **Database**, **Nome utente** e **Password** per il database SQL di Azure e fare clic su **OK**.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. Dopo aver fatto clic su **OK** nel pannello **Nuovo archivio dati**, si visualizzeranno entrambi gli archivi nel pannello **Servizi collegati**

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>Passaggio 3: Creazione di tabelle di input e di output

Nel passaggio precedente sono stati creati i servizi collegati **MyBlobStore** e **MyAzureSQLStore** per collegare un account di archiviazione di Azure e un database SQL di Azure all'istanza di Data factory: **ADFTutorialDataFactory**. In questo passaggio si creeranno le tabelle che rappresentano i dati di input e output per l'attività di copia nella pipeline che verrà creata nel passaggio successivo. 

Una tabella è un set di dati rettangolare ed è l'unico tipo di set di dati supportato in questa fase. La tabella di input fa riferimento a un contenitore BLOB nell'archiviazione di Azure a cui punta MyBlobStore e la tabella di output fa riferimento a una tabella SQL nel database SQL di Azure a cui punta MyAzureSQLStore.  
 
La creazione di set di dati e pipeline non è attualmente supportata dal portale di anteprima di Azure, quindi verranno usati i cmdlet di Azure PowerShell per creare tabelle e pipeline. Prima di creare le tabelle, è necessario eseguire le operazioni seguenti (dopo l'elenco è disponibile una procedura dettagliata).

* Creare un contenitore BLOB denominato **adftutorial** nel modulo di archiviazione BLOB di Azure a cui punta MyBlobStore. 
* Creare e caricare un file di testo **emp.txt** come BLOB per il contenitore **adftutorial**. 
* Creare una tabella denominata **emp** nel database SQL di Azure a cui punta MyAzureSQLStore.
* Creare una cartella denominata **ADFGetStarted** sul disco rigido.  

### Preparazione dell'archiviazione BLOB di Azure e del database SQL Azure per l'esercitazione
1. Avviare il Blocco note, incollare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\ADFGetStarted** sul disco rigido. 

        John, Doe
		Jane, Doe
				
2. Usare strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) per creare il contenitore **adftutorial** e per caricare il file **emp.txt** nel contenitore.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Usare il seguente script SQL per creare la tabella **emp** nel database SQL di Azure. Per connettersi a un database SQL di Azure ed eseguire script SQL è possibile usare Azure SQL Management Console o anche SQL Server Management Studio. 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	Per avviare Azure SQL Management Console, fare clic su **GESTISCI** come illustrato nella figura seguente:
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### Creazione della tabella di input 
Un tabella è un set di dati rettangolare che prevede uno schema. In questo passaggio si creerà una tabella denominata **EmpBlobTable** che punta a un contenitore BLOB nella risorsa di archiviazione di Azure rappresentato dal servizio collegato **MyBlobStore**.


1. Creare un file JSON per una tabella di data factory che rappresenta i dati nel file emp.txt nel blob.Avviare Blocco note, copiare lo script JSON seguente e salvarlo come EmpBlobTable.json nella cartella C:\ADFGetStarted.


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
            		"linkedServiceName": "MyBlobStore"
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
	
	- L'oggetto **type** di location è impostato su **AzureBlobLocation**.
	- **linkedServiceName** è impostato su **MyBlobStore**. Questo servizio collegato è stato creato nel passaggio 2).
	- **folderPath** è impostato sul contenitore **adftutorial**. È anche possibile specificare il nome di un BLOB all'interno della cartella. Poiché non si specifica il nome del BLOB, i dati da tutti i BLOB nel contenitore sono considerati come dati di input.  
	- L'oggetto **type** di format è impostato su **TextFormat**
	- - Nel file di testo sono presenti due campi, **FirstName** e **ColumnName**, separati da una virgola (columDelimiter)	
	- L'oggetto **availability** è impostato su **hourly** (l'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**). Il servizio Data factory cercherà i dati di input ogni ora nella cartella radice nel contenitore BLOB (**adftutorial**) specificato.

	Se non si specifica un oggetto **fileName** per una **tabella di input**, tutti i file/BLOB della cartella di input (**folderPath**) vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Per alcuni esempi, vedere i file nell'[esercitazione][adf-tutorial].
 
	Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà partitionedBy. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Ad esempio, se una sezione viene generata per 2014-10-20T08:00:00, folderName è impostato su wikidatagateway/wikisampledataout/2014/10/20 e fileName è impostato su 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Per dettagli sulle proprietà JSON, vedere il [riferimento agli script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Avviare **Azure PowerShell** ed eseguire il seguente comando per passare in modalità **AzureResourceManager**. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- Eseguire **Add-AzureAccount** e immettere il nome utente e la password usati per accedere al portale di anteprima di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione che si desidera usare. La sottoscrizione deve corrispondere a quella usata nel portale di anteprima di Azure.

3. Usare il cmdlet **New-AzureDataFactoryTable** per creare la tabella di input con il file **EmpBlobTable.json**.


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	Vedere la pagina relativa al [riferimento ai cmdlet di Data factory][cmdlet-reference] per informazioni dettagliate su questo e altri cmdlet di Data factory.
 
### Creazione della tabella di output
In questa parte del passaggio, si creerà una tabella di output denominata **EmpSQLTable** che punta a una tabella SQL nel database SQL di Azure rappresentata dal servizio collegato **MyAzureSQLStore**. 

1. Creare un file JSON per una tabella di Data factory che rappresenta i dati nel database SQL di Azure. Avviare il Blocco note, copiare il seguente script JSON e salvarlo come **EmpSQLTable.json** nella cartella **C:\ADFGetStarted**.



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
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Tenere presente quanto segue: 
	
	* L'oggetto **type** di location è impostato su **AzureSQLTableLocation**.
	* **linkedServiceName** è impostato su **MyAzureSQLStore** (questo servizio collegato è stato creato nel passaggio 2).
	* **tablename** è impostato su **emp**.
	* Sono presenti tre colonne: **ID**, **FirstName** e **LastName** nella tabella emp nel database, ma ID è una colonna di identità, pertanto è necessario specificare solo **FirstName** e **LastName**.
	* L'oggetto **availability** è impostato su **hourly** (l'oggetto frequency è impostato su hour e l'oggetto interval è impostato su 1).  Il servizio Data factory genererà una porzione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure.


2. In **Azure PowerShell** eseguire il seguente comando per creare un'altra tabella di Data factory per rappresentare la tabella **emp** nel database SQL di Azure.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>Passaggio 4: Creazione ed esecuzione di una pipeline
In questo passaggio, è possibile creare una pipeline con un'**attività di copia** che usa **EmpTableFromBlob** come input e **EmpSQLTable** come output.

1. Creare un file JSON per la pipeline. Avviare il Blocco note, copiare il seguente script JSON e salvarlo come **ADFTutorialPipeline.json** nella cartella **C:\ADFGetStarted**.


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
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
        		]
      		}
		} 

	Tenere presente quanto segue:

	- Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **CopyActivity**.
	- L'input per l'attività è impostato su **EmpTableFromBlob** e l'output per l'attività è impostato su **EmpSQLTable**.
	- Nella sezione **transformation** **BlobSource** viene specificato come tipo di origine e **SqlSink** come tipo di sink.

2. Usare il cmdlet **New-AzureDataFactoryPipeline** per creare una pipeline con il file **ADFTutorialPipeline.json** creato.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. Una volta create le pipeline, è possibile specificare la durata dell'elaborazione dati. Specificando il periodo attivo per una pipeline, si definisce quanto tempo durerà l'elaborazione delle sezioni di dati in base alle proprietà della disponibilità definite per ogni tabella di Data factory di Azure.  Eseguire il seguente comando PowerShell per impostare il periodo attivo nella pipeline e immettere Y per confermare. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] Sostituire il valore di **StartDateTime** con il giorno corrente e il valore di **EndDateTime** con il giorno seguente. StartDateTime ed EndDateTime sono orari UTC e devono essere in formato (http://en.wikipedia.org/wiki/ISO_8601)[ISO]. Ad esempio: 2014-10-14T16:32:41Z. Il valore **EndDateTime** è facoltativo, ma in questa esercitazione verrà usato. 
	> Se non si specifica **EndDateTime**, viene calcolato come "**StartDateTime + 48 ore**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come **EndDateTime**.  
	
	Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.

4. Nel **Portale di Azure**, nel pannello **DATA FACTORY** per **ADFTutorialDataFactory** fare clic su **Diagramma**.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. Viene visualizzato un diagramma simile al seguente: Fare doppio clic su un titolo per visualizzare i dettagli sull'elemento rappresentato dal pannello.

	![Diagram view][image-data-factory-get-started-diagram-blade]

**Operazione riuscita.** Sono stati creati correttamente un'istanza di Data factory di Azure, i servizi collegati, le tabelle e una pipeline e la pipeline è stata pianificata.

## <a name="MonitorDataSetsAndPipeline"></a>Passaggio 5: Monitoraggio dei set di dati e della pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Data factory di Azure. È anche possibile usare i cmdlet di PowerShell per monitorare i set di dati e le pipeline. Per dettagli sull'uso dei cmdlet per il monitoraggio, vedere la pagina relativa a [monitoraggio e gestione di Data factory con i cmdlet di PowerShell][monitor-manage-using-powershell].

1. Passare al [Portale di Azure (anteprima)][azure-preview-portal] se non è già aperto. 
2. Se il pannello per **ADFTutorialDataFactory** non è aperto, aprirlo facendo clic su **ADFTutorialDataFactory** nella **Schermata iniziale**. 
3. Viene visualizzato il numero e i nomi delle tabelle e delle pipeline create nel pannello.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. A questo punto fare clic sul riquadro **Set di dati**.
5. Nel pannello **Set di dati** fare clic su **EmpTableFromBlob**. Questa è la tabella di input per **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Si noti che le sezioni di dati fino all'ora corrente sono già state prodotte e sono nello stato **Ready** perché il file **emp.txt** è sempre presente nel contenitore BLOB: **adftutorial\input**. Verificare che non sia visualizzata alcuna sezione in **Sezioni problematiche** nella parte inferiore della pagina.
6. Nel pannello **Set di dati** fare clic su **EmpSQLTable**. Questa è la tabella di output per **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. Verrà visualizzato il pannello **EmpSQLTable**, come illustrato di seguito:

	![table blade][image-data-factory-get-started-table-blade]
 
7. Le sezioni di dati fino all'ora corrente sono state già generate e sono in stato **Ready**. Non ci sono sezioni visualizzate nella sezione **Sezioni problematiche** nella parte inferiore della pagina.
8. Fare clic su **... (puntini di sospensione)** per visualizzare tutte le sezioni.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Fare clic su una qualsiasi sezione di dati dell'elenco per visualizzare il pannello **SEZIONE DI DATI**.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. Nel pannello **SEZIONE DI DATI** saranno visibili tutte le esecuzioni di attività nell'elenco in basso. Fare clic su un'**esecuzione attività** per visualizzare il pannello **DETTAGLI ESECUZIONE ATTIVITÀ**. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Fare clic su **X** per chiudere tutti i pannelli finché non viene visualizzato il pannello iniziale per **ADFTutorialDataFactory**.
14. (facoltativo) Fare clic su **pipeline** nella home page per **ADFTutorialDataFactory**, quindi su **ADFTutorialPipeline** nel pannello **Pipeline** ed eseguire il drill-through delle tabelle di input (**Utilizzate**) o di output (**Prodotte**).
15. Avviare **SQL Server Management Studio**, connettersi al database SQL di Azure e verificare che le righe vengano inserite nella tabella **emp** nel database.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Riepilogo 
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. Nel portale di anteprima di Azure è stata creata l'istanza di Data factory e sono stati collegati i servizi. I cmdlet PowerShell di Azure sono stati usati per creare tabelle e una pipeline e quindi pianificare la pipeline. Ecco i passaggi di alto livello eseguiti in questa esercitazione:  

1.	Creare un'istanza di **Data factory** di Azure.
2.	Creare **servizi collegati** che collegano gli archivi dati e i calcoli (definiti come **servizi collegati**) all'istanza di Data factory.
3.	Creare **tabelle** che descrivono dati di input e dati di output per le pipeline.
4.	Creare **pipeline**. Una pipeline è costituita da una o più attività ed elabora gli input producendo l'output. 
5.	Specificare il **periodo attivo** delle pipeline per l'elaborazione dati. Il periodo attivo definisce l'intervallo di tempo in cui verranno prodotte sezioni di dati.

## Passaggi successivi

Articolo | Descrizione
------ | ---------------
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una **procedura dettagliata end-to-end** che mostra come implementare uno **scenario quasi reale** usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Copiare i dati con Data factory di Azure - Attività di copia][copy-activity] | Questo articolo fornisce una descrizione dettagliata dell'**attività di copia** usata in questa esercitazione. 
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un **database di SQL Server locale** a un BLOB di Azure.
[Usare Pig e Hive con Data factory][use-pig-and-hive-with-data-factory] | Questo articolo contiene una procedura dettagliata che mostra come usare l'**attività HDInsight** per eseguire uno script **hive/pig** per elaborare i dati di input in modo da generare i dati di output. 
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'**attività personalizzata** e usarla in una pipeline. 
[Monitorare e gestire Data factory di Azure con PowerShell][monitor-manage-using-powershell] | Questo articolo descrive come **monitorare e gestire** un'istanza di Data factory di Azure con i **cmdlet di Azure PowerShell**. È possibile provare gli esempi dell'articolo in ADFTutorialDataFactory.
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come **risolvere i problemi** di Data factory di Azure. È possibile provare la procedura dettagliata di questo articolo in ADFTutorialDataFactory introducendo un errore (eliminando la tabella nel database SQL di Azure). 
[Informazioni di riferimento per i cmdlet di Data factory di Azure][cmdlet-reference] | Questo contenuto di riferimento include i dettagli su tutti i **cmdlet di Data factory**.
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
