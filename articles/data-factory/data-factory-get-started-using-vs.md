<properties 
	pageTitle="Esercitazione: Creare una pipeline con l’attività Copia utilizzando Visual Studio" 
	description="In questa esercitazione si creerà una pipeline di Data factory di Azure con un'attività Copia utilizzando Visual Studio." 
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
	ms.date="09/09/2015" 
	ms.author="spelluru"/>

# Esercitazione: Creare e monitorare una data factory mediante Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##Contenuto dell'esercitazione:
In questa esercitazione verranno effettuate le operazioni seguenti utilizzando Visual Studio 2013:

1. Creare due servizi collegati: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. AzureStorageLinkedService1 collega una risorsa di archiviazione di Azure e AzureSqlLinkedService1 collega un database SQL di Azure alla data factory **ADFTutorialDataFactoryVS**. I dati di input per la pipeline si trovano in un contenitore BLOB nell'archivio BLOB di Azure e i dati di output verranno archiviati in una tabella nel database SQL di Azure. Questi due archivi dati vengono quindi aggiunti alla data factory come servizi collegati.
2. Creare due tabelle di data factory, ovvero **EmpTableFromBlob** e **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati. Per EmpTableFromBlob verrà specificato il contenitore BLOB che include un BLOB con i dati di origine, mentre per EmpSQLTable verrà specificata la tabella SQL in cui verranno archiviati i dati di output. Verranno specificate anche altre proprietà, ad esempio la struttura e la disponibilità dei dati e così via.
3. Creare una pipeline denominata **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. La pipeline includerà un'**attività di copia** che copia i dati di input dal BLOB di Azure e li inserisce nella tabella di output SQL di Azure.
4. Creare una data factory e distribuire servizi collegati, tabelle e la pipeline.    

## Creare e distribuire entità della data factory usando Visual Studio 

### Prerequisiti
Nel computer deve essere installato Visual Studio 2013. Scaricare Azure SDK per Visual Studio 2013. Passare alla [pagina di download di Azure](http://azure.microsoft.com/downloads/) e fare clic su **VS 2013 - Installazione** nella sezione **.NET**.

### Procedura dettagliata

#### Creare il progetto di Visual Studio 
1. Avviare **Visual Studio 2013**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Si dovrebbe vedere la finestra di dialogo **Nuovo progetto**.  
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**. Se il modello DataFactory non è visibile, chiudere Visual Studio, installare Azure SDK per Visual Studio 2013 e riaprire Visual Studio.  

	![Finestra di dialogo Nuovo progetto](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Immettere un **nome** per il progetto, un **percorso** e un nome per la **soluzione** e fare clic su **OK**.

	![Esplora soluzioni](./media/data-factory-get-started-using-vs/solution-explorer.png)

#### Creazione di servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere un'archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale.

In questo passaggio verranno creati due servizi collegati: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. Il servizio collegato AzureStorageLinkedService1 collega un account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure alla data factory **ADFTutorialDataFactory**.

##### Creare il servizio collegato di archiviazione di Azure

4. Fare clic con il pulsante destro del mouse su **Servizi collegati** in Esplora soluzioni, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.      
5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** nell'elenco e fare clic su **Aggiungi**. 

	![Nuovo servizio collegato](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure.

	![Servizio collegato di archiviazione di Azure](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Salvare il file **AzureStorageLinkedService1.json**.

#### Creare il servizio collegato SQL di Azure

5. Fare nuovamente clic con il pulsante destro del mouse sul nodo **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
6. Questa volta selezionare **Servizio collegato SQL Azure** e fare clic su **Aggiungi**. 
7. Nel **file AzureSqlLinkedService1.json** sostituire **servername**, **databasename**, ****username@servername**, e **password** con i nomi del server, database, account utente e password SQL di Azure.
8.  Salvare il file **AzureSqlLinkedService1.json**. 


### Creazione di tabelle di input e di output
Nel passaggio precedente sono stati creati i servizi collegati **AzureStorageLinkedService1** e **AzureSqlLinkedService1** per collegare un account di archiviazione di Azure e un database SQL di Azure alla data factory: **ADFTutorialDataFactory**. In questo passaggio verranno definite due tabelle di data factory, ovvero **EmpTableFromBlob** e **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService1 e AzureSqlLinkedService1. Per EmpTableFromBlob verrà specificato il contenitore BLOB che include un BLOB con i dati di origine e per EmpSQLTable verrà specificata la tabella SQL in cui verranno archiviati i dati di output.

#### Creare la tabella di input

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

#### Creare la tabella di output

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

#### Creare la pipeline 
Finora sono stati creati i servizi collegati e le tabelle di input/output. Ora verrà creata una pipeline con un'**Attività di copia** per copiare i dati dal BLOB di Azure al database SQL di Azure.


1. Fare clic con il pulsante destro del mouse su **Pipeline** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.  
15. Selezionare **Copia pipeline dati** nella finestra di dialogo **Aggiungi nuovo elemento** e fare clic su **Aggiungi**. 
16. Sostituire il testo JSON con il testo JSON seguente e salvare il file **CopyActivity1.json**.
			
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

#### Pubblicare/Distribuire le entità della data factory
  
18. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**. 
19. Se viene visualizzato **Accedere al proprio account Microsoft** nella finestra di dialogo immettere le credenziali per l'account che dispone di sottoscrizione di Azure e fare clic su **accedi**.
20. Verrà visualizzata la finestra di dialogo seguente:

	![Finestra di dialogo Pubblica](./media/data-factory-get-started-using-vs/publish.png)

21. Nella pagina Configura data factory, procedere come segue:
	1. Selezionare l’opzione**Crea nuova data factory**.
	2. Immettere **VSTutorialFactory** per **Nome**.
	3. Selezionare la sottoscrizione adatta per il campo **Sottoscrizione**. 
	4. Selezionare il **gruppo di risorse** per la data factory da creare. 
	5. Selezionare l’**area** per la data factory. 
	6. Fare clic su **Avanti** per passare alla pagina **Pubblica elementi**. 
23. Nella pagina **Pubblica elementi**, assicurarsi che tutte le data factory siano selezionate e fare clic su **Avanti** per passare alla pagina **Riepilogo**.     
24. Rivedere il riepilogo e fare clic su **Avanti** per avviare il processo di distribuzione e visualizzare lo **Stato della distribuzione**.
25. Nella pagina **Stato della distribuzione**, è possibile visualizzare lo stato del processo di distribuzione. Fare clic su Fine dopo il termine della distribuzione. 


## Usare Esplora Server per esaminare le entità della data factory

1. In **Visual Studio** fare clic su **Visualizza** nel menu e fare clic su **Esplora server**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzata la finestra **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione verrà visualizzato nella **finestra **Elenco attività data factory![Esplora server](./media/data-factory-get-started-using-vs/server-explorer.png).
3. È possibile fare clic con il pulsante destro del mouse su una data factory e scegliere l'opzione per esportare la data factory in un nuovo progetto per creare un progetto di Visual Studio basato su una data factory esistente. ![Esportare la data factory in un progetto VS](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Aggiornare gli strumenti di Data factory di Azure per Visual Studio
Per aggiornare gli strumenti di Data Factory di Azure per Visual Studio, eseguire le operazioni seguenti:

1. Fare clic su**Strumenti**nel menu e selezionare**Estensioni e aggiornamenti**. 
2. Selezionare **Aggiornamenti** nel riquadro sinistro e quindi selezionare **Visual Studio Gallery**.
4. Selezionare **Strumenti di Data Factory di Azure per Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento. 

Vedere [Monitorare i set di dati e la pipeline](data-factory-get-started-using-editor.md/#MonitorDataSetsAndPipeline) per istruzioni su come usare il portale di anteprima di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione.


## Invia commenti e suggerimenti
I commenti e i suggerimenti su questo articolo possono essere molto utili. L'invio di commenti e suggerimenti tramite [posta elettronica](mailto:adfdocfeedback@microsoft.com?subject=data-factory-get-started-using-vs.md) richiede solo alcuni minuti.

<!---HONumber=Sept15_HO2-->