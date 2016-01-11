<properties
	pageTitle="Introduzione a Data Factory di Azure (Visual Studio)"
	description="In questa esercitazione si creerà una pipeline di esempio di Data factory di Azure usando Visual Studio."
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

# Creare la prima pipeline di Data factory di Azure con Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


Questo articolo descrive come usare Microsoft Visual Studio per creare la prima data factory di Azure.

## Prerequisiti

1. Prima di procedere è **necessario** leggere l'articolo [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) ed eseguire i passaggi richiesti.
2. Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Si consiglia di leggere l'articolo [Introduzione al servizio Data factory di Azure](data-factory-introduction.md) per una panoramica dettagliata del servizio.  

## Procedura dettagliata: Creare e distribuire entità della data factory usando Visual Studio 

### Prerequisiti

È necessario disporre dei seguenti prodotti installati nel computer in uso:

- Visual Studio 2013 o Visual Studio 2015
- Download di Azure SDK per Visual Studio 2013 o Visual Studio 2015. Passare alla [pagina di download di Azure](http://azure.microsoft.com/downloads/) e fare clic su **Visual Studio 2013** o **Visual Studio 2015** nella sezione **.NET**.
- Scaricare il plug-in Data factory di Azure più recente per Visual Studio: [Visual Studio 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [Visual Studio 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se si usa Visual Studio 2013, è anche possibile aggiornare il plug-in nel modo seguente: nel menu fare clic su **Strumenti** -> **Estensioni e aggiornamenti** -> **Online** -> **Visual Studio Gallery** -> **Strumenti di Data factory di Azure per Visual Studio** -> **Aggiorna**. 
	
	

### Creare il progetto di Visual Studio 
1. Avviare **Visual Studio 2013** o **Visual Studio 2015**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Verrà visualizzata la finestra di dialogo **Nuovo progetto**.  
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**.   

	![Finestra di dialogo Nuovo progetto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Immettere un **nome** per il progetto, un **percorso** e un nome per la **soluzione** e fare clic su **OK**.

	![Esplora soluzioni](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Creazione di servizi collegati
Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, attività di copia per copiare dati da un'origine a un archivio dati di destinazione e attività Hive di HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. Il nome e le impostazioni per la data factory verranno specificati in un secondo momento, quando si pubblica la soluzione Data factory.

In questo passaggio l'account di archiviazione di Azure e un cluster HDInsight su richiesta di Azure vengono collegati alla data factory. In questo esempio l'account di archiviazione di Azure contiene i dati di input e di output per la pipeline. In questo esempio il servizio collegato HDInsight viene usato per eseguire lo script Hive specificato nell'attività della pipeline. È necessario identificare l'archivio dati/i servizi di calcolo usati nello scenario e collegare tali servizi alla data factory creando servizi collegati.

#### Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. Ai fini dell'esercitazione, usare lo stesso account di archiviazione di Azure per archiviare i dati di input/output e il file script HQL.

4. Fare clic con il pulsante destro del mouse su **Servizi collegati** in Esplora soluzioni, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.      
5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** dell'elenco e fare clic su **Aggiungi**. 
3. Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere la sezione [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	![Servizio collegato Archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Salvare il file **AzureStorageLinkedService1.json**.

#### Creare un servizio collegato Azure HDInsight
In questo passaggio viene collegato un cluster HDInsight su richiesta alla data factory. Il cluster HDInsight viene creato automaticamente in fase di esecuzione ed eliminato al termine dell'elaborazione, se rimane inattivo per il periodo di tempo specificato. È possibile usare il proprio cluster HDInsight anziché un cluster HDInsight su richiesta. Per informazioni dettagliate, vedere [Servizi collegati di calcolo](data-factory-compute-linked-services.md).

1. Fare clic con il pulsante destro del mouse su **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **Servizio collegato HDInsight su richiesta** e fare clic su **Aggiungi**. 
3. Sostituire il codice **JSON** con quanto segue:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
		  }
		}
	
	La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:
	
	Proprietà | Descrizione
	-------- | -----------
	Versione | Specifica che la versione di HDInsight creata è 3.2. 
	ClusterSize | Crea un cluster HDInsight con un nodo. 
	TimeToLive | Specifica il tempo di inattività del cluster HDInsight, prima che sia eliminato.
	linkedServiceName | Specifica l'account di archiviazione che sarà usato per archiviare i log generati da HDInsight.

4. Salvare il file **HDInsightOnDemandLinkedService1.json**.

### Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto **AzureStorageLinkedService1** creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione di Azure e i set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.

#### Creare il set di dati di input

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Tabelle**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Selezionare **Blob di Azure** dall'elenco, cambiare il nome del file in **OutputDataset.json** e quindi fare clic su **Aggiungi**.
3. Sostituire il codice **JSON** nell'editor con quanto segue: 

	Nel frammento di codice JSON si crea un set di dati denominato **AzureBlobInput** che rappresenta i dati di input per un'attività nella pipeline. Si specifica anche che i dati di input si trovano nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **inputdata**.
		
		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "AzureStorageLinkedService1",
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
	| linkedServiceName | Fa riferimento all'oggetto AzureStorageLinkedService1 creato in precedenza. |
	| fileName | Questa proprietà è facoltativa. Se si omette questa proprietà, vengono prelevati tutti i file da folderPath. In tal caso viene elaborato solo il file input.log. |
	| type | I file di log sono in formato testo, si usa quindi TextFormat. | 
	| columnDelimiter | Le colonne nei file di log sono delimitate da virgola (,). |
	| frequenza/intervallo | La frequenza è impostata su Month e l'intervallo è 1, ciò significa che le sezioni di input sono disponibili con cadenza mensile. | 
	| external | Questa proprietà è impostata su true se i dati di input non vengono generati dal servizio Data factory. | 
	  
	
3. Salvare il file **InputDataset.json**.

 
#### Creare il set di dati di output
Viene creato ora il set di dati di output per rappresentare i dati di output archiviati nell'archiviazione BLOB di Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Tabelle**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Selezionare **Blob di Azure** dall'elenco, cambiare il nome del file in **OutputDataset.json** e quindi fare clic su **Aggiungi**. 
3. Sostituire il codice **JSON** nell'editor con quanto segue: 

	Nel frammento di codice JSON si crea un set di dati denominato **AzureBlobOutput** e si specifica la struttura dei dati che verranno generati dallo script Hive. Si specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

4. Salvare il file **OutputDataset.json**.


### Creazione della prima pipeline
In questo passaggio viene creata la prima pipeline con un'attività **HDInsightHive**. Si noti che la sezione di input è disponibile ogni mese (frequenza: Month, intervallo: 1), la sezione di output viene generata ogni mese e anche la proprietà dell'utilità di pianificazione dell'attività è impostata su una frequenza mensile (vedere sotto). Le impostazioni per il set di dati di output e l'utilità di pianificazione dell'attività devono corrispondere. In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. Le proprietà usate nel codice JSON seguente sono illustrate in fondo a questa sezione.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Pipeline**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Selezionare **Pipeline di trasformazione Hive** dell'elenco e fare clic su **Aggiungi**. 
3. Sostituire il codice **JSON** con il frammento seguente.

	> [AZURE.IMPORTANT]Sostituire **storageaccountname** con il nome del proprio account di archiviazione.

		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "AzureStorageLinkedService1",
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
	
	Nel frammento di codice JSON si crea una pipeline costituita da una singola attività che usa Hive per elaborare i dati in un cluster HDInsight.
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **AzureStorageLinkedService1**) e nella cartella **script** nel contenitore **adfgetstarted**.

	La sezione **defines** è usata per specificare le impostazioni di runtime che verranno passate allo script Hive come valori di configurazione Hive (ad esempio, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Le proprietà **start** ed **end** della pipeline ne specificano il periodo attivo.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata da **linkedServiceName** - **HDInsightOnDemandLinkedService**.

3. Salvare il file **HiveActivity1.json**.

### Aggiungere partitionweblogs.hql e input.log come dipendenza 

1. Fare clic con il pulsante destro del mouse su **Dipendenze** nella finestra **Esplora soluzioni**, scegliere **Aggiungi** e quindi fare clic su **Elemento esistente**.  
2. Passare a **C:\\ADFGettingStarted**, selezionare i file **partitionweblogs.hql** e **input.log** e quindi fare clic su **Aggiungi**. Questi due file sono stati creati come prerequisiti nella [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md).

Quando si pubblica la soluzione nel passaggio successivo, il file **partitionweblogs.hql** viene caricato nella cartella degli script nel contenitore BLOB **adfgetstarted**.

### Pubblicare/Distribuire le entità della data factory

18. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**. 
19. Se viene visualizzato **Accedere al proprio account Microsoft**, nella finestra di dialogo immettere le credenziali per l'account associato alla sottoscrizione di Azure e fare clic su **Accedi**.
20. Verrà visualizzata la finestra di dialogo seguente:

	![Finestra di dialogo Pubblica](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Nella pagina Configura data factory, procedere come segue:
	1. Selezionare l'opzione **Crea nuova data factory**.
	2. In **Nome** immettere **FirstDataFactoryUsingVS**. 
	
		> [AZURE.IMPORTANT]È necessario specificare un nome univoco globale per la Data factory di Azure. Se viene visualizzato l'errore **Il nome "FirstDataFactoryUsingVS" per la data factory non è disponibile** al momento della pubblicazione, modificare il nome (ad esempio, nomeutenteFirstDataFactoryUsingVS). Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md).
		> 
		> Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.
	3. Selezionare la sottoscrizione adatta per il campo **Sottoscrizione**. 
	4. Selezionare il **gruppo di risorse** per la data factory da creare. 
	5. Selezionare l'**area** per la data factory. 
	6. Fare clic su **Avanti** per passare alla pagina **Pubblica elementi**. Premere **TAB** per uscire dal campo Nome se il pulsante **Avanti** è disabilitato. 
23. Nella pagina **Pubblica elementi** assicurarsi che tutte le data factory siano selezionate e fare clic su **Avanti** per passare alla pagina **Riepilogo**.     
24. Esaminare il riepilogo e fare clic su **Avanti** per avviare il processo di distribuzione e visualizzare lo **Stato della distribuzione**.
25. Nella pagina **Stato della distribuzione** è possibile visualizzare lo stato del processo di distribuzione. Fare clic su Fine dopo il termine della distribuzione. 
 
## Passaggio 4: Monitorare la pipeline

6. Accedere al [portale di Azure](http://portal.azure.com/) e seguire questa procedura:
	1. Fare clic su **Sfoglia** e selezionare **Data factory**. ![Esplora data factory](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
	2. Selezionare **FirstDataFactoryUsingVS** dall'elenco di data factory. 
7. Nella home page della data factory fare clic su **Diagramma**.
  
	![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. In Vista diagramma saranno visualizzati una panoramica della pipeline e i set di dati usati in questa esercitazione.
	
	![Vista Diagramma](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Per visualizzare tutte le attività nella pipeline, fare clic con il pulsante destro del mouse sulla pipeline nel diagramma e scegliere Apri pipeline. 

	![Menu Apri pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Assicurarsi che l'attività HDInsightHive sia visualizzata nella pipeline. 
  
	![Visualizzazione Apri pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

	Per tornare alla visualizzazione precedente, fare clic su **Data factory** nel menu di navigazione nella parte superiore. 
10. In **Vista diagramma** fare doppio clic sul set di dati **AzureBlobInput**. Verificare che lo stato della sezione sia **Pronto**. Potrebbero essere necessari alcuni minuti perché lo stato della sezione venga visualizzato come Pronto. Se dopo qualche minuto ciò non accade, verificare che il file di input, input.log, sia posizionato nel contenitore adfgetstarted e nella cartella inputdata corretti.

	![Sezione di input nello stato Pronto](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Fare clic su **X** per chiudere il pannello **AzureBlobInput**. 
12. In **Vista diagramma** fare doppio clic sul set di dati **AzureBlobOutput**. Verrà visualizzata la sezione in fase di elaborazione.

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Al termine dell'elaborazione lo stato della sezione sarà **Pronto**.
	>[AZURE.IMPORTANT]La creazione di un cluster HDInsight su richiesta di solito richiede tempo (circa 20 minuti).  

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)
	
10. Quando lo stato della sezione è **Pronto**, cercare i dati di output nella cartella **partitioneddata** del contenitore **adfgetstarted** nell'archivio BLOB.
 
	![Dati di output](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)

## Usare Esplora Server per esaminare le entità della data factory

1. In **Visual Studio** fare clic su **Visualizza** nel menu e quindi fare clic su **Esplora server**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzata la finestra **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione verrà visualizzato nella finestra **Elenco attività data factory**.

	![Esplora server](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. È possibile fare clic con il pulsante destro del mouse su una data factory e scegliere l'opzione **Esporta data factory in un nuovo progetto** per creare un progetto di Visual Studio basato su una data factory esistente.

	![Data factory di Azure](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Aggiornare gli strumenti di Data factory di Azure per Visual Studio

Per aggiornare gli strumenti di Data Factory di Azure per Visual Studio, eseguire le operazioni seguenti:

1. Fare clic su **Strumenti** nel menu e selezionare **Estensioni e aggiornamenti**.
2. Selezionare **Aggiornamenti** nel riquadro di sinistra e quindi selezionare **Visual Studio Gallery**.
3. Selezionare **Strumenti di Data factory di Azure per Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento. 

Per istruzioni su come usare il portale di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione, vedere [Monitorare i set di dati e la pipeline](data-factory-monitor-manage-pipelines.md).
 

## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL](data-factory-get-started.md).
  

<!---HONumber=AcomDC_1223_2015-->