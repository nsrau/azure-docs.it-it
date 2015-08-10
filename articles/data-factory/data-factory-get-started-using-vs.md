<properties 
	pageTitle="Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL" 
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
	ms.date="07/27/2015" 
	ms.author="spelluru"/>

# Esercitazione: Creare e monitorare una data factory mediante Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


##Contenuto dell'esercitazione:
In questa esercitazione prima si creerà una data factory di Azure denominata **ADFTutorialDataFactoryVS** usando il portale di anteprima di Azure e quindi si eseguiranno le operazioni seguenti usando Visual Studio 2013:

1. Creare due servizi collegati: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. AzureStorageLinkedService1 collega una risorsa di archiviazione di Azure e AzureSqlLinkedService1 collega un database SQL di Azure alla data factory **ADFTutorialDataFactoryVS**. I dati di input per la pipeline si trovano in un contenitore BLOB nell'archivio BLOB di Azure e i dati di output verranno archiviati in una tabella nel database SQL di Azure. Questi due archivi dati vengono quindi aggiunti alla data factory come servizi collegati.
2. Creare due tabelle di data factory, ovvero **EmpTableFromBlob** e **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati. Per EmpTableFromBlob verrà specificato il contenitore BLOB che include un BLOB con i dati di origine, mentre per EmpSQLTable verrà specificata la tabella SQL in cui verranno archiviati i dati di output. Verranno specificate anche altre proprietà, ad esempio la struttura e la disponibilità dei dati e così via.
3. Creare una pipeline denominata **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. La pipeline includerà un'**attività di copia** che copia i dati di input dal BLOB di Azure e li inserisce nella tabella di output SQL di Azure. 


## <a name="CreateDataFactory"></a>Creare una data factory di Azure.
In questo passaggio è possibile usare il portale di anteprima di Azure per creare un'istanza di data factory di Azure denominata **ADFTutorialDataFactoryVS**.

1.	Dopo l'accesso al [portale di anteprima di Azure](http://portal.azure.com), fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **Analisi dei dati** nel pannello **Crea** e infine fare clic su **Data factory** nel pannello **Analisi dei dati**. 

	![Nuovo->Data factory](./media/data-factory-get-started-using-vs/NewDataFactoryMenu.png)

6. Nel pannello **Nuova data factory**:
	1. Immettere **ADFTutorialDataFactoryVS** come **nome**. 
	
  		![Pannello Nuova data factory](./media/data-factory-get-started-using-vs/getstarted-new-data-factory.png)
	2. Fare clic su **NOME DEL GRUPPO DI RISORSE** ed eseguire queste operazioni:
		1. Fare clic su **Crea un nuovo gruppo di risorse**.
		2. Nel pannello **Crea gruppo di risorse** immettere **ADFTutorialResourceGroup** come **nome** del gruppo di risorse e fare clic su **OK**. 

			![Crea gruppo di risorse](./media/data-factory-get-started-using-vs/CreateNewResourceGroup.png)

		Alcuni dei passaggi di questa esercitazione presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](resource-group-overview.md).  
7. Si noti che l'opzione **Aggiungi a Schermata iniziale** è selezionata nel pannello **Nuova data factory**.
8. Fare clic su **Crea** nel pannello **Nuova data factory**.

	È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato un errore simile a **Nome "ADFTutorialDataFactoryVS" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteADFTutorialDataFactoryVS) e provare di nuovo a crearla. Durante l'esecuzione dei passaggi rimanenti in questa esercitazione usare questo nome anziché ADFTutorialFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento [Data factory - Regole di denominazione][data-factory-naming-rules] relativo alle regole di denominazione degli elementi di data factory.
	 
	![Nome di data factory non disponibile](./media/data-factory-get-started-using-vs/getstarted-data-factory-not-available.png)

9. Fare clic sull'hub **NOTIFICHE** a sinistra e cercare le notifiche del processo di creazione. Fare clic su **X** per chiudere il pannello **NOTIFICHE**, se è aperto.
10. Al termine della creazione, il pannello **DATA FACTORY** avrà un aspetto simile al seguente.

    ![Home page di Data factory](./media/data-factory-get-started-using-vs/getstarted-data-factory-home-page.png)

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
 
3. Sostituire **<accountname>** e **<accountkey>** con il nome e la chiave dell'account di archiviazione di Azure.

	![Servizio collegato di archiviazione di Azure](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Salvare il file **AzureStorageLinkedService1.json**.

#### Creare il servizio collegato SQL di Azure

5. Fare nuovamente clic con il pulsante destro del mouse sul nodo **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
6. Questa volta selezionare **Servizio collegato SQL Azure** e fare clic su **Aggiungi**. 
7. Nel **file AzureSqlLinkedService1.json** sostituire **servername**, **databasename**, **username@servername** e **password** con i nomi del server, database, account utente e password SQL di Azure. 8.  Salvare il file **AzureSqlLinkedService1.json**. 


### Creazione di tabelle di input e di output
Nel passaggio precedente sono stati creati i servizi collegati **AzureStorageLinkedService1** e **AzureSqlLinkedService1** per collegare un account di archiviazione di Azure e un database SQL di Azure alla data factory **ADFTutorialDataFactory**. In questo passaggio verranno definite due tabelle di data factory, ovvero **EmpTableFromBlob** e **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService1 e AzureSqlLinkedService1. Per EmpTableFromBlob verrà specificato il contenitore BLOB che include un BLOB con i dati di origine e per EmpSQLTable verrà specificata la tabella SQL in cui verranno archiviati i dati di output.

#### Creare la tabella di input

9. Fare clic con il pulsante destro del mouse su **Tabelle** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
10. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **BLOB di Azure** e fare clic su **Aggiungi**.   
10. Sostituire il testo JSON con il testo seguente e salvare il file **AzureBlobLocation1.json**. 

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
	                "linkedServiceName": "AzureStorageLinkedService1"
	            },
	            "availability": 
	            {
	                "frequency": "Hour",
	                "interval": 1,
	                "waitOnExternal": {}
                }
	        }
		}

#### Creare la tabella di output

11. Fare nuovamente clic con il pulsante destro del mouse su **Tabelle** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
12. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Azure SQL** e fare clic su **Aggiungi**. 
13. Sostituire il testo JSON con il testo JSON seguente e salvare il file **AzureSqlTableLocation1.json**.

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
		            "type": "AzureSqlTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService1"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

#### Creare la pipeline 
Finora sono stati creati i servizi collegati e le tabelle di input/output. Ora verrà creata una pipeline con un'**attività di copia** per copiare i dati dal BLOB di Azure al database SQL di Azure.


1. Fare clic con il pulsante destro del mouse su **Pipeline** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.  
15. Selezionare **Copia pipeline dati** nella finestra di dialogo **Aggiungi nuovo elemento** e fare clic su **Aggiungi**. 
16. Sostituire il testo JSON con il testo JSON seguente e salvare il file **CopyActivity1.json**.
			
		 {
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from a blob to Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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
		                        "type": "SqlSink",
		                        "writeBatchSize": 10000,
		                        "writeBatchTimeout": "60:00:00"
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
		
		        "start": "2015-07-12T00:00:00Z",
		        "end": "2015-07-13T00:00:00Z",
		        "isPaused": false
		    }
		} 

#### Pubblicare/Distribuire le entità della data factory
  
18. Nell'area della barra degli strumenti fare clic con il pulsante destro del mouse e scegliere **Data factory** per abilitare la barra degli strumenti Data factory, se necessario. 
19. Nella **barra degli strumenti Data factory** fare clic sulla **casella di riepilogo** per visualizzare tutte le data factory nella sottoscrizione di Azure. Se viene visualizzata la finestra di dialogo **Accedi a Visual Studio**: 
	20. Immettere l'**account di posta elettronica** associato alla sottoscrizione di Azure in cui si vuole creare la data factory, immettere la **password** e fare clic su **Accedi**.
	21. Una volta completato l'accesso, si dovrebbero vedere tutte le data factory nella sottoscrizione di Azure. In questa esercitazione verrà creata una nuova data factory.       
22. Nell'elenco a discesa selezionare **ADFTutorialFactoryVS** e fare clic sul pulsante **Pubblica** per distribuire/pubblicare i servizi collegati, i set di dati e la pipeline.    

	![Pulsante Publish](./media/data-factory-get-started-using-vs/publish.png)

23. Si dovrebbe vedere lo stato della pubblicazione nella finestra con l'elenco di attività della data factory illustrata nella figura precedente. Verificare che la pubblicazione abbia avuto esito positivo.

## Usare Esplora Server per esaminare le entità della data factory

1. In **Visual Studio** scegliere **Esplora server** dal menu **Visualizza**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzato **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione verrà visualizzato nella finestra **Elenco attività data factory**. ![Esplora server](./media/data-factory-get-started-using-vs/server-explorer.png)
3. È possibile fare clic con il pulsante destro del mouse su una data factory e scegliere l'opzione per esportare la data factory in un nuovo progetto per creare un progetto di Visual Studio basato su una data factory esistente. ![Esportare la data factory in un progetto VS](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Aggiornare gli strumenti di Data factory di Azure per Visual Studio
Per aggiornare gli strumenti di Data Factory di Azure per Visual Studio, eseguire le operazioni seguenti:

1. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**. 
2. Selezionare **Aggiornamenti** nel riquadro sinistro e quindi selezionare **Visual Studio Gallery**.
4. Selezionare **Strumenti di Data Factory di Azure per Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento. 

Vedere [Monitorare i set di dati e la pipeline](data-factory-get-started-using-editor.md/#MonitorDataSetsAndPipeline) per istruzioni su come usare il portale di anteprima di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione.

<!---HONumber=July15_HO5-->