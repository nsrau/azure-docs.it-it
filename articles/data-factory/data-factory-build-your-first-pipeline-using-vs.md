<properties
	pageTitle="Creare la prima pipeline di Data factory di Azure con Visual Studio"
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
	ms.topic="article" 
	ms.date="10/06/2015"
	ms.author="spelluru"/>

# Creare la prima pipeline di Data factory di Azure con Visual Studio
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Questo articolo descrive come usare Visual Studio per creare la prima pipeline. Questa esercitazione include i passaggi seguenti:

2.	Creazione dei servizi collegati (archivi dati, risorse di calcolo).
3.	Creazione di un set di dati.
3.	Creazione di una pipeline.
4.	Creare una data factory e distribuire servizi collegati, set di dati e la pipeline. 

Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Per una panoramica dettagliata del servizio, vedere l'articolo [Introduzione al servizio Data factory di Azure](data-factory-introduction.md).

> [AZURE.IMPORTANT]Leggere l'articolo [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) e completare i prerequisiti prima di eseguire questa esercitazione.

## Procedura dettagliata: Creare e distribuire entità della data factory usando Visual Studio 

### Prerequisiti

Nel computer deve essere installato Visual Studio 2013. Scaricare Azure SDK per Visual Studio 2013. Andare alla [pagina di download di Azure](http://azure.microsoft.com/downloads/) e fare clic su **Installazione di Visual Studio 2013** nella sezione **.NET**. - Aggiornare "Strumenti di Visual Studio per Data factory di Azure" alla versione più recente passando a Visual Studio 2013 --> Strumenti --> Aggiornamenti --> Visual Studio Gallery e facendo clic su "Aggiorna" nella voce del plug-in "Strumenti di Visual Studio per Data factory di Azure".


### Creare il progetto di Visual Studio 
1. Avviare **Visual Studio 2013**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Verrà visualizzata la finestra di dialogo **Nuovo progetto**.  
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**. Se il modello DataFactory non è visibile, chiudere Visual Studio, installare Azure SDK per Visual Studio 2013 e riaprire Visual Studio.  

	![Finestra di dialogo Nuovo progetto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Immettere un **nome** per il progetto, un **percorso** e un nome per la **soluzione** e fare clic su **OK**.

	![Esplora soluzioni](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Creazione di servizi collegati
In questo passaggio si procederà al collegamento dell'account di archiviazione di Azure e di un cluster HDInsight di Azure su richiesta alla data factory e quindi alla creazione di un set di dati per rappresentare i dati di output dell'elaborazione Hive.


#### Creare il servizio collegato Archiviazione di Azure


4. Fare clic con il pulsante destro del mouse su **Servizi collegati** in Esplora soluzioni, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.      
5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** nell'elenco e fare clic su **Aggiungi**. 

	![Nuovo servizio collegato](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure.

	![Servizio collegato Archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Salvare il file **AzureStorageLinkedService1.json**.

#### Creare un servizio collegato Azure HDInsight
Si creerà ora un servizio collegato per il cluster HDInsight su richiesta che sarà usato per eseguire lo script Hive.

1. Fare clic con il pulsante destro del mouse su **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **Servizio collegato HDInsight su richiesta** e fare clic su **Aggiungi**. 
3. Sostituire il codice **JSON** con quanto segue:

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
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

4. Salvare il file **HDInsightOnDemandLinkedService1.json**.
 
### Creare il set di dati di output
Si creerà ora il set di dati di output per rappresentare i dati archiviati nell'archivio BLOB di Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Selezionare **BLOB di Azure** nell'elenco e quindi fare clic su **Aggiungi**. 
3. Sostituire il codice **JSON** nell'editor con quanto segue: nel frammento di codice JSON si crea un set di dati denominato **AzureBlobOutput** e si specifica la struttura dei dati che verranno generati dallo script Hive. Si specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **data** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
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

4. Salvare il file **AzureBlobLocation1.json**.


### Creazione della prima pipeline
In questo passaggio si creerà la prima pipeline.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Pipeline**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Selezionare **Pipeline di trasformazione Hive** nell'elenco e fare clic su **Aggiungi**. 
3. Sostituire **JSON** con il frammento seguente.

	> [AZURE.IMPORTANT]Sostituire **storageaccountname** con il nome del proprio account di archiviazione.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "AzureStorageLinkedService1",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
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
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **AzureStorageLinkedService1**) e in un contenitore denominato **script**.

	La sezione **extendedProperties** è usata per specificare le impostazioni di runtime che verranno passate allo script Hive come valori di configurazione Hive (ad esempio, ${hiveconf:PartitionedData}).

	Le proprietà **start** ed **end** della pipeline specificano il periodo attivo della pipeline.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata dal servizio collegato, **HDInsightOnDemandLinkedService**.
3. Salvare il file **HiveActivity1.json**.

### Aggiungere partitionweblogs.hql come dipendenza 

1. Fare clic con il pulsante destro del mouse su **Dipendenze** nella finestra **Esplora soluzioni**, scegliere **Aggiungi** e quindi fare clic su **Elemento esistente**.  
2. Individuare **C:\\ADFGettingStarted**, selezionare il file **partitionweblogs.hql** e quindi fare clic su **Aggiungi**. 

Quando si pubblica la soluzione nel passaggio successivo, il file HQL è caricato nel contenitore di script nell'archiviazione BLOB.

### Pubblicare/Distribuire le entità della data factory

18. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**. 
19. Se viene visualizzata la finestra di dialogo **Accedi al tuo account Microsoft**, immettere le credenziali per l'account che dispone di una sottoscrizione di Azure e fare clic su **Accedi**.
20. Verrà visualizzata la finestra di dialogo seguente:

	![Finestra di dialogo Pubblica](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Nella pagina Configura data factory, procedere come segue:
	1. Selezionare l'opzione**Crea nuova data factory**.
	2. Immettere **FirstPipelineUsingVS** nel campo **Nome**.
	3. Selezionare la sottoscrizione adatta per il campo **Sottoscrizione**. 
	4. Selezionare il **gruppo di risorse** per la data factory da creare. 
	5. Selezionare l'**area** per la data factory. 
	6. Fare clic su **Avanti** per passare alla pagina **Pubblica elementi** Premere **TAB** per uscire dal campo Nome se il pulsante **Avanti** è disabilitato. 
23. Nella pagina **Pubblica elementi** assicurarsi che tutte le data factory siano selezionate e fare clic su **Avanti** per passare alla pagina **Riepilogo**.     
24. Esaminare il riepilogo e fare clic su **Avanti** per avviare il processo di distribuzione e visualizzare lo **Stato distribuzione**.
25. Nella pagina **Stato distribuzione** viene visualizzato lo stato del processo di distribuzione. Fare clic su Fine dopo il termine della distribuzione. 
 

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

Vedere [Monitorare i set di dati e la pipeline](data-factory-monitor-manage-pipelines.md) per istruzioni su come usare il portale di anteprima di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione.
 

## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati da un BLOB di Azure ad Azure SQL](data-factory-get-started.md).
  
## Invia commenti e suggerimenti
I commenti e i suggerimenti su questo articolo possono essere molto utili. L'invio di commenti e suggerimenti tramite [posta elettronica](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-vs.md) richiede solo alcuni minuti.

<!---HONumber=Oct15_HO2-->