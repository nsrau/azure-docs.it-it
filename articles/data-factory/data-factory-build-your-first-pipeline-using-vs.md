<properties
	pageTitle="Creare la prima pipeline con Data factory di Azure"
	description="Questa esercitazione mostra come creare una pipeline di dati di esempio che trasforma i dati usando Azure HDInsight con Visual Studio"
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
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Creare la prima pipeline con Data factory di Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Questo articolo descrive come usare Visual Studio per creare la prima pipeline. Questa esercitazione include i passaggi seguenti:

1.	Creazione della data factory
2.	Creazione dei servizi collegati (archivi dati, risorse di calcolo) e set di dati
3.	Creazione della pipeline

Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Per una panoramica dettagliata del servizio, vedere l'articolo [Introduzione al servizio Data factory di Azure](data-factory-introduction.md).

## Passaggio 1: Creazione della data factory

1.	Dopo l'accesso al [portale di anteprima di Azure](http://portal.azure.com/), eseguire le operazioni seguenti:
	1.	Fare clic su **NUOVO** nell'angolo inferiore sinistro. 
	2.	Fare clic su **Analisi dei dati** nel pannello **Crea**.
	3.	Fare clic su **Data factory** nel pannello **Analisi dei dati**.

		![Pannello Crea](./media/data-factory-build-your-first-pipeline-using-vs/create-blade.png)

2.	Nel pannello **Nuova data factory** immettere **DataFactoryMyFirstPipeline** come Nome.

	![Pannello Nuova data factory](./media/data-factory-build-your-first-pipeline-using-vs/new-data-factory-blade.png)

	> [AZURE.IMPORTANT] 
3.	Se non è stato creato un gruppo di risorse, sarà necessario crearne uno. A tale scopo, effettuare l'operazione seguente:
	1.	Fare clic su **NOME DEL GRUPPO DI RISORSE**.
	2.	Selezionare **Crea un nuovo gruppo di risorse** nel pannello **Gruppo di risorse**.
	3.	Immettere **ADF** come **Nome** nel pannello **Crea gruppo di risorse**.
	4.	Fare clic su **OK**.
	
		![Creare gruppo di risorse](./media/data-factory-build-your-first-pipeline-using-vs/create-resource-group.png)
4.	Dopo aver selezionato il gruppo di risorse, verificare se la sottoscrizione in cui si vuole creare la data factory è quella corretta.
5.	Fare clic su **Crea** nel pannello **Nuova data factory**.
6.	Nella **Schermata iniziale** del portale di anteprima di Azure verrà visualizzata la data factory che si sta creando:   

	![Stato creazione della data factory](./media/data-factory-build-your-first-pipeline-using-vs/creating-data-factory-image.png)
7. Congratulazioni. La creazione della prima data factory è così completata. Dopo aver creato la data factory, verrà visualizzata la pagina corrispondente con elencato il contenuto della data factory. 	

	![Pannello Data factory](./media/data-factory-build-your-first-pipeline-using-vs/data-factory-blade.png)

Nei passaggi successivi si apprenderà come creare i servizi collegati, i set di dati e la pipeline da usare in questa esercitazione.

## Procedura dettagliata: Creare e distribuire entità della data factory usando Visual Studio 

### Prerequisiti

Nel computer deve essere installato Visual Studio 2013. Scaricare Azure SDK per Visual Studio 2013. Passare alla [pagina di download di Azure](http://azure.microsoft.com/downloads/) e fare clic su **VS 2013 - Installazione** nella sezione **.NET**.


### Creare il progetto di Visual Studio 
1. Avviare **Visual Studio 2013**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Verrà visualizzata la finestra di dialogo **Nuovo progetto**.  
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**. Se il modello DataFactory non è visibile, chiudere Visual Studio, installare Azure SDK per Visual Studio 2013 e riaprire Visual Studio.  

	![Finestra di dialogo Nuovo progetto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Immettere un **nome** per il progetto, un **percorso** e un nome per la **soluzione**, quindi fare clic su **OK**.

	![Esplora soluzioni](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### Creazione di servizi collegati
In questo passaggio si procederà al collegamento dell'account di archiviazione di Azure e di un cluster HDInsight di Azure su richiesta alla data factory e quindi alla creazione di un set di dati per rappresentare i dati di output dell'elaborazione Hive.


#### Creare il servizio collegato Archiviazione di Azure


4. Fare clic con il pulsante destro del mouse su **Servizi collegati** in Esplora soluzioni, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.      
5. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** nell'elenco e fare clic su **Aggiungi**. 

	![Nuovo servizio collegato](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. Sostituire **<accountname>** e **<accountkey>** con il nome e la chiave dell'account di archiviazione di Azure.

	![Servizio collegato di archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Salvare il file **AzureStorageLinkedService1.json**.

#### Creare un servizio collegato Azure HDInsight
Si creerà ora un servizio collegato per il cluster HDInsight su richiesta che sarà usato per eseguire lo script Hive.

1. Fare clic con il pulsante destro del mouse su **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **Servizio collegato HDInsight su richiesta** e fare clic su **Aggiungi**. 
3. Sostituire **JSON** con quanto segue:

		{
		    "name": "HDInsightOnDemandLinkedService",
		    "properties": {
		        "version": "3.1",
		        "clusterSize": 1,
		        "timeToLive": "00:05:00",
		        "jobsContainer": "adfjobs",
		        "linkedServiceName": "StorageLinkedService",
		        "type": "HDInsightOnDemandLinkedService"
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

1. Fare clic con il pulsante destro del mouse in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Selezionare **BLOB di Azure** nell'elenco, quindi fare clic su **Aggiungi**. 
3. Sostituire il codice **JSON** nell'editor con quanto segue: nel frammento di codice JSON si crea un set di dati denominato **AzureBlobOutput** e si specifica la struttura dei dati che verranno generati dallo script Hive. Si specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **data** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene generato su base mensile.
	
		{
		    "name": "AzureBlobOutput",
		    "properties": {
		        "location": {
		            "type": "AzureBlobLocation",
		            "folderPath": "data/partitioneddata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            },
		            "linkedServiceName": "StorageLinkedService"
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

1. Fare clic con il pulsante destro del mouse su **Pipeline** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Selezionare **Pipeline di trasformazione Hive** nell'elenco e fare clic su **Aggiungi**. 
3. Sostituire **JSON** con il frammento di codice seguente e sostituire **storageaccountname** con il nome del proprio account di archiviazione.

		{
			"name": "MyFirstPipeline",
			"properties": {
			"description": "My first Azure Data Factory pipeline",
		 	"activities": [
		      {
		            "type": "HDInsightActivity",
		            "transformation": {
		                    "scriptPath": "script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "type": "Hive",
		                    "extendedProperties": {
		                        "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "outputs": [   {  "name": "AzureBlobOutput"    }   ],
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
	
	Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure specificato da scriptLinkedService, denominato **StorageLinkedService**, e in un contenitore denominato **script**.

	La sezione **extendedProperties** è usata per specificare le impostazioni di runtime che verranno passate allo script Hive come valori di configurazione Hive (ad esempio, ${hiveconf:PartitionedData}).

	Le proprietà **start** e **end** della pipeline specificano il periodo attivo della pipeline.

	Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata dal servizio collegato, **HDInsightOnDemandLinkedService**.
3. Salvare il file **HiveActivity1.json**. 

### Pubblicare/Distribuire le entità della data factory
  
1. Nell'area della barra degli strumenti fare clic con il pulsante destro del mouse e scegliere **Data factory** per abilitare la barra degli strumenti Data factory, se necessario. 
19. Nella **barra degli strumenti Data factory** fare clic sulla **casella di riepilogo a discesa** per visualizzare tutte le data factory nella sottoscrizione di Azure. Se viene visualizzata la finestra di dialogo **Accedi a Visual Studio**: 
	20. Immettere l'**account di posta elettronica** associato alla sottoscrizione di Azure in cui si vuole creare la data factory, immettere la **password** e fare clic su **Accedi**.
	21. Una volta completato l'accesso, si dovrebbero vedere tutte le data factory nella sottoscrizione di Azure. In questa esercitazione verrà creata una nuova data factory.       
22. Nell'elenco a discesa selezionare **DataFactoryMyFirstPipeline** e fare clic sul pulsante **Pubblica** per distribuire/pubblicare i servizi collegati, i set di dati e la pipeline.    

	![Pulsante Publish](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

23. Verrà visualizzato lo stato della pubblicazione nella finestra relativa all'**elenco attività della data factory** illustrata nella figura precedente. Verificare che la pubblicazione abbia avuto esito positivo.


## Usare Esplora Server per esaminare le entità della data factory

1. In **Visual Studio** scegliere **Esplora server** dal menu **Visualizza**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzata la finestra **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione verrà visualizzato nella finestra relativa all'**elenco attività della data factory**.

	![Esplora server](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. È possibile fare clic con il pulsante destro del mouse su una data factory e scegliere l'opzione per **esportare la data factory in un nuovo progetto** per creare un progetto Visual Studio basato su una data factory esistente.

	![Data factory di Azure](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Aggiornare gli strumenti di Data factory di Azure per Visual Studio

Per aggiornare gli strumenti di Data Factory di Azure per Visual Studio, eseguire le operazioni seguenti:

1. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**.
2. Selezionare **Aggiornamenti** nel riquadro sinistro e quindi selezionare **Visual Studio Gallery**.
3. Selezionare **Strumenti di Data factory di Azure per Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento. 

Vedere [Monitorare i set di dati e la pipeline](data-factory-monitor-manage-pipelines.md) per istruzioni su come usare il portale di anteprima di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione.
 

## Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati da un BLOB di Azure ad Azure SQL](data-factory-get-started.md).
  

<!---HONumber=July15_HO5-->