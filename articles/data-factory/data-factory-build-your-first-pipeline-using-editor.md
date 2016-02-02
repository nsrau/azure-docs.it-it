<properties
	pageTitle="Introduzione a Data factory di Azure (portale di Azure)"
	description="In questa esercitazione si creerà una pipeline di esempio di Data factory di Azure usando l'editor di Data factory nel portale di Azure."
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
	ms.topic="hero-article" 
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Introduzione a Data Factory di Azure (Editor di Data Factory)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)

Questo articolo descrive come usare il [portale di Azure](https://portal.azure.com/) per creare la prima data factory di Azure.

## Prerequisiti

1. Prima di procedere è **necessario** leggere l'articolo [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) ed eseguire i passaggi richiesti.
2. Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Si consiglia di leggere l'articolo [Introduzione al servizio Data factory di Azure](data-factory-introduction.md) per una panoramica dettagliata del servizio.  

## Passaggio 1: Creare la data factory
Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, attività di copia per copiare dati da un'origine a un archivio dati di destinazione e attività Hive di HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. In questo passaggio iniziale viene creata la data factory.

1.	Dopo l'accesso al [portale di Azure](https://portal.azure.com/), seguire questa procedura:
	1.	Fare clic su **NUOVO** nel menu a sinistra. 
	2.	Fare clic su **Analisi dei dati** nel pannello **Crea**.
	3.	Fare clic su **Data factory** nel pannello **Analisi dei dati**.

		![Pannello Crea](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	Nel pannello **Nuova data factory** immettere **GetStartedDF** come nome.

	![Pannello Nuova data factory](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato l'errore **Il nome "GetStartedDF" per la data factory non è disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteGetStartedDF) e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md).
	>  
	> Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.

3.	Selezionare la **sottoscrizione di Azure** in cui creare la data factory.
4.	Selezionare un **gruppo di risorse** esistente o crearne uno nuovo. Ai fini dell'esercitazione, creare un gruppo di risorse denominato **ADFGetStartedRG**.    
5.	Fare clic su **Crea** nel pannello **Nuova data factory**.
6.	Nella **Schermata iniziale** del portale di Azure verrà visualizzata la data factory in fase di creazione:   

	![Stato creazione della data factory](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Congratulazioni. La creazione della prima data factory è così completata. Dopo aver creato la data factory, verrà visualizzata la pagina corrispondente con elencato il contenuto della data factory. 	

	![Pannello Data factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Prima di creare una pipeline è necessario creare alcune entità di Data factory. Creare prima di tutto i servizi collegati per collegare archivi dati/servizi di calcolo all'archivio dati, definire i set di dati di input e di output per rappresentare i dati negli archivi dati collegati e quindi creare la pipeline con un'attività che usa questi set di dati.

## Passaggio 2: Creare servizi collegati
In questo passaggio l'account di archiviazione di Azure e un cluster HDInsight su richiesta di Azure vengono collegati alla data factory. In questo esempio l'account di archiviazione di Azure contiene i dati di input e di output per la pipeline. In questo esempio il servizio collegato HDInsight viene usato per eseguire lo script Hive specificato nell'attività della pipeline. È necessario identificare l'archivio dati/i servizi di calcolo usati nello scenario e collegare tali servizi alla data factory creando servizi collegati.

### Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. Ai fini dell'esercitazione, usare lo stesso account di archiviazione di Azure per archiviare i dati di input/output e il file script HQL.

1.	Fare clic su **Crea e distribuisci** nel pannello **DATA FACTORY** relativo a **GetStartedDF**. Verrà avviato l'editor di Data factory. 
	 
	![Riquadro Creare e distribuire](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Fare clic su **Nuovo archivio dati** e scegliere **Archiviazione di Azure**.
	
	![Servizio collegato Archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Nell'editor verrà visualizzato lo script JSON per la creazione di un servizio collegato Archiviazione di Azure. 
4. Sostituire **account name** con il nome dell'account di archiviazione di Azure e **account key** con la chiave di accesso dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere la sezione [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
5. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

	![Pulsante Distribuisci](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Al termine della distribuzione del servizio collegato, la finestra **Bozza-1** viene nascosta e viene visualizzato **StorageLinkedService** nella visualizzazione albero a sinistra. ![Servizio collegato Archiviazione nel menu](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)

 
### Creare un servizio collegato Azure HDInsight
In questo passaggio viene collegato un cluster HDInsight su richiesta alla data factory. Il cluster HDInsight viene creato automaticamente in fase di esecuzione ed eliminato al termine dell'elaborazione, se rimane inattivo per il periodo di tempo specificato.

1. Nell'**editor di Data factory** fare clic su **Nuovo calcolo** sulla barra dei comandi e selezionare **Cluster HDInsight su richiesta**.

	![Nuovo calcolo](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copiare e incollare il frammento di codice sottostante nella finestra **Bozza-1**. Il frammento di codice JSON descrive le proprietà che saranno usate per creare il cluster HDInsight su richiesta. 

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
	| ClusterSize | Crea un cluster HDInsight con un nodo. | 
	| TimeToLive | Specifica il tempo di inattività del cluster HDInsight, prima che sia eliminato. |
	| linkedServiceName | Specifica l'account di archiviazione che sarà usato per archiviare i log generati da HDInsight. |

	Tenere presente quanto segue:
	
	- Data factory crea automaticamente un cluster HDInsight **basato su Windows** con il codice JSON precedente. È anche possibile fare in modo che crei un cluster HDInsight **basato su Linux**. Per i dettagli, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
	- È possibile usare **il proprio cluster HDInsight** invece di un cluster HDInsight su richiesta. Per i dettagli, vedere [Servizio collegato HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
	- Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Si tratta di un comportamento previsto da progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**) che viene eliminato al termine dell'elaborazione.
	
		Man mano che vengono elaborate sempre più sezioni, verranno visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. Il nome di questi contenitori segue uno schema: "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp". Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Storage Explorer](http://storageexplorer.com/).

	Per i dettagli, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato. 
4. Verificare che nella visualizzazione albero a sinistra siano presenti sia **StorageLinkedService** che **HDInsightOnDemandLinkedService**.

	![Visualizzazione albero con servizi collegati](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## Passaggio 3: Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto **StorageLinkedService** creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione di Azure e i set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.

### Creare il set di dati di input

1. In **Editor di Data factory** fare clic su **Nuovo set di dati** sulla barra dei comandi e selezionare **Archivio BLOB di Azure**.

	![Nuovo set di dati](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copiare e incollare il frammento di codice sottostante nella finestra Bozza-1. Nel frammento di codice JSON si crea un set di dati denominato **AzureBlobInput** che rappresenta i dati di input per un'attività nella pipeline. Si specifica anche che i dati di input si trovano nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **inputdata**.
		
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

	La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

	| Proprietà | Descrizione |
	| :------- | :---------- |
	| type | La proprietà type è impostata su AzureBlob perché i dati si trovano nell'archiviazione BLOB di Azure. |  
	| linkedServiceName | Fa riferimento all'oggetto StorageLinkedService creato in precedenza. |
	| fileName | Questa proprietà è facoltativa. Se si omette questa proprietà, vengono prelevati tutti i file da folderPath. In tal caso viene elaborato solo il file input.log. |
	| type | I file di log sono in formato testo, si usa quindi TextFormat. | 
	| columnDelimiter | Le colonne nei file di log sono delimitate da virgola (,). |
	| frequenza/intervallo | La frequenza è impostata su Month e l'intervallo è 1, ciò significa che le sezioni di input sono disponibili con cadenza mensile. | 
	| external | Questa proprietà è impostata su true se i dati di input non vengono generati dal servizio Data factory. | 
	  
	
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati appena creato. Il set di dati viene visualizzato nella visualizzazione albero a sinistra.


### Creare il set di dati di output
Viene creato ora il set di dati di output per rappresentare i dati di output archiviati nell'archiviazione BLOB di Azure.

1. Nell'**editor di Data factory** fare clic su **Nuovo set di dati** sulla barra dei comandi e selezionare **Archivio BLOB di Azure**.  
2. Copiare e incollare il frammento di codice sottostante nella finestra Bozza-1. Nel frammento di codice JSON si crea un set di dati denominato **AzureBlobOutput** e si specifica la struttura dei dati che verranno generati dallo script Hive. Si specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.
	
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

	Per le descrizioni di queste proprietà, vedere la sezione **Creare il set di dati di input**. La proprietà esterna non viene impostata su un set di dati di output perché il set di dati viene generato dal servizio Data factory.
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati appena creato.
4. Verificare se il set di dati è stato creato correttamente.

	![Visualizzazione albero con servizi collegati](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Passaggio 4: Creare la prima pipeline
In questo passaggio viene creata la prima pipeline con un'attività **HDInsightHive**. Si noti che la sezione di input è disponibile ogni mese (frequenza: Month, intervallo: 1), la sezione di output viene generata ogni mese e anche la proprietà dell'utilità di pianificazione dell'attività è impostata su una frequenza mensile (vedere sotto). Le impostazioni per il set di dati di output e l'utilità di pianificazione dell'attività devono corrispondere. In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. Le proprietà usate nel codice JSON seguente sono illustrate in fondo a questa sezione.

1. Nell'**editor di Data factory** fare clic sui **puntini di sospensione (…)** e quindi su **Nuova pipeline**.
	
	![Pulsante Nuova pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copiare e incollare il frammento di codice sottostante nella finestra Bozza-1.

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
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}
 
	Nel frammento di codice JSON si crea una pipeline costituita da una singola attività che usa Hive per elaborare i dati in un cluster HDInsight.
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **StorageLinkedService**) e nella cartella **script** nel contenitore **adfgetstarted**.

	La sezione **defines** è usata per specificare le impostazioni di runtime che verranno passate allo script Hive come valori di configurazione Hive (ad esempio, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Le proprietà **start** ed **end** della pipeline ne specificano il periodo attivo.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata da **linkedServiceName** - **HDInsightOnDemandLinkedService**.


3. Verificare che il file input.log si trovi nella cartella adfgetstarted/inputdata nell'archiviazione BLOB di Azure e fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline. Dal momento che gli orari di **inizio** e **fine** sono impostati nel passato e **isPaused** è impostato su false, la pipeline (l'attività nella pipeline) viene eseguita immediatamente dopo la distribuzione.
4. Verificare che la pipeline sia visibile nella visualizzazione albero.

	![Visualizzazione albero con pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. La creazione della prima pipeline è così completata.

## Passaggio 4: Monitorare la pipeline

6. Fare clic su **X** per chiudere i pannelli dell'editor di Data factory, tornare al pannello Data factory e quindi fare clic su **Diagramma**.
  
	![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. In Vista diagramma saranno visualizzati una panoramica della pipeline e i set di dati usati in questa esercitazione.
	
	![Vista Diagramma](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Per visualizzare tutte le attività nella pipeline, fare clic con il pulsante destro del mouse sulla pipeline nel diagramma e scegliere Apri pipeline. 

	![Menu Apri pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Assicurarsi che l'attività HDInsightHive sia visualizzata nella pipeline. 
  
	![Visualizzazione Apri pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

	Per tornare alla visualizzazione precedente, fare clic su **Data factory** nel menu di navigazione nella parte superiore. 
10. In **Vista diagramma** fare doppio clic sul set di dati **AzureBlobInput**. Verificare che lo stato della sezione sia **Pronto**. Potrebbero essere necessari alcuni minuti perché lo stato della sezione venga visualizzato come Pronto. Se dopo qualche minuto ciò non accade, verificare che il file di input, input.log, sia posizionato nel contenitore adfgetstarted e nella cartella inputdata corretti.

	![Sezione di input nello stato Pronto](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Fare clic su **X** per chiudere il pannello **AzureBlobInput**. 
12. In **Vista diagramma** fare doppio clic sul set di dati **AzureBlobOutput**. Verrà visualizzata la sezione in fase di elaborazione.

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Al termine dell'elaborazione lo stato della sezione sarà **Pronto**.
	>[AZURE.IMPORTANT] La creazione di un cluster HDInsight su richiesta di solito richiede tempo (circa 20 minuti).  

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)
	
10. Quando lo stato della sezione è **Pronto**, cercare i dati di output nella cartella **partitioneddata** del contenitore **adfgetstarted** nell'archivio BLOB.
 
	![Dati di output](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL](./data-factory-get-started.md).

### Riferimenti
| Argomento | Descrizione |
| :---- | :---- |
| [Pipeline](data-factory-create-pipelines.md) | Questo articolo consentirà di conoscere le pipeline e le attività in Data factory di Azure e su come sfruttarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) | Questo articolo illustra i set di dati in Data factory di Azure.
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) | Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md) | Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline. Contiene anche informazioni su come creare avvisi e ricevere notifiche sugli errori. |


  

<!---HONumber=AcomDC_0128_2016-->