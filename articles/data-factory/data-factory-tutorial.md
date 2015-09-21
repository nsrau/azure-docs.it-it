<properties 
	pageTitle="Spostare ed elaborare i file di log con Data factory di Azure (Portale di Azure)" 
	description="Questa esercitazione avanzata descrive uno scenario reale e lo implementa usando il servizio Data factory di Azure e Data Factory Editor nel portale di Azure." 
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
	ms.date="08/25/2015" 
	ms.author="spelluru"/>

# Esercitazione: valutazione dell'efficacia di una campagna di marketing  
Contoso è una società di giochi online che crea giochi per più piattaforme: console di gioco, dispositivi palmari e personal computer (PC). Questi giochi generano una grande quantità di log e l'obiettivo di Contoso è raccoglierli e analizzarli per ottenere informazioni dettagliate sul cliente (tra cui preferenze, dati demografici e comportamento d'utilizzo), identificare opportunità di upselling e cross-selling, sviluppare nuove accattivanti funzionalità per favorire la crescita aziendale e offrire una migliore esperienza ai clienti.

In questa esercitazione verranno create pipeline di Data factory per valutare l'efficacia di una campagna di marketing lanciata recentemente da Contoso raccogliendo i log di esempio, elaborandoli e arricchendoli con dati di riferimento e trasformando i dati. Sono previste le tre pipeline seguenti:

1.	**PartitionGameLogsPipeline** legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB e crea partizioni basate su anno, mese e giorno.
2.	**EnrichGameLogsPipeline** unisce gli eventi di giochi partizionati con dati di riferimento del codice geografico e arricchisce i dati eseguendo il mapping di indirizzi IP per le aree geografiche corrispondenti.
3.	La pipeline **AnalyzeMarketingCampaignPipeline** si avvale dei dati migliorati e li elabora con i dati pubblicitari per creare l'output finale contenente l'efficacia della campagna di marketing.

## Preparazione per l'esercitazione
1.	Per una panoramica di Data factory di Azure e per conoscere i concetti principali, leggere [Introduzione a Data factory di Azure][adfintroduction].
2.	Per questa esercitazione, è necessario disporre di una sottoscrizione di Azure. Per informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto](http://azure.microsoft.com/pricing/purchase-options/), [Offerte per i membri](http://azure.microsoft.com/pricing/member-offers/) oppure [Versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
3.	È necessario scaricare e installare [Azure PowerShell][download-azure-powershell] nel computer in uso. Si eseguiranno i cmdlet di Data factory per caricare i dati di esempio e gli script pig/hive per l'archivio BLOB. 
2.	**(consigliato)** Esaminare ed eseguire l'esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted] per acquisire familiarità con il portale e i cmdlet.
3.	**(consigliato)** Esaminare ed eseguire la procedura dettagliata nell'articolo [Usare Pig e Hive con Data factory di Azure][usepigandhive] sulla creazione di una pipeline per spostare i dati dall'origine dati locale a un archivio BLOB di Azure.
4.	Scaricare i file di [ADFWalkthrough][adfwalkthrough-download] nella cartella **C:\\ADFWalkthrough** **mantenendo la struttura di cartelle**:
	- **Pipelines:** include i file JSON contenenti la definizione delle pipeline.
	- **Tables:** include i file JSON contenenti la definizione delle tabelle.
	- **LinkedServices:** include i file JSON contenenti la definizione della risorsa di archiviazione e del cluster di calcolo (HDInsight) 
	- **Scripts:** include gli script Hive e Pig usati per elaborare i dati e richiamati dalle pipeline
	- **SampleData:** include i dati di esempio per questa procedura dettagliata
	- **OnPremises:** include i file JSON e lo script usati per dimostrare l'accesso ai dati locali
	- **uploadSampleDataAndScripts.ps1:** questo script carica gli script e i dati di esempio in Azure.
5. Verificare di aver creato le risorse di Azure seguenti:			
	- Account di archiviazione di Azure
	- Database SQL di Azure
	- Cluster HDInsight di Azure, versione 3.1 o successiva (oppure usare un cluster HDInsight su richiesta che il servizio Data factory creerà automaticamente)	
7. Una volta create le risorse di Azure, verificare di avere le informazioni necessarie per connettersi a ognuna di queste risorse.
 	- **Account di archiviazione di Azure** - Nome e chiave dell'account.  
	- **Database SQL di Azure** - Server, database, nome utente e password.
	- **Cluster HDInsight di Azure** - Nome del cluster HDInsight, nome utente, password e nome e chiave dell'account per l'archiviazione di Azure associati a questo cluster. Se si desidera usare un cluster HDInsight su richiesta, anziché il proprio cluster HDInsight, è possibile ignorare questo passaggio.  
8. Avviare **Azure PowerShell** ed eseguire i comandi seguenti. Tenere aperta la finestra di Azure PowerShell. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.
	- Eseguire **Add-AzureAccount** e immettere il nome utente e la password usati per accedere al portale di anteprima di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di anteprima di Azure.	

## Panoramica
Il flusso di lavoro end-to-end è illustrato di seguito:

![Flusso end-to-end dell'esercitazione][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB (RawGameEventsTable) e crea partizioni basate su anno, mese e giorno (PartitionedGameEventsTable).
2. **EnrichGameLogsPipeline** unisce gli eventi del gioco partizionati (tabella PartitionedGameEvents, che è un output di PartitionGameLogsPipeline) al codice dell'area geografica (RefGetoCodeDictionaryTable) e arricchisce i dati con il mapping di un indirizzo IP alla corrispondente posizione geografica (EnrichedGameEventsTable).
3. La pipeline **AnalyzeMarketingCampaignPipeline** sfrutta i dati arricchiti (EnrichedGameEventTable generato da EnrichGameLogsPipeline) e li elabora con i dati della pubblicità (RefMarketingCampaignnTable) per creare l'output finale dell'efficacia della campagna di marketing, che viene copiato nel database SQL di Azure (MarketingCampainEffectivensessSQLTable) e in una risorsa di archiviazione BLOB di Azure (MarketingCampaignEffectivenessBlobTable) per l'analisi.
    
## Procedura dettagliata: creazione, distribuzione e monitoraggio di flussi di lavoro
1. [Passaggio 1: caricare script e dati di esempio](#MainStep1). In questo passaggio si caricheranno tutti i dati di esempio (inclusi tutti i log e i dati di riferimento) e gli script Hive/Pig che verranno eseguiti dai flussi di lavoro. Gli script eseguiti creano anche un database SQL di Azure (denominato MarketingCampaigns), tabelle, tipi definiti dall'utente e stored procedure.
2. [Passaggio 2: creare un'istanza di Data factory di Azure](#MainStep2). In questo passaggio si creerà un'istanza di Data factory di Azure denominato LogProcessingFactory.
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
	- **MarketingCampaignEffectivenessSQLTable**. Questa tabella specifica la tabella SQL (MarketingCampaignEffectiveness) nel database SQL di Azure definito da AzureSqlLinkedService contenente i dati dell'efficacia della campagna di marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Questa tabella specifica la posizione dei dati dell'efficacia della campagna di marketing nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Passaggio 5: creare e pianificare le pipeline](#MainStep5). In questo passaggio si creeranno le pipeline seguenti:
	- **PartitionGameLogsPipeline**. Questa pipeline legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB (RawGameEventsTable) e crea partizioni basate su anno, mese e giorno (PartitionedGameEventsTable). 


		![Pipeline PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Questa pipeline unisce gli eventi del gioco partizionati (tabella PartitionedGameEvents, che è un output di PartitionGameLogsPipeline) al codice dell'area geografica (RefGetoCodeDictionaryTable) e arricchisce i dati con il mapping di un indirizzo IP alla corrispondente posizione geografica (EnrichedGameEventsTable).

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Questa pipeline sfrutta i dati degli eventi del gioco arricchiti (EnrichedGameEventTable generato da EnrichGameLogsPipeline) e li elabora con i dati della pubblicità (RefMarketingCampaignnTable) per creare l'output finale dell'efficacia della campagna di marketing, che viene copiato nel database SQL di Azure (MarketingCampainEffectivensessSQLTable) e in una risorsa di archiviazione BLOB di Azure (MarketingCampaignEffectivenessBlobTable) per l'analisi.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Passaggio 6: monitorare le pipeline e le sezioni di dati](#MainStep6). In questo passaggio si monitoreranno le pipeline, le tabelle e le sezioni di dati usando il portale di Azure.

## <a name="MainStep1"></a> Passaggio 1: caricare script e dati di esempio
In questo passaggio si caricano tutti i dati di esempio (inclusi tutti i log e i dati di riferimento) e gli script Hive/Pig richiamati dai flussi di lavoro. Gli script eseguiti creano anche un database SQL di Azure chiamato **MarketingCampaigns**, tabelle, tipi definiti dall'utente e stored procedure.

Le tabelle, i tipi definiti dall'utente e le stored procedure vengono usati quando si spostano i risultati dell'efficacia della campagna di marketing dall'archiviazione BLOB di Azure al database SQL di Azure.

1. Aprire **uploadSampleDataAndScripts.ps1** dalla cartella **C:\\ADFWalkthrough** (o dalla cartella contenente i file estratti) nell'editor preferito, sostituire le informazioni evidenziate con quelle del cluster e salvare il file.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	Questo script richiede che l'utilità sqlcmd sia installata nel computer. Se SQL Server è installato, anche l'utilità è già installata; in caso contrario, [scaricare][sqlcmd-install] e installare l'utilità.
	
	In alternativa, è possibile usare i file nella cartella C:\\ADFWalkthrough\\Scripts per caricare gli script pig/hive e i file di esempio nel contenitore adfwalkthrough nell'archiviazione BLOB e creare la tabella MarketingCampaignEffectiveness nel database SQL di Azure MarketingCamapaigns.
   
2. Verificare che al computer locale sia consentito l'accesso al database SQL di Azure. Per abilitare l'accesso, usare il **Portale di gestione di Azure** o **sp\_set\_firewall\_rule** nel database master per creare una regola firewall per l'indirizzo IP del computer. Affinché la modifica diventi effettiva potrebbero essere necessari fino a cinque minuti. Vedere [Procedura: Configurare le impostazioni del firewall (database SQL di Azure)][azure-sql-firewall].
4. In Azure PowerShell passare al percorso in cui si sono estratti gli esempi (ad esempio, **C:\\ADFWalkthrough**)
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

## <a name="MainStep2"></a> Passaggio 2: creare un'istanza di Data factory di Azure
In questo passaggio si crea un'istanza di Data factory di Azure denominata **LogProcessingFactory**.

1.	Dopo l'accesso al [Portale di anteprima di Azure][azure-preview-portal], fare clic su **NUOVO** nell'angolo inferiore sinistro, fare clic su **Analisi dei dati** nel pannello **Crea** e infine fare clic su **Data factory** nel pannello **Analisi dei dati**. 

	![Nuovo->DataFactory][image-data-factory-new-datafactory-menu]

5. Nel pannello **Nuova data factory** immettere **LogProcessingFactory** come **Nome**.

	![Pannello Data factory][image-data-factory-tutorial-new-datafactory-blade]

6. Se non si è già creato un gruppo di risorse di Azure denominato **ADF**, eseguire le operazioni seguenti:
	1. Fare clic su **NOME GRUPPO DI RISORSE**, quindi su **Crea un nuovo gruppo di risorse**.
	
		![Pannello Gruppo di risorse][image-data-factory-tutorial-resourcegroup-blade]
	2. Nel pannello **Crea gruppo di risorse** immettere **ADF** come nome del gruppo di risorse e fare clic su **OK**.
	
		![Crea gruppo di risorse][image-data-factory-tutorial-create-resourcegroup]
7. Selezionare **ADF** come **NOME GRUPPO DI RISORSE**.  
8.	Nel pannello **Nuova data factory** si noti che **Aggiungi alla schermata iniziale** è selezionato per impostazione predefinita. In questo modo viene aggiunto un collegamento a Data factory nella Schermata iniziale (come si vede quando si accede al portale di anteprima di Azure).

	![Pannello di creazione Data factory][image-data-factory-tutorial-create-datafactory]

9.	Nel pannello **Nuova data factory** fare clic su **Crea** per creare l'istanza di Data factory.
10.	Una volta creata l'istanza di Data factory, si vedrà il pannello **DATA FACTORY** intitolato **LogProcessingFactory**.

	![Home page di Data factory][image-data-factory-tutorial-datafactory-homepage]

	
	In caso contrario, eseguire una delle operazioni seguenti:

	- Fare clic su **LogProcessingFactory** nella **Schermata iniziale** (home page)
	- Fare clic su **SFOGLIA** sulla sinistra, fare clic su **Tutto**, fare clic su **Istanze di Data factory** e fare clic sull'istanza di Data factory.
 
	È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato un errore analogo a **Il nome "LogProcessingFactory" per la data factory non è disponibile**, cambiare il nome, ad esempio, nomeutenteLogProcessingFactory. Durante l'esecuzione dei passaggi in questa esercitazione, usare questo nome anziché LogProcessingFactory.
 
## <a name="MainStep3"></a> Passaggio 3: creare servizi collegati

> [AZURE.NOTE]Questo articolo usa il portale di Azure, in particolare l'Editor di Data factory per creare servizi collegati, tabelle, pipeline. Per eseguire questa esercitazione con Azure PowerShell, vedere l'esercitazione sull'[uso di Azure PowerShell][adftutorial-using-powershell].

In questo passaggio si creeranno i servizi collegati seguenti:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Creare StorageLinkedService e HDInsightStorageLinkedService

1.	Nel pannello **DATA FACTORY** fare clic sul riquadro **Creare e distribuire** per avviare l'**Editor** per la data factory.

	![Riquadro Creare e distribuire][image-author-deploy-tile]

	Per una panoramica dettagliata dell'editor di Data factory, vedere l'argomento [Editor di Data factory di Azure][data-factory-editor].

2.  Nell'**Editor**, fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti, quindi scegliere **Archiviazione di Azure** dal menu a discesa. Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato di archiviazione di Azure.
	
	![Pulsante Nuovo archivio dati dell'editor][image-editor-newdatastore-button]

3. Sostituire **accountname** e **accountkey** con i valori relativi a nome e chiave dell'account di archiviazione di Azure.

	![JSON dell'archivio BLOB dell'editor][image-editor-blob-storage-json]
	
	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire StorageLinkedService. Controllare che sulla barra del titolo sia visualizzato un messaggio simile a **CREAZIONE DEL SERVIZIO COLLEGATO COMPLETATA**.

	![Distribuzione dell'archivio BLOB dell'editor][image-editor-blob-storage-deploy]

5. Ripetere la procedura per creare un altro servizio collegato di archiviazione di Azure denominato **HDInsightStorageLinkedService** per lo spazio di archiviazione associato al cluster HDInsight. Nello script JSON per il servizio collegato, modificare il valore della proprietà **name** in **HDInsightStorageLinkedService**.

### Creare AzureSqlLinkedService
1. Nell'**Editor di Data factory** fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti e scegliere **Database SQL di Azure** dal menu a discesa. Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato SQL di Azure.
2. Sostituire **servername**, **e**username@servername**, e**password** con i nomi del server SQL di Azure, account utente e la password.
3. Sostituire **databasename** con **MarketingCampaigns**. Questo è il database SQL di Azure creato dagli script eseguiti nel passaggio 1. È consigliabile verificare che questo database sia stato effettivamente creato dagli script (in caso di errori). 
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire AzureSqlLinkedService.

### Creare HDInsightLinkedService
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, quando si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster. Ai fini dell'esempio, viene usato un cluster su richiesta.

#### Per usare un cluster HDInsight su richiesta
1. Fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi selezionare **Cluster HDInsight su richiesta** dal menu.
2. Nello script JSON procedere come segue: 
	1. Per la proprietà **clusterSize**, specificare le dimensioni del cluster HDInsight.
	2. Per la proprietà **jobsContainer**, specificare il nome del contenitore predefinito in cui verranno archiviati i log del cluster. Ai fini di questa esercitazione, specificare **adfjobscontainer**.
	3. Per la proprietà **timeToLive** specificare il tempo di inattività consentito per il cliente prima dell'eliminazione. 
	4. Per la proprietà **version**, specificare la versione di HDInsight da utilizzare. Se si esclude questa proprietà, verrà usata la versione più recente.  
	5. Per **linkedServiceName** specificare l'oggetto **HDInsightStorageLinkedService** creato nell'esercitazione introduttiva. 

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

		Si noti che il **tipo** di servizio collegato è impostato su **HDInsightOnDemandLinkedService**.

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.
   
   
#### Per usare il proprio cluster HDInsight: 

1. Fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi selezionare **Cluster HDInsight** dal menu.
2. Nello script JSON procedere come segue: 
	1. Per la proprietà **clusterUri**, immettere l'URL di HDInsight. Ad esempio: https://<clustername>.azurehdinsight.net/     
	2. Per la proprietà **UserName**, immettere il nome dell'utente che ha accesso al cluster HDInsight.
	3. Per la proprietà **Password**, immettere la password dell'utente. 
	4. Per la proprietà **LinkedServiceName**, immettere **StorageLinkedService**, ossia il servizio collegato creato nell'esercitazione introduttiva. 

	Si noti che il **tipo** di servizio collegato è impostato su **HDInsightBYOCLinkedService** (BYOC è l'acronimo di Bring Your Own Cluster).

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.


## <a name="MainStep4"></a> Passaggio 4: creare tabelle
 
In questo passaggio verranno create le tabelle di Data factory seguenti:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Flusso end-to-end dell'esercitazione][image-data-factory-tutorial-end-to-end-flow]
 
L'immagine precedente visualizza le pipeline nella parte centrale e le tabelle nelle parti superiore e inferiore.

### Per creare le tabelle
	
1. Nell'**Editor** per la Data factory fare clic sul pulsante **Nuovo set di dati** sulla barra degli strumenti, quindi scegliere **Archiviazione BLOB di Azure** dal menu a discesa. 
2. Sostituire JSON nel riquadro a destra con lo script JSON del file **RawGameEventsTable.json** incluso nella cartella **C:\\ADFWalkthrough\\Tables**.
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire la tabella. Controllare che sulla barra del titolo dell'editor sia visualizzato un messaggio analogo a **CREAZIONE TABELLA COMPLETATA**.
4. Ripetere i passaggi da 1 a 3 con il contenuto dei file seguenti: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Ripetere i passaggi da 1 a 3 con il contenuto del file seguente. MA selezionare **Azure Sql** dopo aver fatto clic su **Nuovo set di dati**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Passaggio 5: creare e pianificare le pipeline
In questo passaggio si creeranno le pipeline seguenti:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Per creare le pipeline

1. Nell'**editor di Data factory** fare clic sul pulsante **Nuova pipeline** sulla barra degli strumenti. Fare clic su **... (puntini di sospensione)** sulla barra degli strumenti se il pulsante non è visibile. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Pipeline** nella visualizzazione albero e fare clic su **Nuova pipeline**.
2. Sostituire JSON nel riquadro a destra con lo script JSON del file **.json** incluso nella cartella **C:\\ADFWalkthrough\\Pipelines**.
3. Aggiungere una **virgola (',')** alla fine della **parentesi quadra di chiusura (']')** nel file JSON e quindi aggiungere le seguenti tre righe dopo la parentesi quadra di chiusura. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Si noti che le date di inizio e di fine vengono impostate sull'1 maggio 2014 e sul 5 maggio 2014 perché i dati di esempio in questa procedura dettagliata sono compresi tra l'1 maggio 2014 e il 5 maggio 2014.
 
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire la pipeline. Controllare che sulla barra del titolo dell'editor sia visualizzato un messaggio simile a **CREAZIONE PIPELINE COMPLETATA**.
4. Ripetere i passaggi da 1 a 3 con il contenuto dei file seguenti: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Chiudere i pannelli di Data factory premendo **X** in alto a destra per visualizzare la home page (**pannello DATA FACTORY**) della propria Data factory.

### Vista diagramma

1. Nel pannello **DATA FACTORY** per **LogProcessingFactory** fare clic su **Diagramma**. 

	![Collegamento al diagramma][image-data-factory-tutorial-diagram-link]

2. È possibile modificare il diagramma visualizzato. Il seguente è un diagramma modificato che mostra gli input diretti nella parte superiore e gli output in quella inferiore. Come si può notare, l'output di **PartitionGameLogsPipeline** viene passato come input a EnrichGameLogsPipeline e l'output di **EnrichGameLogsPipeline** viene passato ad **AnalyzeMarketingCampaignPipeline**. Fare doppio clic su un titolo per visualizzare i dettagli sull'elemento rappresentato dal pannello.

	![Vista Diagramma][image-data-factory-tutorial-diagram-view]

3. Fare clic con il pulsante destro del mouse su **AnalyzeMarketingCampaignPipeline**, quindi fare clic su **Apri pipeline**. Dovrebbero essere visualizzate tutte le attività della pipeline, oltre ai set di dati di input e output per le attività.
 
	![Apri pipeline](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Fare clic su **Data factory** sulla barra di navigazione nell'angolo superiore sinistro per tornare alla vista Diagramma con tutte le pipeline.


**Congratulazioni**. Sono stati creati Data factory di Azure, i servizi collegati, le pipeline, le tabelle ed è stato avviato il flusso di lavoro.


## <a name="MainStep6"></a> Passaggio 6: monitorare le pipeline e le sezioni di dati 

1.	Se il pannello **DATA FACTORY** per **LogProcessingFactory** non è aperto, è possibile eseguire una delle operazioni seguenti:
	1.	Fare clic su **LogProcessingFactory** nella **Schermata iniziale**. Durante la creazione dell'istanza di Data factory, l'opzione **Aggiungi alla Schermata iniziale** è stata selezionata automaticamente.

		![Monitoraggio della schermata iniziale][image-data-factory-monitoring-startboard]

	2. Fare clic sull'hub **SFOGLIA** e fare clic su **Tutto**.
	 	
		![Monitoraggio hub Tutto][image-data-factory-monitoring-hub-everything]

		Nel pannello **Sfoglia** selezionare **Istanze di Data factory** e selezionare **LogProcessingFactory** nel pannello **Istanze di Data factory**.

		![Monitoraggio esplorazione data factory][image-data-factory-monitoring-browse-datafactories]
2. È possibile monitorare l'istanza di Data factory in diversi modi. È possibile iniziare con le pipeline o con i set di dati. Si inizierà con l'esaminare le pipeline. 
3.	Fare clic su **Pipeline** nel pannello **DATA FACTORY**. 
4.	Fare clic su **PartitionGameLogsPipeline** nel pannello Pipeline. 
5.	Nel pannello **PIPELINE** per **PartitionGameLogsPipeline**, si noterà che la pipeline consuma il set di dati **RawGameEventsTable**. Fare clic su **RawGameEventsTable**.

	![Pipeline Consumato e Prodotti][image-data-factory-monitoring-pipeline-consumed-produced]

6. Nel pannello TABELLA per **RawGameEventsTable** sono visualizzate tutte le sezioni. Nella cattura di schermata seguente lo stato di tutte le sezioni è **Pronto** e non ci sono sezioni con errori. I dati sono quindi pronti per l'elaborazione.

	![Pannello TABELLA RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]

	Gli elenchi **Sezioni aggiornate di recente** e **Sezioni non riuscite di recente** sono ordinati in base a **ORA ULTIMO AGGIORNAMENTO**. L'ora di aggiornamento di una sezione viene modificata nelle situazioni seguenti.

	-  Lo stato della sezione viene aggiornato manualmente, ad esempio usando **Set-AzureDataFactorySliceStatus** oppure facendo clic su **ESEGUI** nel pannello **SEZIONE** della sezione.
	-  Lo stato della sezione cambia a causa di un'esecuzione, ad esempio un'esecuzione avviata, un'esecuzione terminata con errore, un'esecuzione terminata correttamente e così via.
 
	Fare clic sul titolo degli elenchi oppure sui **... (puntini di sospensione)** per visualizzare un elenco più ampio delle sezioni. Fare clic su **Filtro** sulla barra degli strumenti per filtrare le sezioni.
	
	Per visualizzare invece le sezioni di dati ordinate in base alle ore di inizio/fine, fare clic sul riquadro **Sezioni dati (in base all'ora della sezione)**.

	![Sezioni dati in base all'ora della sezione][DataSlicesBySliceTime]
 
7. Nel pannello **PIPELINE** per **PartitionGameLogsPipeline** fare clic su **Prodotto**.
8. Si vedrà l'elenco di set di dati prodotti da questa pipeline: 
9. Fare clic sulla tabella **PartitionedGameEvents** nel pannello **Set di dati prodotti**. 
10.	Verificare che lo **stato** di tutte le sezioni sia impostato su **Pronto**. 
11.	Fare clic su una delle sezioni con stato **Pronto** per visualizzare il pannello **SEZIONE DI DATI** per tale sezione.

	![Pannello SEZIONE DATI RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se si è verificato un errore, viene visualizzato lo stato **Operazione non riuscita**. È anche possibile che entrambe le sezioni abbiano lo stato **Pronto** o **ConvalidaInSospeso**, a seconda della velocità di elaborazione delle sezioni.

	Se lo stato della sezione non è **Pronto**, sarà possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**.
 
	Per conoscere tutti i possibili stati delle sezioni, vedere la [Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference].

12.	Nel pannello **SEZIONE DI DATI** fare clic sull'esecuzione nell'elenco **Esecuzioni attività**. Si vedrà il pannello Esecuzione attività per tale sezione. Si vedrà il pannello **DETTAGLI ESECUZIONE ATTIVITÀ** seguente.

	![Pannello Dettagli esecuzione attività][image-data-factory-monitoring-activity-run-details]

13.	Fare clic su **Download** per scaricare i file. Questa schermata è particolarmente utile quando si risolvono i problemi dovuti a errori di elaborazione di HDInsight.
	 
	
Quando tutta la pipeline ha completato l'esecuzione, è possibile visualizzare i risultati in **MarketingCampaignEffectivenessTable** nel database SQL di Azure **MarketingCampaigns**.

**Congratulazioni**. Ora è possibile monitorare e risolvere i problemi dei flussi di lavoro. In questa esercitazione si è appreso come usare Data factory di Azure per elaborare i dati e ottenere l'analisi.

## Estendere l'esercitazione all'uso dei dati locali
Nell'ultimo passaggio dello scenario di elaborazione dei log dalla procedura dettagliata in questo articolo, l'output dell'efficacia della campagna di marketing è stato copiato in un database SQL di Azure. È anche possibile spostare questi dati in SQL Server locale per l'analisi nell'organizzazione.
 
Per copiare i dati dell'efficacia della campagna di marketing dal BLOB di Azure a SQL Server locale, è necessario creare altri servizi collegati, tabelle e pipeline locali presentati nella procedura dettagliata in questo articolo.

Eseguire la [Procedura dettagliata: copiare i dati dell'efficacia di una campagna in un database SQL Server locale][tutorial-onpremises] per informazioni su come creare una pipeline per la copia dei dati dell'efficacia della campagna di marketing in un database SQL Server locale.

## Invia commenti e suggerimenti
I commenti e i suggerimenti su questo articolo possono essere molto utili. L'invio di commenti e suggerimenti tramite [posta elettronica](mailto:adfdocfeedback@microsoft.com?subject=data-factory-tutorial.md) richiede solo alcuni minuti.

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


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

<!---HONumber=Sept15_HO2-->