<properties
	pageTitle="Creare la prima pipeline di Data factory di Azure con l'editor di Data factory"
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
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# Creare la prima pipeline di Data factory di Azure con l'editor di Data factory (portale di Azure)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Questo articolo descrive come usare il [portale di anteprima di Azure](https://portal.azure.com/) per creare la prima pipeline. Questa esercitazione include i passaggi seguenti:

1.	Creazione della data factory
2.	Creazione dei servizi collegati (archivi dati, risorse di calcolo) e dei set di dati
3.	Creazione della pipeline

Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Per una panoramica dettagliata del servizio, vedere l'articolo [Introduzione al servizio Data factory di Azure](data-factory-introduction.md).

> [AZURE.IMPORTANT]Leggere l'articolo [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) e completare i prerequisiti prima di eseguire questa esercitazione.

## Passaggio 1: Creazione della data factory

1.	Dopo l'accesso al [portale di anteprima di Azure](http://portal.azure.com/), seguire questa procedura:
	1.	Fare clic su **NUOVO** nel menu a sinistra. 
	2.	Fare clic su **Analisi dei dati** nel pannello **Crea**.
	3.	Fare clic su **Data factory** nel pannello **Analisi dei dati**.

		![Pannello Crea](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	Nel pannello **Nuova data factory** immettere **DataFactoryMyFirstPipeline** nel campo Nome.

	![Pannello Nuova data factory](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato un errore simile a **Nome "DataFactoryMyFirstPipeline" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteDataFactoryMyFirstPipeline) e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md).
	>  
	> Il nome della data factory può essere registrato in futuro come un nome DNS e pertanto divenire visibile pubblicamente.

3.	Se non è stato creato un gruppo di risorse, sarà necessario crearne uno. A tale scopo, seguire questa procedura:
	1.	Fare clic su **NOME GRUPPO DI RISORSE**.
	2.	Selezionare **Crea un nuovo gruppo di risorse** nel pannello **Gruppo di risorse**.
	3.	Immettere **ADF** nel campo **Nome** del pannello **Crea gruppo di risorse**.
	4.	Fare clic su **OK**.
	
		![Creare gruppo di risorse](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	Dopo aver selezionato il gruppo di risorse, verificare se la sottoscrizione in cui si vuole creare la data factory è quella corretta.
5.	Fare clic su **Crea** nel pannello **Nuova data factory**.
6.	Nella **Schermata iniziale** del portale di anteprima di Azure verrà visualizzata la data factory in fase di creazione:   

	![Stato creazione della data factory](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Congratulazioni. La creazione della prima data factory è così completata. Dopo aver creato la data factory, verrà visualizzata la pagina corrispondente con elencato il contenuto della data factory. 	

	![Pannello Data factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Nei passaggi successivi si apprenderà come creare i servizi collegati, i set di dati e la pipeline da usare in questa esercitazione.

## Passaggio 2: Creare servizi collegati e set di dati
In questo passaggio si procederà al collegamento dell'account di archiviazione di Azure e di un cluster HDInsight di Azure su richiesta alla data factory e quindi alla creazione di un set di dati per rappresentare i dati di output dell'elaborazione Hive.

### Creare il servizio collegato Archiviazione di Azure
1.	Fare clic su **Crea e distribuisci** nel pannello **DATA FACTORY** relativo a **DataFactoryFirstPipeline**. Verrà avviato l'editor di Data factory. 
	 
	![Riquadro Creare e distribuire](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Fare clic su **Nuovo archivio dati** e scegliere **Archiviazione di Azure**.
	
	![Servizio collegato Archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	Nell'editor verrà visualizzato lo script JSON per la creazione di un servizio collegato Archiviazione di Azure. 
4. Sostituire **account name** con il nome dell'account di archiviazione di Azure e **account key** con la chiave di accesso dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
5. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

	![Pulsante Distribuisci](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Creare un servizio collegato Azure HDInsight
Si creerà ora un servizio collegato per il cluster HDInsight su richiesta che sarà usato per eseguire lo script Hive.

1. Nell'**editor di Data factory** fare clic su **Nuovo calcolo** sulla barra dei comandi e selezionare **Cluster HDInsight su richiesta**.

	![Nuovo calcolo](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copiare e incollare il frammento di codice sottostante nella finestra Bozza-1. Il frammento di codice JSON descrive le proprietà che saranno usate per creare il cluster HDInsight su richiesta. 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:
	
	Proprietà | Descrizione
	-------- | -----------
	Versione | Specifica che la versione di HDInsight creata è 3.1. 
	ClusterSize | Crea un cluster HDInsight con un nodo. 
	TimeToLive | Specifica il tempo di inattività del cluster HDInsight, prima che sia eliminato.
	JobsContainer | Specifica il nome del contenitore di processo che sarà creato per archiviare i log generati da HDInsight.
	linkedServiceName | Specifica l'account di archiviazione che sarà usato per archiviare i log generati da HDInsight.
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato. 
4. Verificare che nella visualizzazione albero a sinistra siano presenti sia StorageLinkedService che HDInsightOnDemandLinkedService.

	![Visualizzazione albero con servizi collegati](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### Creare il set di dati di output
Si creerà ora il set di dati di output per rappresentare i dati archiviati nell'archivio BLOB di Azure.

1. Nell'**editor di Data factory** fare clic su **Nuovo set di dati** sulla barra dei comandi e selezionare **Archivio BLOB di Azure**.  

	![Nuovo set di dati](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copiare e incollare il frammento di codice sottostante nella finestra Bozza-1. Nel frammento di codice JSON si crea un set di dati denominato **AzureBlobOutput** e si specifica la struttura dei dati che verranno generati dallo script Hive. Si specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **data** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
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

3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati appena creato.
4. Verificare se il set di dati è stato creato correttamente.

	![Visualizzazione albero con servizi collegati](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Passaggio 3: Creazione della prima pipeline
In questo passaggio si creerà la prima pipeline.

1. Nell'**editor di Data factory** fare clic sui **puntini di sospensione (…)** e quindi su **Nuova pipeline**.
	
	![Pulsante Nuova pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copiare e incollare il frammento di codice sottostante nella finestra Bozza-1.

	> [AZURE.IMPORTANT]Nel codice JSON sostituire **storageaccountname** con il nome dell'account di archiviazione.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}
 
	Nel frammento di codice JSON si crea una pipeline costituita da una singola attività che usa Hive per elaborare i dati in un cluster HDInsight.
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **StorageLinkedService**) e in un contenitore denominato **script**.

	La sezione **extendedProperties** consente di specificare le impostazioni di runtime che verranno passate allo script Hive come valori di configurazione Hive (ad esempio, ${hiveconf:PartitionedData}).

	Le proprietà **start** ed **end** della pipeline specificano il periodo attivo della pipeline.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo indicata dal servizio collegato, **HDInsightOnDemandLinkedService**.
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.
4. Verificare che la pipeline sia visibile nella visualizzazione albero.

	![Visualizzazione albero con pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. La creazione della prima pipeline è così completata.
6. Fare clic su **X** per chiudere i pannelli dell'editor di Data factory, tornare al pannello Data factory e quindi fare clic su **Diagramma**.
  
	![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. In Vista diagramma saranno visualizzati una panoramica della pipeline e i set di dati usati in questa esercitazione.
	
	![Vista diagramma](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. In Vista diagramma fare doppio clic sul set di dati **AzureBlobOutput**. Verrà visualizzata la sezione in fase di elaborazione.

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Al termine dell'elaborazione lo stato della sezione sarà **Pronto**. La creazione di un cluster HDInsight su richiesta di solito richiede tempo. 

	![Set di dati](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. Quando lo stato sella sezione è **Ready**, cercare i dati di output nella cartella **partitioneddata** del contenitore **data** nell'archivio BLOB.  
 

 

## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati da un BLOB di Azure ad Azure SQL](./data-factory-get-started.md).
  

<!---HONumber=Nov15_HO3-->