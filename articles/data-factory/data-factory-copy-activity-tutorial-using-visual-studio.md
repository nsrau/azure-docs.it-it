<properties 
	pageTitle="Esercitazione: Creare una pipeline con l'attività di copia usando Visual Studio | Microsoft Azure" 
	description="In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando Visual Studio." 
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
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# Esercitazione: Creare una pipeline con l’attività Copia utilizzando Visual Studio
> [AZURE.SELECTOR]
- [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Copia guidata](data-factory-copy-data-wizard-tutorial.md)

Questa esercitazione mostra come creare e monitorare un'istanza di Azure Data Factory con Visual Studio. La pipeline nella data factory usa un'attività di copia per copiare i dati dall'archivio BLOB di Azure al database SQL di Azure.

Di seguito sono elencati i passaggi da eseguire in questa esercitazione:

1. Creare due servizi collegati: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. AzureStorageLinkedService1 collega una risorsa di archiviazione di Azure e AzureSqlLinkedService1 collega un database SQL di Azure alla data factory **ADFTutorialDataFactoryVS**. I dati di input per la pipeline si trovano in un contenitore BLOB nell'archivio BLOB di Azure e i dati di output vengono archiviati in una tabella nel database SQL di Azure. Questi due archivi dati vengono quindi aggiunti alla data factory come servizi collegati.
2. Creare due tabelle di data factory, ovvero **EmpTableFromBlob** e **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati. Per EmpTableFromBlob, specificare il contenitore BLOB che contiene un BLOB con i dati di origine. Per EmpSQLTable, specificare la tabella SQL che contiene i dati di output. Specificare anche altre proprietà come struttura, disponibilità e criteri.
3. Creare una pipeline denominata **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. La pipeline include un'**attività di copia** che copia i dati di input dal BLOB di Azure e li inserisce nella tabella di output di Azure SQL. L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md).
4. Creare una data factory e distribuire servizi collegati, tabelle e la pipeline.

## Prerequisiti

1. Vedere la [panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ed eseguire i passaggi relativi ai **prerequisiti**.
2. Per potere pubblicare entità di Data Factory in Azure Data Factory, è necessario essere un **amministratore della sottoscrizione di Azure**.
3. È necessario disporre dei seguenti prodotti installati nel computer in uso:
	- Visual Studio 2013 o Visual Studio 2015
	- Download di Azure SDK per Visual Studio 2013 o Visual Studio 2015. Passare alla [pagina di download di Azure](https://azure.microsoft.com/downloads/) e fare clic su **VS 2013** o **VS 2015** nella sezione **.NET**.
	- Scaricare il plug-in Azure Data Factory più recente per Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [2015 VS](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se si usa Visual Studio 2013, è anche possibile aggiornare il plug-in nel modo seguente: nel menu fare clic su **Strumenti** -> **Estensioni e aggiornamenti** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools for Visual Studio** (Strumenti di Azure Data Factory per Visual Studio)-> **Aggiorna**.
 


## Creare un progetto di Visual Studio 
1. Avviare **Visual Studio 2013**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Si dovrebbe vedere la finestra di dialogo **Nuovo progetto**.
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**. Se il modello DataFactory non è visibile, chiudere Visual Studio, installare Azure SDK per Visual Studio 2013 e riaprire Visual Studio.

	![Finestra di dialogo Nuovo progetto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png) 

3. Immettere un **nome** per il progetto, un **percorso** e un nome per la **soluzione** e fare clic su **OK**.

	![Esplora soluzioni](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png) 

## Creazione di servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere una risorsa di Archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale.

In questo passaggio vengono creati due servizi collegati: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. Il servizio collegato AzureStorageLinkedService1 collega un account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure alla data factory **ADFTutorialDataFactory**.

### Creare il servizio collegato di archiviazione di Azure

4. Fare clic con il pulsante destro del mouse su **Servizi collegati** in Esplora soluzioni, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** nell'elenco e fare clic su **Aggiungi**.

	![Nuovo servizio collegato](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure.

	![Servizio collegato Archiviazione di Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png) 

4. Salvare il file **AzureStorageLinkedService1.json**.

### Creare il servizio collegato SQL di Azure

5. Fare nuovamente clic con il pulsante destro del mouse sul nodo **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
6. Questa volta selezionare **Servizio collegato SQL Azure** e fare clic su **Aggiungi**.
7. Nel file **AzureSqlLinkedService1.json** sostituire **servername**, **databasename**, **username@servername** e **password** con i nomi del server, database, account utente e password di Azure SQL.
8.  Salvare il file **AzureSqlLinkedService1.json**.


## Creare set di dati
Nel passaggio precedente sono stati creati i servizi collegati **AzureStorageLinkedService1** e **AzureSqlLinkedService1** per collegare un account di archiviazione di Azure e un database SQL di Azure alla data factory **ADFTutorialDataFactory**. In questo passaggio vengono definite due tabelle di Data Factory, ovvero **EmpTableFromBlob** ed **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService1 e AzureSqlLinkedService1. Per EmpTableFromBlob, specificare il contenitore BLOB che contiene un BLOB con i dati di origine. Per EmpSQLTable, specificare la tabella SQL che contiene i dati di output.

### Creare set di dati di input

9. Fare clic con il pulsante destro del mouse su **Tabelle** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
10. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **BLOB di Azure** e fare clic su **Aggiungi**.
10. Sostituire il testo JSON con il testo seguente e salvare il file **AzureBlobLocation1.json**.

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
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
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

### Creare il set di dati di output

11. Fare nuovamente clic con il pulsante destro del mouse su **Tabelle** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
12. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Azure SQL** e fare clic su **Aggiungi**.
13. Sostituire il testo JSON con il testo JSON seguente e salvare il file **AzureSqlTableLocation1.json**.

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
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## Creare una pipeline 
Finora sono stati creati i servizi collegati e le tabelle di input/output. Creare ora una pipeline con un'**attività di copia** per copiare i dati dal BLOB di Azure al database SQL di Azure.


1. Fare clic con il pulsante destro del mouse su **Pipeline** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
15. Selezionare **Copia pipeline dati** nella finestra di dialogo **Aggiungi nuovo elemento** e fare clic su **Aggiungi**.
16. Sostituire il codice JSON con il codice JSON seguente e salvare il file **CopyActivity1.json**.
			
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
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
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
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

## Pubblicare/Distribuire le entità della data factory
  
18. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**.
19. Se viene visualizzato **Sign in to your Microsoft account** (Accedere all'account Microsoft), nella finestra di dialogo immettere le credenziali per l'account associato alla sottoscrizione di Azure e fare clic su **Accedi**.
20. Verrà visualizzata la finestra di dialogo seguente:

	![Finestra di dialogo Pubblica](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)  

21. Nella pagina Configure data factory (Configura data factory), procedere come segue:
	1. Selezionare l'opzione **Crea nuova data factory**.
	2. Immettere **VSTutorialFactory** per **Nome**.
	
		> [AZURE.NOTE]  
		È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se si riceve un messaggio di errore riguardante il nome della data factory durante la pubblicazione, è possibile modificare il nome della data factory, ad esempio, nomeutenteVSTutorialFactory, e provare di nuovo ad effettuare la pubblicazione. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md).
		
	3. Selezionare la sottoscrizione adatta per il campo **Sottoscrizione**.
	4. Selezionare il **gruppo di risorse** per la data factory da creare.
	5. Selezionare l'**area** per la data factory.
	6. Fare clic su **Avanti** per passare alla pagina **Pubblica elementi**.
23. Nella pagina **Pubblica elementi**, assicurarsi che tutte le data factory siano selezionate e fare clic su **Avanti** per passare alla pagina **Riepilogo**.
24. Esaminare il riepilogo e fare clic su **Avanti** per avviare il processo di distribuzione e visualizzare lo **Stato della distribuzione**.
25. Nella pagina **Stato della distribuzione**, è possibile visualizzare lo stato del processo di distribuzione. Fare clic su Finish (Fine) dopo il termine della distribuzione.

Tenere presente quanto segue:

- Se viene visualizzato l'errore "**La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory**", eseguire una di queste operazioni e provare a ripetere la pubblicazione:

	- In Azure PowerShell eseguire questo comando per registrare il provider di Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		È possibile eseguire questo comando per verificare che il provider di Data Factory sia registrato.
	
			Get-AzureRmResourceProvider
	- Accedere usando la sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com) e passare al pannello Data Factory oppure creare un'istanza di Data Factory nel portale di Azure. Questa azione registra automaticamente il provider.
- 	Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.
- 	Per creare istanze di data factory, è necessario essere un collaboratore/amministratore della sottoscrizione di Azure.

## Riepilogo
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. È stato usato Visual Studio per creare la data factory, i servizi collegati, i set di dati e una pipeline. Ecco i passaggi generali eseguiti in questa esercitazione:

1.	Creare un'istanza di Azure **Data Factory**.
2.	Creare **servizi collegati**:
	1. Un servizio collegato di **Archiviazione di Azure** per collegare l'account di archiviazione di Azure che include i dati di input.
	2. Un servizio collegato di **Azure SQL** per collegare il database SQL di Azure che contiene i dati di output.
3.	Creare **set di dati** che descrivono dati di input e dati di output per le pipeline.
4.	Creare una **pipeline** con un'**attività di copia** con **BlobSource** come origine e **SqlSink** come sink.


## Usare Esplora Server per visualizzare le data factory

1. In **Visual Studio** fare clic su **Visualizza** nel menu e fare clic su **Esplora server**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzata la finestra **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione viene visualizzato nella finestra **Data Factory Task List** (Elenco attività Data Factory).![Esplora server](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. È possibile fare clic con il pulsante destro del mouse su una data factory e scegliere l'opzione per esportare la data factory in un nuovo progetto per creare un progetto di Visual Studio basato su una data factory esistente. ![Esportare la data factory in un progetto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)

## Aggiornare gli strumenti di Data factory di Azure per Visual Studio
Per aggiornare gli strumenti di Azure Data Factory per Visual Studio, eseguire queste operazioni:

1. Fare clic su**Strumenti**nel menu e selezionare**Estensioni e aggiornamenti**.
2. Selezionare **Aggiornamenti** nel riquadro sinistro e quindi selezionare **Visual Studio Gallery**.
4. Selezionare **Strumenti di Data factory di Azure per Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento.

Per istruzioni su come usare il portale di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline).

## Vedere anche
| Argomento | Description |
| :---- | :---- |
| [Attività di spostamento dei dati](data-factory-data-movement-activities.md) | Questo articolo fornisce informazioni dettagliate sull'attività di copia usata nell'esercitazione. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo fornisce informazioni sulle pipeline e le attività in Azure Data Factory. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo fornisce informazioni sui set di dati in Azure Data Factory.
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. 

<!---HONumber=AcomDC_0921_2016-->