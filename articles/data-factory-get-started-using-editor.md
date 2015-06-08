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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Esercitazione: Creare e monitorare una factory di dati utilizzando dati Factory Editor
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##Contenuto dell'esercitazione:
In questa esercitazione include i passaggi seguenti:

Passaggio | Descrizione
-----| -----------
[Passaggio 1: Creare una Factory di dati di Azure](#CreateDataFactory) | In questo passaggio si creerà una factory di dati di Azure denominata **ADFTutorialDataFactory**.  
[Passaggio 2: Creare servizi collegati](#CreateLinkedServices) | In questo passaggio si creerà due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. Il StorageLinkedService collega l'archiviazione di Azure e AzureSqlLinkedService collega il database di SQL Azure per la ADFTutorialDataFactory. I dati di input per la pipeline risiedono in un contenitore di blob nell'archiviazione blob di Azure e i dati di output verranno archiviati in una tabella nel database SQL Azure. Pertanto, aggiungere questi archivi di dati di due come servizi collegati per la factory di dati.      
[Passaggio 3: Creazione di input e output di tabelle](#CreateInputAndOutputDataSets) | Nel passaggio precedente è stato creato servizi collegati che fanno riferimento ad archivi dati che contengono dati di input/output. In questo passaggio verranno definiti due tabelle di dati factory - **EmpTableFromBlob** e **EmpSQLTable** che rappresentano i dati di input/output sono memorizzati in archivi dati. Per EmpTableFromBlob, si specificherà il contenitore di blob che contiene un blob con i dati di origine e per il EmpSQLTable, si specificherà la tabella SQL in cui verrà archiviati i dati di output. È inoltre necessario specificare altre proprietà, ad esempio la struttura dei dati, la disponibilità di dati, ecc... 
[Passaggio 4: Creare ed eseguire una pipeline](#CreateAndRunAPipeline) | In questo passaggio si creerà una pipeline denominata **ADFTutorialPipeline** nel ADFTutorialDataFactory. La pipeline avrà un **attività Copia** che copia input i dati da Azure blob per la tabella di output SQL Azure.
[Passaggio 5: Monitorare le sezioni e pipeline](#MonitorDataSetsAndPipeline) | In questo passaggio, monitorare le sezioni delle tabelle di input e output tramite il portale Azure Preview.
 

## <a name="CreateDataFactory"></a>Passaggio 1: Creare una Factory di dati di Azure
In questo passaggio, è possibile utilizzare il portale Azure Preview per creare una factory di dati di Azure denominata **ADFTutorialDataFactory**.

1.	Accedendo alla [portale Azure Preview][azure-preview-portal], fare clic su **nuovo** nell'angolo inferiore sinistro, selezionare **analisi di dati** nel **Crea** blade e fare clic su **dati Factory** nel **analisi di dati** blade. 

	![Nuovo -> DataFactory][image-data-factory-new-datafactory-menu]

6. Nel **nuova factory di dati** blade:
	1. Immettere **ADFTutorialDataFactory** per il **nome**. 
	
  		![Nuovo blade di factory di dati][image-data-factory-getstarted-new-data-factory-blade]
	2. Fare clic su **nome gruppo di risorse** e procedere come segue:
		1. Fare clic su **creare un nuovo gruppo di risorse**.
		2. Nel **Crea gruppo di risorse** blade, immettere **ADFTutorialResourceGroup** per il **nome** del gruppo di risorse e fare clic su **OK**. 

			![Crea gruppo di risorse][image-data-factory-create-resource-group]

		Alcuni dei passaggi in questa esercitazione si presuppone che venga utilizzato il nome: **ADFTutorialResourceGroup** per il gruppo di risorse. Per ulteriori informazioni sui gruppi di risorse, vedere [utilizza gruppi di risorse per gestire le risorse di Azure](resource-group-overview.md).  
7. Nel **nuova factory di dati** blade, si noti che **aggiungere alla schermata iniziale** è selezionata.
8. Fare clic su **Crea** nel **nuova factory di dati** blade.

	> [AZURE.NOTE]È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato l'errore: **dati factory "ADFTutorialDataFactory" non è disponibile**, modificare il nome della factory di dati (ad esempio, yournameADFTutorialDataFactory) e provare nuovamente a creare. Utilizzare questo nome al posto di ADFTutorialFactory durante l'esecuzione di passaggi restanti in questa esercitazione. Vedere [dati Factory - le regole di denominazione][data-factory-naming-rules] argomento per le regole di denominazione per gli elementi dati Factory.
	 
	![Nome della Factory dati non disponibile][image-data-factory-name-not-available]

9. Fare clic su **notifiche** hub a sinistra e cercare le notifiche dal processo di creazione. Fare clic su **X** per chiudere la **notifiche** blade se è aperto.
10. Dopo aver completata la creazione, verrà visualizzato il **dati FACTORY** blade, come illustrato di seguito.

    ![Home page di factory di dati][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Passaggio 2: Creare servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Un archivio dati può essere un'archiviazione di Azure, un database SQL di Azure o un database di SQL Server locale.

In questo passaggio si creerà due servizi collegati: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService collegati collegamenti al servizio un Account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL Azure per la **ADFTutorialDataFactory**. Più avanti in questa esercitazione che copia dati da un contenitore di blob in StorageLinkedService in una tabella SQL AzureSqlLinkedService si creerà una pipeline.

### Creare un servizio collegato per l'account di archiviazione di Azure
1.	Nel **dati FACTORY** blade, fare clic su **autore e distribuire** riquadro per avviare il **Editor** per la factory di dati.

	![Riquadro Creare e distribuire][image-author-deploy-tile]

	> [AZURE.NOTE]Vedere [dati Factory Editor][data-factory-editor] argomento per una panoramica dettagliata dell'editor dati Factory.
	 
5. Nel **Editor**, fare clic su **nuovo archivio dati** pulsante sulla barra degli strumenti e selezionare **di archiviazione Azure** dal menu a discesa. Verrà visualizzato il modello JSON per la creazione di un servizio di archiviazione Azure collegato nel riquadro di destra.

	![Editor nuovo pulsante di archivio dati][image-editor-newdatastore-button]
    
6. Sostituire **accountname** e **accountkey** con il nome di account e i valori di chiave di account per l'account di archiviazione di Azure.

	![Archiviazione Blob editor JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Vedere [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) per informazioni dettagliate sulle proprietà JSON.

6. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire il StorageLinkedService. Verificare che sia visualizzato il messaggio **collegato servizio creato correttamente** sulla barra del titolo.

	![Distribuire archiviazione Blob di editor][image-editor-blob-storage-deploy]

### Creare un servizio collegato per il Database di SQL Azure
1. Nel **dati Factory Editor** , fare clic su **nuovo archivio dati** pulsante sulla barra degli strumenti e selezionare **database SQL Azure** dal menu a discesa. Verrà visualizzato il modello JSON per la creazione del servizio SQL Azure collegato nel riquadro di destra.

	![Impostazioni di SQL Azure Editr][image-editor-azure-sql-settings]

2. Sostituire **nomeserver**, **databasename**, **username@servername**, e **password** con nomi di server SQL Azure, database, account utente e password. 
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire il AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Passaggio 3: Creazione di input e output di tabelle
Nel passaggio precedente, creato servizi collegati **StorageLinkedService** e **AzureSqlLinkedService** per collegare un account di archiviazione Azure e un database di SQL Azure per la factory di dati: **ADFTutorialDataFactory**. In questo passaggio verranno definiti due tabelle di dati factory - **EmpTableFromBlob** e **EmpSQLTable** che rappresentano i dati di input/output sono memorizzati in archivi dati a cui fa riferimento StorageLinkedService e AzureSqlLinkedService rispettivamente. Per EmpTableFromBlob, si specificherà il contenitore di blob che contiene un blob con i dati di origine e per EmpSQLTable, si specificherà la tabella SQL in cui verrà archiviati i dati di output.

### Creazione della tabella di input 
Un tabella è un set di dati rettangolare che prevede uno schema. In questo passaggio si creerà una tabella denominata **EmpBlobTable** che punta a un contenitore di blob in Azure Storage rappresentata dal **StorageLinkedService** collegato del servizio.

1. Nel **Editor** per la Factory di dati, fare clic su **nuovo set di dati** pulsante sulla barra degli strumenti fare clic su **tabella Blob** dal menu a discesa. 
2. Sostituire JSON nel riquadro di destra con il frammento JSON seguente: 

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
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     Tenere presente quanto segue:
	
	- percorso **tipo** è impostato su **AzureBlobLocation**.
	- **linkedServiceName** è impostato su **StorageLinkedService**. È stato creato questo servizio collegato nel passaggio 2.
	- **folderPath** viene impostato il **adftutorial** contenitore. È anche possibile specificare il nome di un BLOB all'interno della cartella. Poiché non si specifica il nome del BLOB, i dati da tutti i BLOB nel contenitore sono considerati come dati di input.  
	- formato **tipo** è impostato su **FormatoTesto**
	- Sono disponibili due campi nel file di testo: **FirstName** e **LastName** separati da una virgola (* * columnDelimiter * *)	
	- Il **disponibilità** è impostato su **oraria** (* * frequenza * * è impostato su **ora** e **intervallo** è impostato su **1** ), in modo che il servizio dati Factory Cerca dati di input ogni ora nella cartella radice nel contenitore di blob (* * adftutorial * *) specificato. 
	

	Se non si specifica un **fileName** per un **input** **tabella**, tutti i BLOB/file dalla cartella di input (* * folderPath * *) vengono considerati come input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Visualizzare i file di esempio nel [esercitazione][adf-tutorial] per gli esempi.
 
	Se non si specifica un **fileName** per un **tabella di output**, il generato il file nel **folderPath** denominato nel formato seguente: Data. & lt; GUID & gt;. txt (esempio: Data.0a405f8a 93ff di b3be 4c6f f69616f1df7a.txt.).

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

2. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire il **EmpTableFromBlob** tabella. Verificare che il **tabella CREATA correttamente** messaggio sulla barra del titolo dell'Editor.

### Creazione della tabella di output
In questa parte del passaggio, si creerà una tabella di output denominata **EmpSQLTable** che punta a una tabella SQL in SQL Azure database che è rappresentato dal **AzureSqlLinkedService** collegato del servizio.

1. Nel **Editor** per la Factory di dati, fare clic su **nuovo set di dati** pulsante sulla barra degli strumenti fare clic su **tabella di SQL Azure** dal menu a discesa. 
2. Sostituire JSON nel riquadro di destra con il frammento JSON seguente:

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
	* **linkedServiceName** è impostato su **AzureSqlLinkedService** (è stato creato questo servizio collegato nel passaggio 2).
	* **tablename** è impostato su **emp**.
	* Sono presenti tre colonne: **ID**, **FirstName**, e **LastName** : nella tabella emp nel database, ma l'ID è una colonna identity, pertanto è necessario specificare solo **FirstName** e **LastName** qui.
	* Il **disponibilità** è impostato su **oraria** (* * frequenza * * impostato su **ora** e **intervallo** impostato su **1**). Il servizio dati Factory genererà una porzione di dati di output ogni ora il **emp** tabella nel database SQL Azure.


3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire il **EmpSQLTable** tabella.


## <a name="CreateAndRunAPipeline"></a>Passaggio 4: Creare ed eseguire una pipeline
In questo passaggio, è possibile creare una pipeline con un **attività Copia** che utilizza **EmpTableFromBlob** come input e **EmpSQLTable** come output.

1. Nel **Editor** per la Factory di dati, fare clic su **nuova pipeline** sulla barra degli strumenti. Fare clic su **... (puntini di sospensione)** sulla barra degli strumenti se il pulsante non è visibile. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Pipeline** nella visualizzazione albero e fare clic su **Nuova pipeline**.

	![Editor nuovo pulsante della Pipeline][image-editor-newpipeline-button]
 
2. Sostituire JSON nel riquadro di destra con il frammento JSON seguente:

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

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	Tenere presente quanto segue:

	- Nella sezione attività esiste una sola attività cui **tipo** è impostato su **CopyActivity**.
	- Input per l'attività è impostata su **EmpTableFromBlob** e di output per l'attività è impostata su **EmpSQLTable**.
	- Nel **trasformazione** sezione **BlobSource** viene specificato come tipo di origine e **SqlSink** viene specificato come tipo di sink.

	> [AZURE.NOTE]Sostituire il valore del **avviare** proprietà con il giorno corrente e **end** valore con il giorno successivo. È possibile specificare solo la parte di data e ignorare la parte dell'ora della data dell'ora. Ad esempio, "2015-02-03", che equivale a "2015-02-03T00:00:00Z" sia avviare e date e ore di fine deve essere [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. Il **end** ora è facoltativo, ma si utilizzerà in questa esercitazione. Se non si specifica il valore per il **end** proprietà, viene calcolato come "* * inizio + 48 ore * *". Per eseguire la pipeline a tempo indeterminato, specificare **9999-09-09** come valore per il **end** proprietà. Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
	
	> [AZURE.NOTE]Vedere [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) per informazioni dettagliate sulle proprietà JSON.

4. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire il **ADFTutorialPipeline**. Verificare che il **PIPELINE creato correttamente** messaggio.
5. A questo punto, chiudere il **Editor** blade facendo clic su **X**. Fare clic su **X** per chiudere il blade ADFTutorialDataFactory con la visualizzazione della barra degli strumenti e struttura ad albero. Se viene visualizzato **verranno annullate le modifiche non salvate** dei messaggi, fare clic su **OK**.
6. Dovrebbe essere verso il **dati FACTORY** blade per il **ADFTutorialDataFactory**.

**Congratulazioni**. Data factory di Azure, i servizi collegati, le tabelle e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.
 
### Visualizzare la factory di dati in una visualizzazione Diagramma 
1. Nel **dati FACTORY** blade, fare clic su **diagramma**.

	![Dati Factory Blade - riquadro Diagramma][image-datafactoryblade-diagramtile]

2. Viene visualizzato un diagramma simile al seguente:

	![Vista diagramma][image-data-factory-get-started-diagram-blade]

	È possibile eseguire lo zoom avanti, eseguire lo zoom indietro, zoom al 100%, zoom per adattarsi, automaticamente posizionare pipeline e le tabelle e visualizzare le informazioni di derivazione (evidenzia gli elementi padre e figlio degli elementi selezionati). È possibile double blick su un oggetto (tabella di input/output o pipeline) per visualizzare le proprietà per esso. 
3. Fare clic destro sul **ADFTutorialPipeline** nella visualizzazione Diagramma e fare clic su **aprire pipeline**. Si noterà che le attività nella pipeline e DataSet di input e output per le attività. In questa esercitazione, è necessario avere una sola attività nella pipeline (attività Copia) con EmpTableBlob come set di dati di input ed EmpSQLTable come set di dati di output.   

	![Aprire Pipeline](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Fare clic su **Data factory** in tale breadcrumb nell'angolo superiore sinistro per tornare alla visualizzazione Diagramma. Visualizza tutte le pipeline. In questo esempio è stata creata solo una singola pipeline.
 

## <a name="MonitorDataSetsAndPipeline"></a>Passaggio 5: Monitorare i set di dati e la pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Data factory di Azure. È anche possibile usare i cmdlet di PowerShell per monitorare i set di dati e le pipeline. Per ulteriori informazioni sull'utilizzo dei cmdlet per il monitoraggio, vedere [monitorare e gestire dati Factory utilizzando PowerShell Cmdlets][monitor-manage-using-powershell].

1. Passare a [portale di Azure (anteprima)][azure-preview-portal] se non si dispone, aprire. 
2. Se il blade per **ADFTutorialDataFactory** non è aperto, aprirlo scegliendo **ADFTutorialDataFactory** sul **occhio**. 
3. Viene visualizzato il numero e i nomi delle tabelle e delle pipeline create nel pannello.

	![Home page con nomi][image-data-factory-get-started-home-page-pipeline-tables]

4. Fare clic su **set di dati** riquadro.
5. Nel **set di dati** blade, fare clic su **EmpTableFromBlob**. Questa è la tabella di input per il **ADFTutorialPipeline**.

	![Set di dati con EmpTableFromBlob selezionata][image-data-factory-get-started-datasets-emptable-selected]   
5. Si noti che gli intervalli di dati fino all'ora corrente sono già stati prodotti e sono **Pronto** perché il **emp.txt** file esiste sempre nel contenitore di blob: **adftutorial\input**. Verificare che nessun sezioni vengono visualizzate nel **Impossibile recentemente sezioni** nella parte inferiore.

	Entrambi **aggiornato di recente sezioni** e **Impossibile recentemente sezioni** gli elenchi vengono ordinati in base la **ora ultimo aggiornamento**. I tempi di aggiornamento di una sezione viene modificato nelle situazioni seguenti.
    

	-  È aggiornare lo stato della sezione manualmente, ad esempio, utilizzando il **Set AzureDataFactorySliceStatus** (o) facendo clic su **eseguire** sul **sezione** blade della sezione.
	-  La sezione Cambia stato a causa di un'esecuzione (ad esempio, una sequenza di avvio, un'esecuzione terminata e non è riuscita, un'esecuzione terminata e ha avuto esito positivo, ecc).
 
	Fare clic sul titolo degli elenchi o **... (puntini di sospensione)** Per visualizzare l'elenco di intervalli di dimensioni maggiore. Fare clic su **filtro** sulla barra degli strumenti per filtrare le sezioni.
	
	Per visualizzare le sezioni di dati ordinati in base alle ore di inizio e fine sezione invece, fare clic su **sezioni di dati (per ora slice)** riquadro.

	![Sezioni di dati dalla sezione tempo][DataSlicesBySliceTime]

6. Ora, nel **set di dati** blade, scegliere il **EmpSQLTable**. Questa è la tabella di output per il **ADFTutorialPipeline**.

	![blade di set di dati][image-data-factory-get-started-datasets-blade]



	 
6. Verrà visualizzato il **EmpSQLTable** blade, come illustrato di seguito:

	![blade di tabella][image-data-factory-get-started-table-blade]
 
7. Si noti che gli intervalli di dati fino all'ora corrente sono già stati prodotti e sono **Pronto**. Nessun intervalli visualizzati nella **sezioni problema** nella parte inferiore.
8. Fare clic su **... (Puntini di sospensione)** Per visualizzare tutte le sezioni.

	![blade porzioni di dati][image-data-factory-get-started-dataslices-blade]

9. Fare clic su una sezione di dati dall'elenco e verrà visualizzato il **sezione di dati** blade.

	![blade porzione di dati][image-data-factory-get-started-dataslice-blade]
  
	Se non è inclusa nella sezione il **Pronto** stato, è possibile visualizzare le sezioni a monte che non si è pronti e bloccano l'intervallo corrente da eseguito nel **sezioni a monte che non sono pronte** elenco.

11. Nel **sezione di dati** blade, dovrebbe essere visualizzato esegue tutte le attività nell'elenco nella parte inferiore. Fare clic su un **attività eseguire** per visualizzare il **dettagli di esecuzione di attività** blade.

	![Dettagli di esecuzione di attività][image-data-factory-get-started-activity-run-details]

	
12. Fare clic su **X** per chiudere tutti i server blade fino a tornare alla home blade per il **ADFTutorialDataFactory**.
14. (facoltativo) Fare clic su **pipeline** nella home page per **ADFTutorialDataFactory**, fare clic su **ADFTutorialPipeline** nel **pipeline** blade e drill-through tabelle di input (* * utilizzato * *) o tabelle di output (* * prodotte * *).
15. Avviare **SQL Server Management Studio**, connettersi al Database di SQL Azure e verificare che le righe vengono inserite nella **emp** tabella nel database.

	![risultati della query SQL][image-data-factory-get-started-sql-query-results]


## Riepilogo 
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. Il portale Azure Preview consente di creare la factory di dati, servizi collegati, tabelle e una pipeline. Ecco i passaggi di alto livello eseguiti in questa esercitazione:

1.	Creare un Azure **factory dati**.
2.	Creare **collegati servizi** che si collegano gli archivi dati e Calcola (definito come **servizi collegati**) per la factory di dati.
3.	Creare **tabelle** che descrivono dati e i dati di output per le pipeline di input.
4.	Creare **pipeline**. Una pipeline è costituita da una o più attività ed elabora gli input producendo l'output. Impostare il periodo attivo per la pipeline specificando **avviare** tempo e **End** tempo per la pipeline. Il periodo attivo definisce l'intervallo di tempo in cui verranno prodotte sezioni di dati. 


> [AZURE.NOTE]Per un elenco di attività supportate, vedere [pipeline e le attività][msdn-activities] argomento e per un elenco di servizi collegati supportati, vedere [servizi collegati][msdn-linkedservices] argomento in MSDN Library.
> 
> Per eseguire questa esercitazione utilizzando Azure PowerShell, vedere [Create e monitorare una factory di dati con Azure PowerShell][monitor-manage-using-powershell].

## Passaggi successivi

Articolo | Descrizione
------ | ---------------
[Copiare i dati con Azure dati Factory - attività di copia][copy-activity] | Questo articolo vengono fornite informazioni dettagliate sul **attività Copia** utilizzato in questa esercitazione. 
[Abilitare le pipeline lavorare con dati locali][use-onpremises-datasources] | In questo articolo è una procedura dettagliata in cui viene illustrato come copiare dati da un **database di SQL Server on-premise** in un blob di Azure. 
[Esercitazione: Spostare ed elaborare i file di log utilizzando la Factory di dati][adf-tutorial] | In questo articolo fornisce un **procedura dettagliata end-to-end** che viene illustrato come implementare un **scenario reale** utilizza Azure dati Factory per trasformare i dati dai file di log in approfondimenti.
[Risoluzione dei problemi di Factory di dati][troubleshoot] | Questo articolo viene descritto come **risoluzione dei problemi** problemi Factory di dati di Azure. È possibile provare la procedura dettagliata di questo articolo in ADFTutorialDataFactory introducendo un errore (eliminando la tabella nel database SQL di Azure). 
[Riferimenti per sviluppatori Factory di dati di Azure][developer-reference] | Il riferimento per sviluppatori presenta il contenuto di riferimento completo per i cmdlet, script JSON, funzioni e così via... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir-->