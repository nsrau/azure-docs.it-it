<properties 
	pageTitle="Editor di Data factory di Azure" 
	description="Descrive l'editor di Data factory di Azure, che consente di creare, modificare e distribuire definizioni JSON di servizi, tabelle e pipeline collegati usando una semplice interfaccia utente basata sul Web." 
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
	ms.date="09/22/2015" 
	ms.author="spelluru"/>

# Editor di Data factory di Azure
L'editor di Data factory di Azure è un semplice editor Web che fa parte del portale di anteprima di Azure, che consente di creare, modificare e distribuire file JSON di tutte le entità di Data Factory Azure. Permette di creare servizi collegati, set di dati e pipeline usando i modelli JSON forniti con il servizio Data factory. Se non si conosce PowerShell, evita di dover installare e imparare a usare Azure PowerShell per creare data factory di Azure.

## Avvio dell'editor di Data factory di Azure
Per avviare l'editor di Data factory, fare clic sul riquadro **Creare e distribuire** del pannello **Data factory** della data factory di Azure.

![Riquadro Creare e distribuire][author-and-deploy-tile]

L'editor di Data factory verrà visualizzato come nella figura seguente:
 
![Editor di Data factory][data=factory-editor]
 
## Creazione di nuovi servizi, set di dati e pipeline collegati
Ci sono quattro pulsanti sulla barra degli strumenti che è possibile usare per creare entità di Data factory di Azure.
 
- **Nuovo archivio dati** per la creazione di un servizio collegato di archivio dati. Facendo clic su questo pulsante, verrà visualizzato un menu con le seguenti opzioni: Archiviazione di Azure, Database SQL di Azure, Database SQL Server locale.
- **Nuovo calcolo** per la creazione di un servizio collegato di calcolo. Facendo clic su questo pulsante, verrà visualizzato un menu con le seguenti opzioni: Cluster HDInsight su richiesta, Cluster HDInsight, Servizio collegato AzureML.      
- **Nuovo set di dati** per la creazione di un set di dati. Facendo clic su questo pulsante, verranno visualizzate le opzioni seguenti: Tabella BLOB, Tabella di Azure SQL, Tabella locale.  
- **Nuova pipeline** per la creazione di una pipeline. Fare clic su **... (puntini di sospensione)** sulla barra degli strumenti se questo pulsante non è visibile sulla barra degli strumenti.
 
### Per creare una servizio collegato di archiviazione
1. Fare clic su **Nuovo archivio dati** e fare clic su una delle opzioni del menu.
 
	![Menu Nuovo archivio dati][new-data-store-menu] 
2. Verrà visualizzato il modello JSON per la creazione di un servizio collegato di archiviazione nel **canvas dell'editor** a destra. Si noterà anche che sotto **Bozze** appare il nodo di una bozza. Eseguire le operazioni seguenti:
	1. Per **Archiviazione di Azure**: sostituire **<accountname>** e **<accountkey>** con il nome e la chiave dell'account di archiviazione di Azure.
	2. Per **Database SQL di Azure**: sostituire **<servername>** con il nome del server di Azure SQL, **<databasename>** con il nome del database, **<username>@<servername>** con il nome dell'utente e **<password>** con la password dell'account utente. 
	3. Per **Database SQL Server locale**: sostituire **<servername>** con il nome del server SQL locale, **<databasename>** con il nome del database, **<username>** con il nome dell'utente e **<password>** con la password dell'account utente.
4. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire il servizio collegato. È possibile fare clic su **Rimuovi** per rimuovere la bozza JSON creata.
 
	![Pulsante Distribuisci][deploy-button]

1. Sulla barra del titolo si dovrebbe vedere lo stato dell'operazione di distribuzione.

	![Messaggio di distribuzione riuscita][deploy-success-message]
2. Se l'operazione di distribuzione ha esito positivo, nella visualizzazione albero a sinistra si dovrebbe vedere il servizio collegato creato.
  
	![StorageLinkedService nella visualizzazione albero][storagelinkedservice-in-treview]

### Per creare una servizio collegato di calcolo
1. Fare clic su **Nuovo calcolo** e fare clic su una delle opzioni del menu.
 
	![Menu Nuovo calcolo][new-compute-menu] 
2. Verrà visualizzato il modello JSON per la creazione di un servizio collegato di calcolo nel canvas dell'editor a destra. Eseguire le operazioni seguenti:
	1. Per **Cluster HDInsight su richiesta**, specificare i valori per le proprietà seguenti: 
		1. Per la proprietà **clusterSize**, specificare le dimensioni del cluster HDInsight che il servizio Dati factory dovrà creare in fase di esecuzione. 
		2. Per la proprietà **jobsContainer**, specificare il nome del contenitore BLOB predefinito dove archiviare i log del cluster.
		3. Per la proprietà **timeToLive**, specificare il tempo di inattività consentito prima che venga eliminato il cluster HDInsight. Ad esempio, 00:05:00 indica che il cluster deve essere eliminato dopo 5 minuti di inattività.
		4. Per la proprietà **version**, specificare la versione di HDInsight per il cluster (impostazione predefinita: versione 3.1).
		5. Per la proprietà **linkedServiceName**, specificare il servizio collegato di archiviazione di Azure da associare al cluster HDInsight. 
	6. Per **Cluster HDInsight** (Bring-Your-Own), specificare i valori per le proprietà seguenti:
		1. Per la proprietà **clusterUri**, specificare l'URL per il proprio cluster HDInsight. 
		2. Per la proprietà **userName**, specificare l'account utente che il servizio Dati factory deve usare per connettersi al cluster HDInsight. 
		3. Per la proprietà **password**, specificare la password per l'account utente. 
		4. Per la proprietà **linkedServiceName**, specificare il servizio collegato di archiviazione di Azure associato al cluster HDInsight.
	5. Per **Servizio collegato AzureML**, specificare le proprietà seguenti:
		1. Per la proprietà **mlEndPoint**, specificare l'URL del punteggio batch di Azure Machine Learning.
		2. Per la proprietà **apiKey**, specificare la chiave API per il modello di area di lavoro pubblicato.
3. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire il servizio collegato.

> [AZURE.NOTE]Per le descrizioni degli elementi JSON usati per definire un servizio collegato di Data factory di Azure, vedere l'argomento [Servizi collegati][msdn-linkedservices-reference] in MSDN Library.

### Per creare un nuovo set di dati
1. Fare clic su **Nuovo set di dati** e fare clic su una delle opzioni del menu.
2. Verrà visualizzato il modello JSON per la creazione di un set di dati nel canvas dell'editor a destra. Eseguire le operazioni seguenti: 
	1. Per **Tabella BLOB **, specificare i valori per le proprietà seguenti:
	2. Per **Tabella di Azure SQL** o **Tabella locale**, specificare i valori per le proprietà seguenti: 
		1. Nella sezione **location**: 
			2. Per la proprietà **linkedServiceName**, specificare il nome del servizio collegato che fa riferimento al database SQL di Azure/SQL Server locale.
			2. Per la proprietà **tableName**, specificare il nome della tabella nell'istanza di database SQL di Azure/server SQL locale a cui fa riferimento il servizio collegato.
		3. Nella sezione **availability**:
			1. Per la proprietà **frequency**, specificare l'unità di tempo per la produzione di una sezione di dati. I valori di frequenza supportati sono: Minute, Hour, Day, Week, Month.
			2. Per la proprietà **interval**, specificare l'intervallo nella frequenza definita. Se **frequency** è impostata su **Hour** e **interval** su **1**, significa che verranno prodotte nuove sezioni di dati ogni ora. 
		3. Nella sezione **structure**: 
			1. Specificare i nomi e i tipi di colonne, come mostrato nell'esempio seguente:
				
				    "structure":
	        		[
	                	{ "name": "FirstName", "type": "String"},
	                	{ "name": "LastName", "type": "String"}
	        		],
	     
> [AZURE.NOTE]Per le descrizioni degli elementi JSON usati per definire una tabella di Data factory di Azure, vedere l'argomento [Tabelle][msdn-tables-reference] in MSDN Library.
 		           
### Per creare e attivare una pipeline 
1. Fare clic su **Nuova pipeline** sulla barra degli strumenti. Se il pulsante **Nuova pipeline** non è presente, fare clic su **...(puntini di sospensione)** per visualizzarlo.   
2. Verrà visualizzato il modello JSON per la creazione di una pipeline nel canvas dell'editor a destra. Eseguire le operazioni seguenti: 
	1. Per la proprietà **description**, specificare la descrizione della pipeline.
	2. Per la sezione **activities**, aggiungere le attività alla pipeline. Esempio:
	 
			"activities":	
			[
				{
					"name": "CopyFromBlobToSQL",
					"description": "Push Regional Effectiveness Campaign data to Azure SQL",
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
	3. Per la proprietà **start**, specificare quando inizia l'elaborazione dati o quando verranno elaborate le sezioni di dati. Esempio: 2014-05-01T00:00:00Z.
	4. Per la proprietà **end**, specificare quando finisce l'elaborazione dati. Esempio: 2014-05-01T00:00:00Z.       

> [AZURE.NOTE]Per le descrizioni degli elementi JSON usati per definire una pipeline di Data factory di Azure, vedere l'argomento [Pipeline e attività][msdn-pipelines-reference] in MSDN Library.

## Per aggiungere una definizione di attività al file JSON di una pipeline
Per aggiungere una definizione di attività al file JSON di una pipeline, fare clic su **Aggiungi attività** sulla barra degli strumenti. Quando si fa clic su questo pulsante, si sceglie il tipo di attività che si desidera aggiungere alla pipeline.

![Opzioni di Aggiungi attività][add-activity-options]

Per copiare dati da un database SQL di Azure all'archiviazione BLOB di Azure ed elaborare i dati nell'archiviazione BLOB usando lo script Pig in un cluster HDInsight, aggiungere prima un'**attività di copia** e quindi un'**attività Pig** alla pipeline. Questo crea due sezioni nella sezione activities del file JSON della pipeline. L'attività Pig non è altro che l'attività di HDInsight con la trasformazione Pig.

	"activities": [
    	{
    		"name": "CopyFromTabletoBlob",
	        "type": "CopyActivity",
			...
		}
		{
			"name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
			...
			"transformation": {
            	"type": "Pig",
				...
			}
		}
	]

## Avvio di una pipeline
Per specificare la data e ora di inizio e la data e ora di fine per una pipeline, specificare i valori per le proprietà start ed end in JSON.

 	{
	    "name": "ADFTutorialPipeline",
	    "properties":
	    {   
	        "description" : "Copy data from a blob to Azure SQL table",
	        "activities":   
	        [
				...
	        ],
	
	        "start": "2015-02-13T00:00:00Z",
	        "end": "2015-02-14T00:00:00Z",
	        "isPaused": false
	    }
	} 
  
## Bozze nell'editor
Le bozze consentono di salvare temporaneamente il lavoro quando si deve cambiare contesto o passare a un'entità diversa nella data factory. La durata delle bozze è associata alla sessione del browser. Se si chiude il browser o si usa un altro computer, le bozze non saranno disponibili.

## Per eliminare una bozza JSON di un'entità di Data factory
Per eliminare la definizione JSON di un'entità di Dati factory di Azure, fare clic sul pulsante **Rimuovi** sulla barra degli strumenti.

## Per clonare un'entità di Data factory
Per clonare un'entità di Data factory di Azure esistente (servizio, tabella o pipeline collegata), selezionare l'entità nella visualizzazione albero e fare clic sul pulsante **Clona** sulla barra degli strumenti.

![Clonare un'entità di Data factory][clone-datafactory-entity]

Verrà creata una nuova bozza nel nodo **Bozze** nella visualizzazione albero.

## Per eliminare un'entità di Data factory
Per eliminare un'entità di Data factory di Azure (servizio, tabella o pipeline collegata), selezionare l'entità nella visualizzazione albero e fare clic su **Elimina** sulla barra degli strumenti oppure fare clic con il pulsante destro del mouse sull'entità e fare clic su **Elimina**.

![Eliminare un'entità di Data factory][delete-datafactory-entity]

## Vedere anche
Per istruzioni dettagliate per creare una data factory di Azure usando l'editor di Data factory, vedere l'argomento [Introduzione a Data factory di Azure][data-factory-get-started].

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png

<!---HONumber=Sept15_HO4-->