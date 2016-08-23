<properties 
	pageTitle="Esercitazione: Creare una pipeline con l’attività Copia utilizzando Data Factory Editor" 
	description="In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory con un'attività di copia usando l'editor di Data Factory nel portale di Azure." 
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

# Esercitazione: Creare una pipeline con l’attività Copia utilizzando Data Factory Editor
> [AZURE.SELECTOR]
- [Panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Con l'editor di Data factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Tramite PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Con Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Uso dell'API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Con la Copia guidata](data-factory-copy-data-wizard-tutorial.md)


Questa esercitazione include i passaggi seguenti:

Passaggio | Descrizione
-----| -----------
[Creazione di un'istanza di Data factory di Azure](#create-data-factory) | In questo passaggio viene creata un'istanza di Azure Data Factory denominata **ADFTutorialDataFactory**.  
[Creazione di servizi collegati](#create-linked-services) | In questo passaggio vengono creati due servizi collegati: **AzureStorageLinkedService** e **AzureSqlLinkedService**. AzureStorageLinkedService collega la risorsa di archiviazione di Azure e AzureSqlLinkedService collega il database SQL di Azure ad ADFTutorialDataFactory. I dati di input per la pipeline si trovano in un contenitore BLOB nell'archivio BLOB di Azure e i dati di output vengono archiviati in una tabella nel database SQL di Azure. Questi due archivi dati vengono quindi aggiunti alla data factory come servizi collegati.      
[Creare set di dati di input e di output](#create-datasets) | Nel passaggio precedente sono stati creati servizi collegati che fanno riferimento agli archivi dati che includono dati di input/output. In questo passaggio vengono definite due tabelle di Data Factory, ovvero **EmpTableFromBlob** ed **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati. Per EmpTableFromBlob viene specificato il contenitore BLOB che include un BLOB con i dati di origine, mentre per EmpSQLTable viene specificata la tabella SQL in cui vengono archiviati i dati di output. Vengono specificate anche altre proprietà, ad esempio la struttura e la disponibilità dei dati e così via. 
[Creare una pipeline](#create-pipeline) | In questo passaggio viene creata una pipeline denominata **ADFTutorialPipeline** in ADFTutorialDataFactory. La pipeline include un'**attività di copia** che copia i dati di input dal BLOB di Azure e li inserisce nella tabella di output di Azure SQL. L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md). 
[Monitorare la pipeline](#monitor-pipeline) | In questo passaggio vengono monitorate sezioni delle tabelle di input e di output mediante il portale di Azure.

> [AZURE.IMPORTANT] 
Leggere l'articolo [Panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e completare i prerequisiti prima di eseguire questa esercitazione.

## Creare un'istanza di Data Factory
In questo passaggio viene usato il portale di Azure per creare un'istanza di Azure Data Factory denominata **ADFTutorialDataFactory**.

1.	Dopo l'accesso al [portale di Azure][azure-portal], fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **Analisi dei dati** nel pannello **Crea** e quindi fare clic su **Data Factory** nel pannello **Analisi dei dati**.

	![Nuovo->DataFactory][image-data-factory-new-datafactory-menu]

6. Nel pannello **Nuova data factory**:
	1. Immettere **ADFTutorialDataFactory** come **nome**.
	
  		![Pannello Nuova data factory][image-data-factory-getstarted-new-data-factory-blade]
	2. Fare clic su **NOME DEL GRUPPO DI RISORSE** ed eseguire queste operazioni:
		1. Fare clic su **Crea un nuovo gruppo di risorse**.
		2. Nel pannello **Crea gruppo di risorse** immettere **ADFTutorialResourceGroup** come **nome** del gruppo di risorse e fare clic su **OK**.

			![Crea gruppo di risorse][image-data-factory-create-resource-group]

		Alcuni dei passaggi di questa esercitazione presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../resource-group-overview.md).
7. Si noti che l'opzione **Aggiungi a Schermata iniziale** è selezionata nel pannello **Nuova data factory**.
8. Fare clic su **Crea** nel pannello **Nuova data factory**.

	È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato un errore simile a **Nome "ADFTutorialDataFactory" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteADFTutorialDataFactory) e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md).
	 
	![Nome di data factory non disponibile][image-data-factory-name-not-available]
	
	> [AZURE.NOTE] Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.
	> 
	> Per creare istanze di data factory, è necessario essere un collaboratore/amministratore della sottoscrizione di Azure.

9. Fare clic sull'hub **NOTIFICHE** a sinistra e cercare le notifiche del processo di creazione. Fare clic su **X** per chiudere il pannello **NOTIFICHE**, se è aperto.
10. Al termine della creazione, il pannello **DATA FACTORY** avrà un aspetto simile al seguente.

    ![Home page di Data factory][image-data-factory-get-stated-factory-home-page]

## Creazione di servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere un'archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale.

In questo passaggio vengono creati due servizi collegati: **AzureStorageLinkedService** e **AzureSqlLinkedService**. Il servizio collegato AzureStorageLinkedService collega un account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure ad **ADFTutorialDataFactory**. Più avanti in questa esercitazione viene creata una pipeline che copia i dati da un contenitore BLOB di AzureStorageLinkedService e li inserisce in una tabella SQL di AzureSqlLinkedService.

### Creare un servizio collegato per l'account di archiviazione di Azure
1.	Nel pannello **DATA FACTORY** fare clic sul riquadro **Creare e distribuire** per avviare l'**editor** della data factory.

	![Riquadro Creare e distribuire][image-author-deploy-tile]

	 
5. Nell'**Editor** fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti, quindi scegliere **Archiviazione di Azure** dal menu a discesa. Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato di archiviazione di Azure.

	![Pulsante Nuovo archivio dati dell'editor][image-editor-newdatastore-button]
    
6. Sostituire **accountname** e **accountkey** con i valori relativi a nome e chiave dell'account di archiviazione di Azure.

	![JSON dell'archivio BLOB dell'editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)
	
	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire AzureStorageLinkedService. Controllare che sulla barra del titolo sia visualizzato un messaggio simile a **CREAZIONE DEL SERVIZIO COLLEGATO COMPLETATA**.

	![Distribuzione dell'archivio BLOB dell'editor][image-editor-blob-storage-deploy]

### Creare un servizio collegato per il database SQL di Azure
1. Nell'**editor di Data Factory** fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti e scegliere **Database SQL di Azure** dal menu a discesa. Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato SQL di Azure.

	![Impostazioni dell'editor di Azure SQL][image-editor-azure-sql-settings]

2. Sostituire **servername**, **databasename**, **username@servername** e **password** con i nomi del server, database, account utente e password di Azure SQL.
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire AzureSqlLinkedService.
   

## Creare set di dati
Nel passaggio precedente sono stati creati i servizi collegati **AzureStorageLinkedService** e **AzureSqlLinkedService** per collegare un account di archiviazione di Azure e un database SQL di Azure alla data factory **ADFTutorialDataFactory**. In questo passaggio vengono definite due tabelle di Data Factory, ovvero **EmpTableFromBlob** ed **EmpSQLTable**, che rappresentano i dati di input/output archiviati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService e AzureSqlLinkedService. Per EmpTableFromBlob viene specificato il contenitore BLOB che include un BLOB con i dati di origine, mentre per EmpSQLTable viene specificata la tabella SQL in cui vengono archiviati i dati di output.

### Creare set di dati di input 
Un tabella è un set di dati rettangolare che prevede uno schema. In questo passaggio viene creata una tabella denominata **EmpBlobTable** che fa riferimento a un contenitore BLOB in Archiviazione di Azure rappresentato dal servizio collegato **AzureStorageLinkedService**.

1. Nell'**editor** di Data Factory fare clic sul pulsante **Nuovo set di dati** sulla barra degli strumenti e scegliere **Tabella BLOB di Azure** dal menu a discesa.
2. Sostituire il codice JSON nel riquadro a destra con il frammento di codice JSON seguente:

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
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
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
	- L'oggetto **linkedServiceName** è impostato su **AzureStorageLinkedService**. Questo servizio collegato è stato creato nel passaggio 2.
	- L'oggetto **folderPath** è impostato sul contenitore **adftutorial**. È anche possibile specificare il nome di un BLOB all'interno della cartella. Poiché non si specifica il nome del BLOB, i dati da tutti i BLOB nel contenitore sono considerati come dati di input.
	- L'oggetto **type** di format è impostato su **TextFormat**.
	- Nel file di testo sono presenti due campi, **FirstName** e **LastName**, separati da una virgola (**columnDelimiter**).
	- L'oggetto **availability** è impostato su **hourly** (**frequency** è impostato su **hour** e **interval** è impostato su **1**), in modo che il servizio Data Factory cerchi dati di input ogni ora nella cartella radice del contenitore BLOB (**adftutorial**) specificato.
	

	Se non si specifica un oggetto **fileName** per una **tabella** di **input**, tutti i file/BLOB della cartella di input (**folderPath**) vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input.
 
	Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il formato seguente: Data.&lt;Guid&gt;.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

	Per informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON di Data Factory](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire la tabella **EmpTableFromBlob**. Controllare che sulla barra del titolo dell'editor sia visualizzato un messaggio simile a **LA CREAZIONE DELLA TABELLA È STATA COMPLETATA**.

### Creare il set di dati di output
In questa parte del passaggio viene creata una tabella di output denominata **EmpSQLTable** che punta a una tabella SQL nel database SQL di Azure rappresentata dal servizio collegato **AzureSqlLinkedService**.

1. Nell'**editor** di Data Factory fare clic sul pulsante **Nuovo set di dati** sulla barra degli strumenti e scegliere **Azure SQL table** (Tabella SQL di Azure) dal menu a discesa.
2. Sostituire il codice JSON nel riquadro a destra con il frammento di codice JSON seguente:

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
	* L'oggetto **linkedServiceName** è impostato su **AzureSqlLinkedService** (questo servizio collegato è stato creato nel passaggio 2).
	* L'oggetto **tablename** è impostato su **emp**.
	* Nella tabella emp del database sono presenti tre colonne, **ID**, **FirstName** e **LastName**, ma ID è una colonna di identità, quindi è necessario specificare solo **FirstName** e **LastName**.
	* L'oggetto **availability** è impostato su **hourly** (l'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**). Il servizio Data Factory genera una porzione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure.


3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire la tabella **EmpSQLTable**.


## Creare una pipeline
In questo passaggio è possibile creare una pipeline con un'**attività di copia** che usa **EmpTableFromBlob** come input e **EmpSQLTable** come output.

1. Nell'**editor** della data factory fare clic sul pulsante **Nuova pipeline** sulla barra degli strumenti. Fare clic su **... (puntini di sospensione)** sulla barra degli strumenti se il pulsante non è visibile. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Pipeline** nella visualizzazione ad albero e fare clic su **Nuova pipeline**.

	![Pulsante Nuova pipeline dell'editor][image-editor-newpipeline-button]
 
2. Sostituire il codice JSON nel riquadro a destra con il frammento di codice JSON seguente:
		
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
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	Tenere presente quanto segue:

	- Nella sezione activities esiste una sola attività con l'oggetto **type** impostato su **CopyActivity**.
	- L'input per l'attività è impostato su **EmpTableFromBlob** e l'output per l'attività è impostato su **EmpSQLTable**.
	- Nella sezione **transformation** **BlobSource** viene specificato come tipo di origine e **SqlSink** come tipo di sink.

	Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. È possibile specificare solo la parte relativa alla data e ignorare la parte relativa all'ora, ad esempio "2015-02-03", che equivale a "2015-02-03T00:00:00Z".
	
	Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2014-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato.
	
	Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**.
	
	Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
	
	Per informazioni dettagliate sulle proprietà JSON, vedere [Informazioni di riferimento sugli script JSON di Data Factory](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire **ADFTutorialPipeline**. Controllare che sia visualizzato un messaggio simile a **LA CREAZIONE DELLA PIPELINE È STATA COMPLETATA**.
5. Chiudere quindi il pannello dell'**editor** facendo clic su **X**. Fare di nuovo clic su **X** per chiudere il pannello ADFTutorialDataFactory con la barra degli strumenti e la visualizzazione ad albero. Se viene visualizzato un messaggio simile a **Eventuali modifiche non salvate verranno rimosse**, fare clic su **OK**.
6. Dovrebbe essere visualizzato di nuovo il pannello **DATA FACTORY** per **ADFTutorialDataFactory**.

**Congratulazioni.** Azure Data Factory, i servizi collegati, le tabelle e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.
 
### Visualizzare la data factory in una vista diagramma 
1. Nel pannello **DATA FACTORY** fare clic su **Diagramma**.

	![Pannello Data factory - Riquadro Diagramma][image-datafactoryblade-diagramtile]

2. Viene visualizzato un diagramma simile al seguente:

	![Vista diagramma][image-data-factory-get-started-diagram-blade]

	È possibile eseguire lo zoom avanti, lo zoom indietro e lo zoom al 100%, adattare alla finestra, posizionare automaticamente pipeline e tabelle e visualizzare le informazioni sulla derivazione, evidenziando gli elementi upstream e downstream degli elementi selezionati. È possibile fare doppio clic su un oggetto (tabella di input/output o pipeline) per visualizzare le relative proprietà.
3. Fare clic con il pulsante destro del mouse su **ADFTutorialPipeline** nella vista diagramma e scegliere **Apri pipeline**. Dovrebbero essere visualizzate le attività della pipeline, oltre ai set di dati di input e output per le attività. In questa esercitazione la pipeline include solo un'attività (attività di copia) con EmpTableBlob come set di dati di input ed EmpSQLTable come set di dati di output.

	![Apri pipeline](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)

4. Fare clic su **Data factory** sulla barra di navigazione nell'angolo superiore sinistro per tornare alla vista diagramma in cui sono visualizzate tutte le pipeline. In questo esempio è stata creata una sola pipeline.
 

## Monitorare la pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Azure Data Factory.

1. Passare al [portale di Azure (anteprima)][azure-portal] se non è già aperto.
2. Se il pannello per **ADFTutorialDataFactory** non è aperto, aprirlo facendo clic su **ADFTutorialDataFactory** nella **Schermata iniziale**.
3. Vengono visualizzati il numero e i nomi delle tabelle e delle pipeline
4. create nel pannello.

	![Home page con nomi][image-data-factory-get-started-home-page-pipeline-tables]

4. A questo punto fare clic sul riquadro **Set di dati**.
5. Nel pannello **Set di dati** fare clic su **EmpTableFromBlob**. Questa è la tabella di input per **ADFTutorialPipeline**.

	![Set di dati con tabella EmpTableFromBlob selezionata][image-data-factory-get-started-datasets-emptable-selected]
5. Si noti che le sezioni di dati fino all'ora corrente sono già state prodotte e sono nello stato **Pronto** perché il file **emp.txt** è sempre presente nel contenitore BLOB: **adftutorial\\input**. Verificare che non sia visualizzata alcuna sezione in **Sezioni non riuscite di recente** nella parte inferiore della pagina.

	Gli elenchi **Sezioni aggiornate di recente** e **Sezioni non riuscite di recente** sono ordinati in base a **ORA ULTIMO AGGIORNAMENTO**. L'ora di aggiornamento di una sezione viene modificata nelle situazioni seguenti.
    

	-  Lo stato della sezione viene aggiornato manualmente, ad esempio usando **Set-AzureRmDataFactorySliceStatus** oppure facendo clic su **ESEGUI** nel pannello **SEZIONE** della sezione.
	-  Lo stato della sezione cambia a causa di un'esecuzione, ad esempio un'esecuzione avviata, un'esecuzione terminata con errore, un'esecuzione terminata correttamente e così via.
 
	Fare clic sul titolo degli elenchi oppure su **... (puntini di sospensione)** per visualizzare un elenco di sezioni più ampio. Fare clic su **Filtro** sulla barra degli strumenti per filtrare le sezioni.
	
	Per visualizzare invece le sezioni di dati ordinate in base alle ore di inizio/fine, fare clic sul riquadro **Sezioni dati (in base all'ora della sezione)**.

	![Sezioni dati in base all'ora della sezione][DataSlicesBySliceTime]

6. Nel pannello **Set di dati** fare clic su **EmpSQLTable**. Questa è la tabella di output per **ADFTutorialPipeline**.

	![Pannello Set di dati][image-data-factory-get-started-datasets-blade]



	 
6. Verrà visualizzato il pannello **EmpSQLTable**, come illustrato di seguito:

	![Pannello Tabella][image-data-factory-get-started-table-blade]
 
7. Le sezioni di dati fino all'ora corrente sono state già generate e sono in stato **Pronto**. Non sono visualizzate sezioni in **Sezioni con errori** nella parte inferiore della pagina.
8. Fare clic su **… (puntini di sospensione)** per visualizzare tutte le sezioni.

	![Pannello Sezioni dati][image-data-factory-get-started-dataslices-blade]

9. Fare clic su una qualsiasi sezione di dati dell'elenco per visualizzare il pannello **SEZIONE DI DATI**.

	![Pannello Sezione di dati][image-data-factory-get-started-dataslice-blade]
  
	Se lo stato della sezione non è **Pronto**, sarà possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**.

11. Nel pannello **SEZIONE DI DATI** è possibile visualizzare tutte le esecuzioni di attività nell'elenco in basso. Fare clic su un'**esecuzione attività** per visualizzare il pannello **DETTAGLI ESECUZIONE ATTIVITÀ**.

	![Dettagli esecuzione attività][image-data-factory-get-started-activity-run-details]

	
12. Fare clic su **X** per chiudere tutti i pannelli finché non viene visualizzato il pannello iniziale per **ADFTutorialDataFactory**.
14. (facoltativo) Fare clic su **Pipeline** nella home page di **ADFTutorialDataFactory**, quindi su **ADFTutorialPipeline** nel pannello **Pipeline** e infine eseguire il drill-through delle tabelle di input (**Utilizzate**) o di output (**Prodotte**).
15. Avviare **SQL Server Management Studio**, connettersi al database SQL di Azure e verificare che le righe vengano inserite nella tabella **emp** nel database.

	![Risultati della query SQL][image-data-factory-get-started-sql-query-results]


## Riepilogo 
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. È stato usato il portale di Azure per creare la data factory, i servizi collegati, i set di dati e una pipeline. Ecco i passaggi generali eseguiti in questa esercitazione:

1.	Creare un'istanza di Azure **Data Factory**.
2.	Creare **servizi collegati**:
	1. Un servizio collegato di **Archiviazione di Azure** per collegare l'account di archiviazione di Azure che include i dati di input.
	2. Un servizio collegato di **Azure SQL** per collegare il database SQL di Azure che contiene i dati di output.
3.	Creare **set di dati** che descrivono dati di input e dati di output per le pipeline.
4.	Creare una **pipeline** con un'**attività di copia** con **BlobSource** come origine e **SqlSink** come sink.


## Vedere anche
| Argomento | Descrizione |
| :---- | :---- |
| [Attività di spostamento dei dati](data-factory-data-movement-activities.md) | Questo articolo fornisce informazioni dettagliate sull'attività di copia usata nell'esercitazione. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo fornisce informazioni sui set di dati in Azure Data Factory.
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0817_2016-->