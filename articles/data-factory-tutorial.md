<properties 
	pageTitle="Spostare ed elaborare i file di log con Data factory di Azure" 
	description="In questa esercitazione avanzata viene descritto uno scenario reale near e implementa lo scenario di utilizzo di servizio Factory di dati di Azure e dati Factory Editor." 
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

# Esercitazione: Valutazione dell'efficacia di una campagna di marketing  
Contoso è una società di giochi che crea i giochi per più piattaforme: gioco console, dispositivi portatile e personal computer (computer). Questi giochi producono molti dei registri e obiettivo di Contoso consiste nel raccogliere e analizzare i registri per ottenere informazioni sulle preferenze dei clienti, dati demografici, il comportamento di utilizzo e così via per identificare le opportunità offerte speciali e cross-selling, sviluppare nuove funzionalità interessanti per promuovere la crescita aziendale e fornire un'esperienza migliore ai clienti.

In questa esercitazione si creerà pipeline dati Factory per valutare l'efficacia di una campagna di marketing Contoso ha recentemente lanciato per raccogliere i log di esempio, l'elaborazione e all'arricchimento coinvolta con dati di riferimento e trasformazione dei dati. Dispone le tre pipeline seguenti:

1.	Il **PartitionGameLogsPipeline** legge gli eventi di giochi non elaborati dall'archiviazione blob e consente di creare partizioni in base all'anno, mese e giorno.
2.	Il **EnrichGameLogsPipeline** unisce gli eventi di giochi partizionati con dati di riferimento del codice geografico e arricchire i dati eseguendo il mapping di indirizzi IP per le aree geografiche corrispondente.
3.	Il **AnalyzeMarketingCampaignPipeline** della pipeline si avvale dei dati migliorati e lo elabora con i dati di pubblicità per creare l'output finale contenente efficacia campagna marketing.

## Preparazione per l'esercitazione
1.	Lettura [Introduzione a Azure dati Factory][adfintroduction] per ottenere una panoramica della Factory di dati di Azure e comprensione dei concetti di livello superiore.
2.	Per questa esercitazione, è necessario disporre di una sottoscrizione di Azure. Per informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](http://azure.microsoft.com/pricing/purchase-options/), [Offerte per i membri](http://azure.microsoft.com/pricing/member-offers/) oppure [Versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
3.	È necessario scaricare e installare [Azure PowerShell][download-azure-powershell] nel computer. Si eseguirà i cmdlet di Factory di dati per caricare i dati di esempio e pig o hive script per l'archiviazione blob. 
2.	**(scelta consigliata)** revisione e provare l'esercitazione nel [iniziare a utilizzare Azure dati Factory][adfgetstarted] articolo per un'esercitazione semplice acquisire familiarità con i cmdlet e il portale.
3.	**(scelta consigliata)** revisione e provare la procedura dettagliata nel [utilizzare Pig e Hive con Azure dati Factory][usepigandhive] articolo per una procedura dettagliata sulla creazione di una pipeline per spostare i dati dall'origine dati locale in un archivio blob di Azure.
4.	Scaricare [ADFWalkthrough][adfwalkthrough-download] file **C:\ADFWalkthrough** cartella **mantenendo la struttura di cartelle**:
	- **Pipeline:** include file JSON contenente la definizione delle pipeline.
	- **Tabelle:** include file JSON contenente la definizione delle tabelle.
	- **LinkedServices:** include file JSON contenente la definizione di archiviazione e calcolo (HDInsight) cluster 
	- **Script:** include script Hive e Pig che vengono utilizzati per elaborare i dati e richiamato dalle pipeline
	- **SampleData:** include dati di esempio per questa procedura dettagliata
	- **Percorso:** include file JSON e script che vengono utilizzati per dimostrare l'accesso ai dati locali
	- **uploadSampleDataAndScripts.ps1:** che carica i dati di esempio e script in Azure.
5. Verificare di aver creato le risorse di Azure seguenti:			
	- Account di archiviazione di Azure
	- Database SQL di Azure
	- Azure HDInsight Cluster della versione 3.1 o versione successiva (o cluster di HDInsight di utilizzare una richiesta al servizio dati Factory creerà automaticamente)	
7. Una volta create le risorse di Azure, verificare di avere le informazioni necessarie per connettersi a ognuna di queste risorse.
 	- **Account di archiviazione azure** : nome Account e la chiave account.  
	- **Database SQL di azure** -Server, database, nome utente e password.
	- **HDInsight Cluster di azure**.-nome del cluster HDInsight, nome utente, password e nome dell'account e chiave dell'account per l'archiviazione di Azure associato al cluster. Se si desidera utilizzare un cluster HDInsight su richiesta anziché proprio cluster HDInsight è possibile ignorare questo passaggio.  
8. Avviare **Azure PowerShell** ed eseguire i comandi seguenti. Tenere aperto Azure PowerShell. Se si chiude e riapre, è necessario eseguire di nuovo questi comandi.
	- Eseguire **Add-AzureAccount** e immettere il nome utente e password utilizzati per accedere al portale di anteprima di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per questo account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione che si desidera utilizzare. La sottoscrizione deve corrispondere a quella usata nel portale di anteprima di Azure.	

## Panoramica
Il flusso di lavoro end-to-end è illustrato di seguito:

![Esercitazione sul flusso End-to-End][image-data-factory-tutorial-end-to-end-flow]

1. Il **PartitionGameLogsPipeline** legge gli eventi di giochi non elaborati da un'archiviazione blob (RawGameEventsTable) e crea partizioni in base all'anno, mese e giorno (PartitionedGameEventsTable).
2. Il **EnrichGameLogsPipeline** unisce gli eventi di giochi partizionati (PartitionedGameEvents tabella, che è l'output del PartitionGameLogsPipeline) con codice geografica (RefGetoCodeDictionaryTable) e arricchire i dati eseguendo il mapping di un indirizzo IP corrispondente posizione geografica (EnrichedGameEventsTable).
3. Il **AnalyzeMarketingCampaignPipeline** pipeline sfrutta i dati arricchiti (prodotto dal EnrichGameLogsPipeline EnrichedGameEventTable) e lo elabora pubblicità (RefMarketingCampaignnTable) per creare l'output finale di marketing efficacia della campagna, i dati viene copiato il database di SQL Azure (MarketingCampainEffectivensessSQLTable) e un'archiviazione blob di Azure (MarketingCampaignEffectivenessBlobTable) per l'analisi.
    
## Procedura dettagliata: Creazione, distribuzione e monitoraggio dei flussi di lavoro
1. [Passaggio 1: caricare dati di esempio e script](#MainStep1). In questo passaggio si caricheranno tutti i dati di esempio (inclusi tutti i log e i dati di riferimento) e gli script Hive/Pig che verranno eseguiti dai flussi di lavoro. Gli script eseguiti creano anche un database SQL di Azure (denominato MarketingCampaigns), tabelle, tipi definiti dall'utente e stored procedure.
2. [Passaggio 2: creare una factory di dati di Azure](#MainStep2). In questo passaggio si creerà un'istanza di Data factory di Azure denominato LogProcessingFactory.
3. [Passaggio 3: creare servizi collegati](#MainStep3). In questo passaggio si creeranno i servizi collegati seguenti: 
	
	- 	**StorageLinkedService**. Collega il percorso di archiviazione di Azure contenente gli eventi del gioco non elaborati, gli eventi del gioco partizionati, gli eventi del gioco arricchiti, le informazioni sull'efficacia della campagna di marketing, i dati del codice dell'area geografica di riferimento e i dati della campagna di marketing di riferimento a LogProcessingFactory.   
	- 	**AzureSqlLinkedService**. Collega un database SQL di Azure contenente le informazioni sull'efficacia della campagna di marketing. 
	- 	**HDInsightStorageLinkedService**. Collega una risorsa di archiviazione BLOB di Azure associata al cluster HDInsight a cui fa riferimento HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Collega un cluster HDInsight di Azure a LogProcessingFactory. Questo cluster viene usato per eseguire l'elaborazione pig/hive sui dati. 
 		
4. [Passaggio 4: creare tabelle](#MainStep4). In questo passaggio si creeranno le tabelle seguenti:
	
	- **RawGameEventsTable**. Questa tabella specifica la posizione dei dati degli eventi del gioco non elaborati nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Questa tabella specifica la posizione dei dati degli eventi del gioco partizionati nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Questa tabella specifica la posizione dei dati del codice dell'area geografica di riferimento nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Questa tabella specifica la posizione dei dati della campagna di marketing di riferimento nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Questa tabella specifica la posizione dei dati degli eventi del gioco arricchiti nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Questa tabella specifica la tabella SQL (MarketingCampaignEffectiveness) nel Database di SQL Azure definito da AzureSqlLinkedService che contiene i dati di efficacia campagna marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Questa tabella specifica la posizione dei dati dell'efficacia della campagna di marketing nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Passaggio 5: creare e pianificare le pipeline](#MainStep5). In questo passaggio si creeranno le pipeline seguenti:
	- **PartitionGameLogsPipeline**. Questa pipeline legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB (RawGameEventsTable) e crea partizioni basate su anno, mese e giorno (PartitionedGameEventsTable). 


		![Pipeline PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Questa pipeline unisce gli eventi del gioco partizionati (tabella PartitionedGameEvents, che è un output di PartitionGameLogsPipeline) al codice dell'area geografica (RefGetoCodeDictionaryTable) e arricchisce i dati con il mapping di un indirizzo IP alla corrispondente posizione geografica (EnrichedGameEventsTable).

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Questa pipeline sfrutta i dati degli eventi del gioco arricchiti (EnrichedGameEventTable generato da EnrichGameLogsPipeline) e li elabora con i dati della pubblicità (RefMarketingCampaignnTable) per creare l'output finale dell'efficacia della campagna di marketing, che viene copiato nel database SQL di Azure (MarketingCampainEffectivensessSQLTable) e in una risorsa di archiviazione BLOB di Azure (MarketingCampaignEffectivenessBlobTable) per l'analisi.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Passaggio 6: monitorare le sezioni di dati e la pipeline](#MainStep6). In questo passaggio si monitoreranno le pipeline, le tabelle e le sezioni di dati usando il portale di Azure.

## <a name="MainStep1"></a> Passaggio 1: Caricare dati di esempio e script
In questo passaggio si caricano tutti i dati di esempio (inclusi tutti i log e i dati di riferimento) e gli script Hive/Pig richiamati dai flussi di lavoro. Gli script è possibile eseguire anche creano un database di SQL Azure denominato **MarketingCampaigns**, tipi definiti dall'utente, tabelle e stored procedure.

Le tabelle, i tipi definiti dall'utente e le stored procedure vengono usati quando si spostano i risultati dell'efficacia della campagna di marketing dall'archiviazione BLOB di Azure al database SQL di Azure.

1. Aprire **uploadSampleDataAndScripts.ps1** da **C:\ADFWalkthrough** cartella (o la cartella che contiene i file estratti) in un editor, sostituire evidenziato con le informazioni sul cluster e salvare il file.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][download][sqlcmd-install]   
2. Verificare che al computer locale sia consentito l'accesso al database SQL di Azure. Per abilitare l'accesso, utilizzare il **portale** o **sp_set_firewall_rule** nel database master per creare una regola del firewall per l'indirizzo IP del computer. Affinché la modifica diventi effettiva potrebbero essere necessari fino a cinque minuti. Vedere [impostazione delle regole del firewall per SQL Azure][azure-sql-firewall].
4. In Azure PowerShell, passare al percorso in cui sono stati estratti gli esempi (ad esempio: **C:\ADFWalkthrough**)
5. Eseguire **uploadSampleDataAndScripts.ps1** 
6. Una volta completata l'esecuzione dello script, verrà visualizzato quanto segue:

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> Passaggio 2: Creare una factory di dati di Azure
In questo passaggio, è possibile creare una factory di dati di Azure denominata **LogProcessingFactory**.

1.	Accedendo alla [portale Azure Preview][azure-preview-portal], fare clic su **nuovo** dall'angolo inferiore sinistro, fare clic su **analisi di dati** nel **Crea** blade e fare clic su **dati Factory** sul **analisi di dati** blade. 

	![Nuovo -> DataFactory][image-data-factory-new-datafactory-menu]

5. Nel **nuova factory di dati** blade, immettere **LogProcessingFactory** per il **nome**.

	![Dati Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. Se è ancora stato creato un gruppo di risorse di Azure denominato **ADF** già, eseguire le operazioni seguenti:
	1. Fare clic su **nome gruppo di risorse**, e fare clic su **creare un nuovo gruppo di risorse**.
	
		![Blade del gruppo di risorse][image-data-factory-tutorial-resourcegroup-blade]
	2. Nel **Crea gruppo di risorse** blade, immettere **ADF** per il nome del gruppo di risorse e fare clic su **OK**.
	
		![Crea gruppo di risorse][image-data-factory-tutorial-create-resourcegroup]
7. Selezionare **ADF** per il **nome gruppo di risorse**.  
8.	Nel **nuova factory di dati** blade, si noti che **aggiungere alla schermata iniziale** selezionata per impostazione predefinita. In questo modo viene aggiunto un collegamento a Data factory nella Schermata iniziale (come si vede quando si accede al portale di anteprima di Azure).

	![Creare dati Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	Nel **nuova factory di dati** blade, fare clic su **Crea** per creare la factory di dati.
10.	Dopo aver creata la factory di dati, verrà visualizzato il **dati FACTORY** blade intitolata **LogProcessingFactory**.

	![Home page di Factory di dati][image-data-factory-tutorial-datafactory-homepage]

	
	In caso contrario, eseguire una delle operazioni seguenti:

	- Fare clic su **LogProcessingFactory** sul **Startboard** (home page di)
	- Fare clic su **Sfoglia** a sinistra, fare clic su **tutto**, fare clic su **factory dati**, scegliere la factory di dati.
 
	> [AZURE.NOTE]È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato l'errore: **dati factory "LogProcessingFactory" non è disponibile**, modificare il nome (ad esempio, yournameLogProcessingFactory). Utilizzare questo nome al posto di LogProcessingFactory durante l'esecuzione di passaggi in questa esercitazione.
 
## <a name="MainStep3"></a> Passaggio 3: Creare servizi collegati

> [AZURE.NOTE]In questo articolo utilizza il portale di Azure, in particolare l'Editor di Factory di dati per creare pipeline, tabelle e i servizi collegati. Vedere [esercitazione utilizzando Azure PowerShell][adftutorial-using-powershell] se si desidera eseguire questa esercitazione utilizzando Azure PowerShell.

In questo passaggio si creeranno i servizi collegati seguenti:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Creare StorageLinkedService e HDInsightStorageLinkedService

1.	Nel **dati FACTORY** blade, fare clic su **autore e distribuire** riquadro per avviare il **Editor** per la factory di dati.

	![Riquadro Creare e distribuire][image-author-deploy-tile]

	> [AZURE.NOTE]Vedere [dati Factory Editor][data-factory-editor] argomento per una panoramica dettagliata dell'editor dati Factory.

2.  Nel **Editor**, fare clic su **nuovo archivio dati** pulsante sulla barra degli strumenti e selezionare **di archiviazione Azure** dal menu a discesa. Verrà visualizzato il modello JSON per la creazione di un servizio di archiviazione Azure collegato nel riquadro di destra.
	
	![Editor nuovo pulsante di archivio dati][image-editor-newdatastore-button]

3. Sostituire **accountname** e **accountkey** con il nome di account e i valori di chiave di account per l'account di archiviazione di Azure.

	![Archiviazione Blob editor JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Vedere [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) per informazioni dettagliate sulle proprietà JSON.

4. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire il StorageLinkedService. Verificare che sia visualizzato il messaggio **collegato servizio creato correttamente** sulla barra del titolo.

	![Distribuire archiviazione Blob di editor][image-editor-blob-storage-deploy]

5. Ripetere i passaggi per creare un altro servizio di archiviazione Azure collegate servizio denominato: **HDInsightStorageLinkedService** per lo spazio di archiviazione associato al cluster HDInsight. Nello script JSON per il servizio collegato, modificare il valore del **nome** proprietà **HDInsightStorageLinkedService**.

### Creare AzureSqlLinkedService
1. Nel **dati Factory Editor** , fare clic su **nuovo archivio dati** pulsante sulla barra degli strumenti e selezionare **database SQL Azure** dal menu a discesa. Verrà visualizzato il modello JSON per la creazione del servizio SQL Azure collegato nel riquadro di destra.
2. Sostituire **nomeserver**, **username@servername**, e **password** con nomi di server SQL Azure, account utente e password. 3. Sostituire **databasename** con **MarketingCampaigns**. Questo è il database di SQL Azure creato dagli script che è stato eseguito nel passaggio 1. È necessario verificare che questo database sia stato effettivamente creato dagli script (nel caso in cui si sono verificati errori). 
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire il AzureSqlLinkedService.

### Creare HDInsightLinkedService
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, quando si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster. Ai fini dell'esempio, viene usato un cluster su richiesta.

#### Per usare un cluster HDInsight su richiesta
1. Fare clic su **nuovo calcolo** dalla barra dei comandi e selezionare **cluster HDInsight su richiesta** dal menu.
2. Eseguire le operazioni seguenti nello script JSON: 
	1. Per il **clusterSize** proprietà, specificare le dimensioni del cluster HDInsight.
	2. Per il **jobsContainer** proprietà, specificare il nome del contenitore predefinito in cui verranno archiviati i registri cluster. Ai fini di questa esercitazione, specificare **adfjobscontainer**.
	3. Per il **timeToLive** proprietà, specificare quanto tempo il cliente può rimanere inattivo prima che venga eliminato. 
	4. Per il **versione** proprietà, specificare la versione di HDInsight si desidera utilizzare. Se si esclude questa proprietà, viene utilizzata la versione più recente.  
	5. Per il **linkedServiceName**, specificare **HDInsightStorageLinkedService** era stata creata in Get esercitazione introduttiva. 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		Si noti che il **tipo** di servizi collegati è impostato su **HDInsightOnDemandLinkedService**.

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.
   
   
#### Per usare il proprio cluster HDInsight: 

1. Fare clic su **nuovo calcolo** dalla barra dei comandi e selezionare **cluster HDInsight** dal menu.
2. Eseguire le operazioni seguenti nello script JSON: 
	1. Per il **clusterUri** proprietà, immettere l'URL per il HDInsight. Ad esempio: https://<clustername>.azurehdinsight.net/     
	2. Per il **nome utente** proprietà, immettere il nome utente che ha accesso al cluster HDInsight.
	3. Per il **Password** proprietà, immettere la password dell'utente. 
	4. Per il **LinkedServiceName** proprietà, immettere **StorageLinkedService**. Si tratta del servizio collegato che era stata creata nell'esercitazione introduttiva Get. 

	Nore che il **tipo** di servizi collegati è impostato su **HDInsightBYOCLinkedService** (BYOC è l'acronimo di portare il proprio Cluster).

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.


## <a name="MainStep4"></a> Passaggio 4: Creare tabelle
 
In questo passaggio si creerà le tabelle di Factory di dati seguenti:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Esercitazione sul flusso End-to-End][image-data-factory-tutorial-end-to-end-flow]
 
L'immagine precedente visualizza le pipeline nella parte centrale e le tabelle nelle parti superiore e inferiore.

### Per creare le tabelle
	
1. Nel **Editor** per la Factory di dati, fare clic su **nuovo set di dati** pulsante sulla barra degli strumenti fare clic su **archiviazione Blob di Azure** dal menu a discesa. 
2. Sostituire JSON nel riquadro destro con lo script JSON dal **RawGameEventsTable.json** file dal **C:\ADFWalkthrough\Tables** cartella.
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire la tabella. Verificare che il **tabella CREATA correttamente** messaggio sulla barra del titolo dell'Editor.
4. Ripetere i passaggi da 1 a 3 e il contenuto di file seguenti: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Ripetere i passaggi da 1 a 3 con il contenuto dal file. MA selezionare **Sql Azure** dopo aver fatto clic **nuovo set di dati**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Passaggio 5: Creare e pianificare le pipeline
In questo passaggio si creeranno le pipeline seguenti:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Per creare pipeline

1. Nell'**editor di Data factory** fare clic sul pulsante **Nuova pipeline** sulla barra degli strumenti. Fare clic su **... (puntini di sospensione)** sulla barra degli strumenti se il pulsante non è visibile. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Pipeline** nella visualizzazione albero e fare clic su **Nuova pipeline**.
2. Sostituire JSON nel riquadro destro con lo script JSON dal **PartitionGameLogsPipeline.json** file dal **C:\ADFWalkthrough\Pipelines** cartella.
3. Aggiungere una **virgola (',')** alla fine della **parentesi quadra di chiusura (']')** nel file JSON e quindi aggiungere le seguenti tre righe dopo la parentesi quadra di chiusura. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]Si noti che le date di inizio e di fine vengono impostate sull'1 maggio 2014 e sul 5 maggio 2014 perché i dati di esempio in questa procedura dettagliata sono compresi tra l'1 maggio 2014 e il 5 maggio 2014.
 
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire la pipeline. Controllare che sulla barra del titolo dell'editor sia visualizzato un messaggio simile a **CREAZIONE PIPELINE COMPLETATA**.
4. Ripetere i passaggi da 1 a 3 e il contenuto di file seguenti: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Chiudere i pannelli Factory dati premendo **X** (angolo in alto a destra) per visualizzare la home page (* * dati FACTORY **blade) per la Factory di dati. 
### Vista diagramma

1. Nel **dati FACTORY** blade per il **LogProcessingFactory**, fare clic su **diagramma**. 

	![Collegamento di diagramma][image-data-factory-tutorial-diagram-link]

2. È possibile modificare il diagramma visualizzato. Il seguente è un diagramma modificato che mostra gli input diretti nella parte superiore e gli output in quella inferiore. È possibile vedere che l'output del **PartitionGameLogsPipeline** viene passato come input per l'EnrichGameLogsPipeline e l'output del **EnrichGameLogsPipeline** viene passato alla **AnalyzeMarketingCampaignPipeline**. Fare doppio clic su un titolo per visualizzare i dettagli sull'elemento rappresentato dal pannello.

	![Vista diagramma][image-data-factory-tutorial-diagram-view]

3. Fare doppio clic su **AnalyzeMarketingCampaignPipeline**, e fare clic su **Apri Pipeline**. Tutte le attività nella pipeline e DataSet di input e output per le attività dovrebbe essere visualizzato.
 
	![Aprire pipeline](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Fare clic su **Data factory** in tale breadcrumb nell'angolo superiore sinistro per tornare alla vista diagramma con tutte le pipeline.


**Congratulazioni**. Sono stati creati Data factory di Azure, i servizi collegati, le pipeline, le tabelle ed è stato avviato il flusso di lavoro.


## <a name="MainStep6"></a> Passaggio 6: Monitorare porzioni di dati e delle pipeline 

1.	Se non è il **dati FACTORY** blade per il **LogProcessingFactory** aperto, è possibile effettuare una delle operazioni seguenti:
	1.	Fare clic su **LogProcessingFactory** sul **Startboard**. Durante la creazione della factory di dati, il **aggiungere alla schermata iniziale** opzione è stata selezionata automaticamente.

		![Schermata iniziale di monitoraggio][image-data-factory-monitoring-startboard]

	2. Fare clic su **Sfoglia** hub e fare clic su **tutto**.
	 	
		![Monitoraggio Hub tutto][image-data-factory-monitoring-hub-everything]

		Nel **Sfoglia** blade, selezionare **factory dati** e selezionare **LogProcessingFactory** nel **factory dati** blade.

		![Monitoraggio Datafactories Sfoglia][image-data-factory-monitoring-browse-datafactories]
2. È possibile monitorare l'istanza di Data factory in diversi modi. È possibile iniziare con le pipeline o con i set di dati. Si inizierà con l'esaminare le pipeline. 
3.	Fare clic su **pipeline** sul **dati FACTORY** blade. 
4.	Fare clic su **PartitionGameLogsPipeline** nel blade pipeline. 
5.	Nel **PIPELINE** blade per **PartitionGameLogsPipeline**, noterete che la pipeline utilizza **RawGameEventsTable** set di dati. Fare clic su **RawGameEventsTable**.

	![Pipeline utilizzati e prodotti][image-data-factory-monitoring-pipeline-consumed-produced]

6. Nel blade tabella per **RawGameEventsTable**, noterete che tutte le sezioni. Nella schermata riportata di seguito, tutte le sezioni sono in **Pronto** stato e non esistono sezioni alcun problema. I dati sono quindi pronti per l'elaborazione.

	![Blade RawGameEventsTable tabella][image-data-factory-monitoring-raw-game-events-table]

	Entrambi **aggiornato di recente sezioni** e **Impossibile recentemente sezioni** gli elenchi vengono ordinati in base la **ora ultimo aggiornamento**. I tempi di aggiornamento di una sezione viene modificato nelle situazioni seguenti.

	-  È aggiornare lo stato della sezione manualmente, ad esempio, utilizzando il **Set AzureDataFactorySliceStatus** (o) facendo clic su **eseguire** sul **sezione** blade della sezione.
	-  La sezione Cambia stato a causa di un'esecuzione (ad esempio, una sequenza di avvio, un'esecuzione terminata e non è riuscita, un'esecuzione terminata e ha avuto esito positivo, ecc).
 
	Fare clic sul titolo degli elenchi o **... (puntini di sospensione)** Per visualizzare l'elenco di intervalli di dimensioni maggiore. Fare clic su **filtro** sulla barra degli strumenti per filtrare le sezioni.
	
	Per visualizzare le sezioni di dati ordinati in base alle ore di inizio e fine sezione invece, fare clic su **sezioni di dati (per ora slice)** riquadro.

	![Sezioni di dati dalla sezione tempo][DataSlicesBySliceTime]
 
7. In questo punto, il **PIPELINE** blade per **PartiionGameLogsPipeline**, fare clic su **prodotte**.
8. Si vedrà l'elenco di set di dati prodotti da questa pipeline: 
9. Fare clic su **PartitionedGameEvents** tabella il **prodotto DataSet** blade. 
10.	Verificare che il **stato** di tutte le sezioni è impostato su **Pronto**. 
11.	Fare clic su una delle sezioni che è **Pronto** per visualizzare il **sezione di dati** blade per tale sezione.

	![SEZIONE di dati RawGameEventsTable blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se si è verificato un errore, si vedrà un **Failed **qui. È possibile visualizzare entrambe le sezioni con stato **Pronto**, o entrambi con stato **PendingValidation**, a seconda la velocità di elaborazione delle sezioni.

	Se non è inclusa nella sezione il **Pronto** stato, è possibile visualizzare le sezioni a monte che non si è pronti e bloccano l'intervallo corrente da eseguito nel **sezioni a monte che non sono pronte** elenco.
 
	Consultare la [riferimenti per sviluppatori di Azure dati Factory][developer-reference] per ottenere le informazioni di tutti gli stati possibili di sezione.

12.	Nel **sezione di dati** blade, fare clic su Esegui dal **esegue attività** elenco. Si vedrà il pannello Esecuzione attività per tale sezione. Si dovrebbe vedere il seguente **dettagli di esecuzione di attività** blade.

	![Blade Dettagli esecuzione di attività][image-data-factory-monitoring-activity-run-details]

13.	Fare clic su **Download** per scaricare i file. Questa schermata è particolarmente utile quando si risolvono i problemi dovuti a errori di elaborazione di HDInsight.
	 
	
Quando viene completata l'esecuzione di tutte le pipeline, è possibile esaminare il **MarketingCampaignEffectivenessTable** nel **MarketingCampaigns** database SQL Azure per visualizzare i risultati.

**Congratulazioni**. Ora è possibile monitorare e risolvere i problemi dei flussi di lavoro. In questa esercitazione si è appreso come usare Data factory di Azure per elaborare i dati e ottenere l'analisi.

## Estendere l'esercitazione per l'utilizzo di dati locali
Nell'ultimo passaggio del log elaborazione scenario della procedura dettagliata in questo articolo, l'output di efficacia campagna marketing è stato copiato un database SQL Azure. È anche possibile spostare questi dati in SQL Server locale per l'analisi nell'organizzazione.
 
Per copiare i dati di efficacia campagna marketing dal Blob di Azure per SQL Server locale, è necessario creare i servizi collegati locali aggiuntivi, tabella e della Pipeline introdotto nella procedura dettagliata in questo articolo.

Esercitazione il [procedura dettagliata: utilizzo di on-premise origine dati][tutorial-onpremises] per imparare a creare una pipeline per copiare i dati di efficacia campagna marketing in un database di SQL Server locale.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->