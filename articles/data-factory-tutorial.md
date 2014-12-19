<properties title="Tutorial: Move and process log files using Azure Data Factory" pageTitle="Spostare ed elaborare i file di log con Data factory di Azure" description="This advanced tutorial describes a near real-world scenario and implements the scenario using Azure Data Factory service." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Esercitazione: Spostare ed elaborare i file di log con Data factory
Questo articolo fornisce una procedura dettagliata end-to-end per uno scenario canonico di elaborazione dei log con Data factory di Azure per trasformare i dati da file di log a informazioni. 

## Scenario
Contoso è una società di giochi online che crea giochi per più piattaforme: console di gioco, dispositivi palmari e personal computer (PC). Ognuno di questi giochi genera grandi quantità di log. L'obiettivo di Contoso è raccogliere e analizzare i log generati da questi giochi per ottenere informazioni sull'utilizzo, identificare opportunità di upselling e cross-selling, sviluppare nuove accattivanti funzionalità e così via, per migliorare il business e offrire una migliore esperienza ai clienti.
 
In questa procedura dettagliata, si raccoglieranno log di esempio, li si elaborerà e arricchirà con dati di riferimento e si trasformeranno i dati per valutare l'efficacia di una campagna di marketing lanciata di recente da Contoso.

## Preparazione per l'esercitazione
1.	Leggere [Introduzione a Data factory di Azure][adfintroduction] per una panoramica di Data factory di Azure e per conoscere i concetti principali.
2.	Per questa esercitazione, è necessario disporre di una sottoscrizione di Azure. Per informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto] [azure-purchase-options], [Offerte per i membri][azure-member-offers] o [Versione di valutazione gratuita][azure-free-trial].
3.	È necessario scaricare e installare [Azure PowerShell][download-azure-powershell] nel computer. 
2.	**(consigliato)** Esaminare ed eseguire la semplice esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted] per acquisire familiarità con il portale e i cmdlet.
3.	**(consigliato)** Esaminare ed eseguire la procedura dettagliata nell'articolo [Usare Pig e Hive con Data factory di Azure][usepigandhive] sulla creazione di una pipeline per spostare i dati dall'origine dati locale a un archivio BLOB di Azure.
4.	Scaricare i file di [ADFWalkthrough][adfwalkthrough-download] nella cartella **C:\ADFWalkthrough** **mantenendo la struttura di cartelle**:
	- **Pipelines:** include i  file JSON contenenti la definizione delle pipeline.
	- **Tables:** include i  file JSON contenenti la definizione delle tabelle.
	- **LinkedServices:** include i file JSON contenenti la definizione della risorsa di archiviazione e del cluster di calcolo (HDInsight). 
	- **Scripts:** include gli script Hive e Pig usati per elaborare i dati e richiamati dalle pipeline.
	- **SampleData:** include i dati di esempio per questa procedura dettagliata
	- **OnPremises:** include i file JSON e lo script usati per dimostrare l'accesso ai dati locali
	- **AzureEnvironmentSetup.ps1:** script PowerShell per configurare l'ambiente Azure.
	- **uploadSampleDataAndScripts.ps1:** questo script carica gli script e i dati di esempio in Azure.
5. Verificare di aver creato le risorse di Azure seguenti:			
	- Account di archiviazione di Azure
	- Database SQL di Azure
	- Cluster HDInsight di Azure, versione 3.1 o successiva	
7. Una volta create le risorse di Azure, verificare di avere le informazioni necessarie per connettersi a ognuna di queste risorse.
 	- **Account di archiviazione di Azure** - Nome e chiave dell'account.  
	- **Database SQL di Azure** - Server, database, nome utente e password.
	- **Cluster HDInsight di Azure** - Nome del cluster HDInsight, nome utente, password e nome e chiave dell'account per l'archiviazione di Azure associati a questo cluster.  
 8. Avviare **Azure PowerShell** e passare a **C:\ADFWalkthrough** ed eseguire lo script di preparazione**\AzureEnvironmentSetup.ps1**.
 
		Prima di eseguire lo script, il nome sottoscrizione e l'account Microsoft devono essere associati alla sottoscrizione.

		È necessario impostare l'ambiente Azure con lo script precedente ogni volta che si avvia  Azure PowerShell. La configurazione non è persistente nelle sessioni e deve essere eseguita separatamente anche quando si usano più sessioni contemporaneamente.

		In alternativa, è possibile usare il cmdlet **Add-AzureAccount** per accedere ad Azure e selezionare una sottoscrizione (se si hanno più sottoscrizioni) usando il cmdlet **Select-AzureSubscription**.
	

## Panoramica
Il flusso di lavoro end-to-end è illustrato di seguito:
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. **PartitionGameLogsPipeline** legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB (RawGameEventsTable) e crea partizioni basate su anno, mese e giorno (PartitionedGameEventsTable).
2. **EnrichGameLogsPipeline** unisce gli eventi del gioco partizionati (tabella PartitionedGameEvents, che è un output di PartitionGameLogsPipeline) al codice dell'area geografica (RefGetoCodeDictionaryTable) e arricchisce i dati con il mapping di un indirizzo IP alla corrispondente posizione geografica (EnrichedGameEventsTable).
3. La pipeline **AnalyzeMarketingCampaignPipeline** sfrutta i dati arricchiti (EnrichedGameEventTable generato da EnrichGameLogsPipeline) e li elabora con i dati della pubblicità (RefMarketingCampaignnTable) per creare l'output finale dell'efficacia della campagna di marketing, che viene copiato nel database SQL di Azure (MarketingCampainEffectivensessSQLTable) e in una risorsa di archiviazione BLOB di Azure (MarketingCampaignEffectivenessBlobTable) per l'analisi.
    
## Procedura dettagliata: creazione, distribuzione e monitoraggio di flussi di lavoro
1. [Passaggio 1: Caricare script e dati di esempio](#MainStep1). In questo passaggio si caricheranno tutti i dati di esempio (inclusi tutti i log e i dati di riferimento) e gli script Hive/Pig che verranno eseguiti dai flussi di lavoro. Gli script eseguiti creano anche un database SQL di Azure (denominato MarketingCampaigns), tabelle, tipi definiti dall'utente e stored procedure.
2. [Passaggio 2: Creare un'istanza di Data factory di Azure](#MainStep2). In questo passaggio si creerà un'istanza di Data factory di Azure denominato LogProcessingFactory.
3. [Passaggio 3: Creare servizi collegati](#MainStep3). In questo passaggio si creeranno i servizi collegati seguenti: 
	
	- 	**StorageLinkedService**. Collega il percorso di archiviazione di Azure contenente gli eventi del gioco non elaborati, gli eventi del gioco partizionati, gli eventi del gioco arricchiti, le informazioni sull'efficacia della campagna di marketing, i dati del codice dell'area geografica di riferimento e i dati della campagna di marketing di riferimento a LogProcessingFactory.   
	- 	**AzureSqlLinkedService**. Collega un database SQL di Azure contenente le informazioni sull'efficacia della campagna di marketing. 
	- 	**HDInsightStorageLinkedService**. Collega una risorsa di archiviazione BLOB di Azure associata al cluster HDInsight a cui fa riferimento HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Collega un cluster HDInsight di Azure a LogProcessingFactory. Questo cluster viene usato per eseguire l'elaborazione pig/hive sui dati. 
 		
4. [Passaggio 4: Creare le tabelle](#MainStep4). In questo passaggio si creeranno le tabelle seguenti:  	
	
	- **RawGameEventsTable**. Questa tabella specifica la posizione dei dati degli eventi del gioco non elaborati nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Questa tabella specifica la posizione dei dati degli eventi del gioco partizionati nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Questa tabella specifica la posizione dei dati del codice dell'area geografica di riferimento nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Questa tabella specifica la posizione dei dati della campagna di marketing di riferimento nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Questa tabella specifica la posizione dei dati degli eventi del gioco arricchiti nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Questa tabella specifica la tabella SQL (MarketingCampaignEffectiveness) nel database SQL di Azure definito da AzureSqlLinkedService contenente i dati dell'efficacia della campagna di marketing. 
	- **MarketingCampaignEffectivenessBlobTable**. Questa tabella specifica la posizione dei dati dell'efficacia della campagna di marketing nella risorsa di archiviazione BLOB di Azure definita da StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Passaggio 5: Creare e pianificare le pipeline](#MainStep5). In questo passaggio si creeranno le pipeline seguenti:
	- **PartitionGameLogsPipeline**. Questa pipeline legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB (RawGameEventsTable) e crea partizioni basate su anno, mese e giorno (PartitionedGameEventsTable). 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Questa pipeline unisce gli eventi del gioco partizionati (tabella PartitionedGameEvents, che è un output di PartitionGameLogsPipeline) al codice dell'area geografica (RefGetoCodeDictionaryTable) e arricchisce i dati con il mapping di un indirizzo IP alla corrispondente posizione geografica (EnrichedGameEventsTable). 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Questa pipeline sfrutta i dati degli eventi del gioco arricchiti (EnrichedGameEventTable generato da EnrichGameLogsPipeline) e li elabora con i dati della pubblicità (RefMarketingCampaignnTable) per creare l'output finale dell'efficacia della campagna di marketing, che viene copiato nel database SQL di Azure (MarketingCampainEffectivensessSQLTable) e in una risorsa di archiviazione BLOB di Azure (MarketingCampaignEffectivenessBlobTable) per l'analisi.


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Passaggio 6: Monitorare le pipeline e le sezioni di dati](#MainStep6). In questo passaggio si monitoreranno le pipeline, le tabelle e le sezioni di dati usando il portale di Azure.

### <a name="MainStep1"></a>Passaggio 1: Caricare script e dati di esempio
In questo passaggio si caricano tutti i dati di esempio (inclusi tutti i log e i dati di riferimento) e gli script Hive/Pig richiamati dai flussi di lavoro. Gli script eseguiti creano anche un database SQL di Azure chiamato **MarketingCampaigns**, tabelle, tipi definiti dall'utente e stored procedure. 

Le tabelle, i tipi definiti dall'utente e le stored procedure vengono usati quando si spostano i risultati dell'efficacia della campagna di marketing dall'archiviazione BLOB di Azure al database SQL di Azure.

1. Aprire **uploadSampleDataAndScripts.ps1** dalla cartella **C:\ADFWalkthrough** (o dalla cartella contenente i file estratti) nell'editor preferito, sostituire le informazioni evidenziate con quelle del cluster e salvare il file.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] Questo script richiede che l'utilità sqlcmd sia installata nel computer. Se SQL Server è installato, anche l'utilità è già installata; in caso contrario, [scaricare][sqlcmd-install] e installare l'utilità. 
	> In alternativa, è possibile usare i file nella cartella: C:\ADFWalkthrough\Scripts per caricare gli script pig/hive e i file di esempio nel contenitore adfwalkthrough nell'archiviazione BLOB e creare la tabella MarketingCampaignEffectiveness nel database SQL di Azure MarketingCamapaigns.   
2. Verificare che al computer locale sia consentito l'accesso al database SQL di Azure. Per abilitare l'accesso, usare il **portale di gestione di Azure** o **sp_set_firewall_rule** nel database master per creare una regola firewall per l'indirizzo IP del computer. Affinché la modifica diventi effettiva potrebbero essere necessari fino a cinque minuti. Vedere l'articolo relativo all'[impostazione di regole firewall per SQL di Azure][azure-sql-firewall].
3. Avviare **Azure PowerShell**. 
4. Passare al percorso in cui si sono estratti gli esempi (ad esempio: **C:\ADFWalkthrough**)
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
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>Passaggio 2: Creare un'istanza di Data factory di Azure
In questo passaggio si crea un'istanza di Data factory di Azure denominata **LogProcessingFactory**.

1.	Dopo l'accesso al [portale di anteprima di Azure][azure-preview-portal], fare clic su **NUOVO** nell'angolo in basso a sinistra e fare clic su **Data factory** nel pannello **Nuovo**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Se **Data factory** non è visualizzato nel pannello **Nuovo**, scorrere verso il basso. 
	
5. Nel pannello **Nuova istanza di Data factory** immettere **LogProcessingFactory** come **Nome**.

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. Se non si è già creato un gruppo di risorse di Azure denominato **ADF**, eseguire le operazioni seguenti:
	1. Fare clic su **NOME GRUPPO DI RISORSE** e fare clic su **Crea un nuovo gruppo di risorse**.
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. Nel pannello **Crea gruppo di risorse** immettere **ADF** come nome del gruppo di risorse e fare clic su **OK**.
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. Selezionare **ADF** come **NOME GRUPPO DI RISORSE**.  
8.	Nel pannello **Nuova istanza di Data factory** si noti che **Aggiungi alla Schermata iniziale** è selezionato per impostazione predefinita. In questo modo viene aggiunto un collegamento a Data factory nella Schermata iniziale (come si vede quando si accede al portale di anteprima di Azure).

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	Nel pannello **Nuova istanza di Data factory** fare clic su **Crea** per creare l'istanza di Data factory.
10.	Una volta creata l'istanza di Data factory, si vedrà il pannello **DATA FACTORY** intitolato **LogProcessingFactory**.

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	In caso contrario, eseguire una delle operazioni seguenti:

	- Fare clic su **LogProcessingFactory** nella **Schermata iniziale** (home page)
	- Fare clic su **SFOGLIA** sulla sinistra, fare clic su **Tutto**, fare clic su **Istanze di Data factory** e fare clic sull'istanza di Data factory.
 

 
### <a name="MainStep3"></a>Passaggio 3: Creare servizi collegati

In questo passaggio si creeranno i servizi collegati seguenti: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService e HDInsightLinkedService.

1.	Nel pannello **LogProcessingFactory** fare clic sul riquadro **Servizi collegati**.

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. Nel pannello **Servizi collegati** fare clic su **+ Archivio dati** nella barra dei comandi.	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. Nel pannello **Nuovo archivio dati** immettere **StorageLinkedService** come **Nome**, fare clic su **TIPO (Impostazioni obbligatorie)** e selezionare **Account di archiviazione di Azure**.

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. Nel pannello **Nuovo archivio dati** si vedranno due campi: **Nome account** e **Chiave account**. Immettere il nome account e la chiave dell'account per l'**Account di archiviazione di Azure**.

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	È possibile ottenere il nome account e la chiave dell'account per l'account di archiviazione di Azure dal portale come mostrato di seguito:

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. Dopo aver fatto clic su **OK** nel pannello Nuovo archivio dati, si vedrà **StorageLinkedService** nell'elenco di **ARCHIVI DATI** nel pannello **Servizi collegati**. Controllare l'hub **NOTIFICHE** (sulla sinistra) per leggere eventuali messaggi.

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Ripetere i **passaggi da 2 a 5** per creare un altro servizio collegato denominato: **HDInsightStorageLinkedService**. Questa è la risorsa di archiviazione usata dal cluster HDInsight.
7. Verificare che siano visualizzati sia **StorageLinkedService** che **HDInsightStorageLinkedService** nell'elenco nel pannello Servizi collegati.
8. Nel pannello **Servizi collegati** fare clic su **Aggiungi (+) Archivio dati** nella barra dei comandi.
9. Immettere **AzureSqlLinkedService** come nome.
10. Fare clic su **TIPO (Impostazioni obbligatorie)**, selezionare **Database SQL di Azure**.
11. Si vedranno ora i seguenti campi aggiuntivi nel pannello **Nuovo archivio dati**. Immettere il **server** di database SQL, il nome del **database** il **nome utente** e la **password** e fare clic su **OK**.
	1. Immettere **MarketingCampaigns** come **database**. Questo è il database SQL di Azure creato dagli script eseguiti nel passaggio 1. È consigliabile verificare che questo database sia stato effettivamente creato dagli script (in caso di errori).
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		Per ottenere questi valori nel portale di gestione di Azure: fare clic su Visualizzare le stringhe di connessione del database SQL per il database MarketingCampaigns

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. Verificare che tutti e tre gli archivi creati siano visualizzati: **StorageLinkedService**, **HDInsightStorageLinkedService** e **AzureSqlLinkedService**.
13. È necessario creare un altro servizio collegato, questa volta un servizio di calcolo, per la precisione **Azure HDInsight cluster**. Il portale non supporta ancora la creazione di un servizio collegato di calcolo. Quindi è necessario usare Azure PowerShell per creare questo servizio collegato. 
14. Passare ad **Azure PowerShell** se è già aperto oppure avviare **Azure PowerShell**.
15. Passare alla modalità **AzureResourceManager** perché i cmdlet di Data factory di Azure sono disponibili in questa modalità.

		Switch-AzureMode AzureResourceManager

16. Passare alla sottocartella **LinkedServices** in **C:\ADFWalkthrough** oppure nella cartella nel percorso in cui si sono estratti i file.
17. Aprire **HDInsightLinkedService.json** nell'editor preferito, sostituire le informazioni evidenziate con quelle del cluster e salvare il file.

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. Usare il comando seguente per impostare la variabile $df sul nome dell'istanza di Data factory.

		$df = "LogProcessingFactory"
19. Usare il cmdlet **New-AzureDataFactoryLinkedService** per creare un servizio collegato nel modo seguente. Iniziare con l'account di archiviazione:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Se si usa un nome diverso per ResourceGroupName, DataFactoryName o LinkedService, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON del servizio collegato se non è possibile trovare il file.
20. Tutti e quattro i servizi collegati dovrebbero essere visualizzati nel pannello **Servizi collegati** come illustrato di seguito. Se il pannello Servizi collegati non è aperto, fare clic su Servizi collegati nella pagina **DATA FACTORY** per **LogProcessingFactory**. L'aggiornamento del pannello Servizi collegati potrebbe richiedere alcuni secondi.

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>Passaggio 4: Creare le tabelle 
In questo passaggio si creeranno le tabelle seguenti: 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
L'immagine precedente visualizza le pipeline nella parte centrale e le tabelle nelle parti superiore e inferiore. 

Poiché il portale di Azure non supporta ancora la creazione di set di dati/tabelle, sarà necessario usare Azure PowerShell per creare le tabelle in questa versione.

#### Per creare le tabelle

1.	In Azure PowerShell passare alla cartella **Tables** (**C:\ADFWalkthrough\Tables\**) dal percorso in cui si sono estratti gli esempi. 
2.	Usare il cmdlet **New-AzureDataFactoryTable** per creare le tabelle nel modo seguente per **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	Se si usa un nome diverso per ResourceGroupName e DataFactoryName, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON delle tabelle se non è possibile trovare il file con il cmdlet.

3. Ripetere il passaggio precedente per creare le tabelle seguenti:	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. Nel **portale di anteprima di Azure** fare clic su **Set di dati** nel pannello **DATA FACTORY** per **LogProcessingFactory** e verificare che tutti i set di dati siano visualizzati (le tabelle sono set di dati rettangolari). 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	È anche possibile usare il comando seguente da Azure PowerShell:
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>Passaggio 5: Creare e pianificare le pipeline
In questo passaggio si creeranno le pipeline seguenti: PartitionGameLogsPipeline, EnrichGameLogsPipeline e AnalyzeMarketingCampaignPipeline.

1. In **Esplora risorse** passare alla sottocartella **Pipelines** nella cartella **C:\ADFWalkthrough** (o nel percorso in cui si sono estratti gli esempi).
2.	Aprire **PartitionGameLogsPipeline.json** nell'editor preferito, sostituire le informazioni evidenziate con le informazioni dell'account di archiviazione dati e salvare il file.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Ripetere il passaggio per creare le pipeline seguenti:
	1. **EnrichGameLogsPipeline**.json (3 occorrenze)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 occorrenze)

	**IMPORTANTE:** verificare di aver sostituito ogni <storageaccountname> con il nome dell'account di archiviazione. 
 
4.  In **Azure PowerShell** passare alla sottocartella **Pipelines** nella cartella **C:\ADFWalkthrough** (o nel percorso in cui si sono estratti gli esempi).
5.  Usare il cmdlet **New-AzureDataFactoryPipeline** per creare le pipeline nel modo seguente per **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	Se si usa un nome diverso per ResourceGroupName, DataFactoryName o Pipeline, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON delle pipeline.
6. Ripetere il passaggio precedente per creare le pipeline seguenti:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. Usare il cmdlet **Get-AzureDataFactoryPipeline** per ottenere l'elenco delle pipeline.
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. Una volta create le pipeline, è possibile specificare la durata dell'elaborazione dati. Specificando il periodo attivo per una pipeline, si definisce quanto tempo durerà l'elaborazione delle sezioni di dati in base alle proprietà della disponibilità definite per ogni tabella ADF.

Per specificare il periodo attivo per la pipeline, è possibile usare il cmdlet Set-AzureDataFactoryPipelineActivePeriod. In questa procedura dettagliata i dati di esempio vanno da 05/01 a 05/05. Usare 2014-05-01 come StartDateTime. EndDateTime è facoltativo.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. Confermare l'impostazione del periodo attivo per la pipeline.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Ripetere i due passaggi precedenti per impostare il periodo attivo per le pipeline seguenti.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. Nel **portale di anteprima di Azure** fare clic sul riquadro **Pipeline** (non sui nomi delle pipeline) nel pannello **DATA FACTORY** per **LogProcessingFactory** Verranno visualizzate le pipeline create.

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. Nel pannello **DATA FACTORY** per **LogProcessingFactory** fare clic su **Diagramma**.

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. È possibile modificare il diagramma visualizzato. Il seguente è un diagramma modificato che mostra gli input diretti nella parte superiore e gli output in quella inferiore. Come si può notare, l'output di **PartitionGameLogsPipeline** viene passato come input a EnrichGameLogsPipeline e l'output di **EnrichGameLogsPipeline** viene passato ad **AnalyzeMarketingCampaignPipeline**. Fare doppio clic su un titolo per visualizzare i dettagli sull'elemento rappresentato dal pannello.

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**Congratulazioni.** Sono stati creati Data factory di Azure, i servizi collegati, le pipeline, le tabelle ed è stato avviato il flusso di lavoro. 


### <a name="MainStep6"></a>Passaggio 6: Monitorare le pipeline e le sezioni di dati 

1.	Se il pannello DATA FACTORY per LogProcessingFactory non è aperto, è possibile eseguire una delle operazioni seguenti:
	1.	Fare clic su **LogProcessingFactory** nella **Schermata iniziale**. Durante la creazione dell'istanza di Data factory, l'opzione **Aggiungi alla Schermata iniziale** è stata selezionata automaticamente.

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. Fare clic sull'hub **SFOGLIA** e fare clic su **Tutto**.
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. Nel pannello **Sfoglia** selezionare **Istanze di Data factory** e selezionare **LogProcessingFactory** nel pannello **Istanze di Data factory**.

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. È possibile monitorare l'istanza di Data factory in diversi modi. È possibile iniziare con le pipeline o con i set di dati. Si inizierà con l'esaminare le pipeline. 
3.	Fare clic su **Pipeline** nel pannello **DATA FACTORY**. 
4.	Fare clic su **PartitionGameLogsPipeline** nel pannello Pipeline. 
5.	Nel pannello **PIPELINE** per **PartitionGameLogsPipeline**, si noterà che la pipeline consuma il set di dati **RawGameEventsTable**.  Fare clic su **RawGameEventsTable**.

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. Nel pannello TABELLA per **RawGameEventsTable**, sono visualizzate tutte le sezioni. Nella cattura di schermata seguente, lo stato di tutte le sezioni è **Ready** e non ci sono sezioni problematiche. I dati sono quindi pronti per l'elaborazione.	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. Nel pannello **PIPELINE** per **PartiionGameLogsPipeline**fare clic su **Prodotto**. 
8. Si vedrà l'elenco di set di dati prodotti da questa pipeline: 
9. Fare clic sulla tabella **PartitionedGameEvents** nel pannello **Set di dati prodotti**. 
10.	Verificare che lo **stato** di tutte le sezioni sia impostato su **Ready**. 
11.	Fare clic su una delle sezioni con stato **Ready** per visualizzare il pannello **SEZIONE DI DATI** per tale sezione.

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se si è verificato un errore, viene visualizzato lo stato **Failed**.  È anche possibile che entrambe le sezioni abbiano lo stato **Ready** o **PendingValidation**, a seconda della velocità di elaborazione delle sezioni.
 
	Vedere la [guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] per conoscere tutti i possibili stati delle sezioni.

12.	Nel pannello **SEZIONE DI DATI** fare clic sull'esecuzione nell'elenco **Esecuzioni attività**. Si vedrà il pannello Esecuzione attività per tale sezione. Si vedrà il pannello **DETTAGLI ESECUZIONE ATTIVITÀ** seguente.

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	Fare clic su **Download** per scaricare i file. Questa schermata è particolarmente utile quando si risolvono i problemi dovuti a errori di elaborazione di HDInsight. 
	 
	
Quando tutta la pipeline ha completato l'esecuzione, è possibile visualizzare i risultati in **MarketingCampaignEffectivenessTable** nel database SQL di Azure **MarketingCampaigns**. 

**Congratulazioni.** Ora è possibile monitorare e risolvere i problemi dei flussi di lavoro. In questa esercitazione si è appreso come usare Data factory di Azure per elaborare i dati e ottenere l'analisi.

	 

## Procedura dettagliata: uso dei dati locali

**(consigliato)** Esaminare ed eseguire la procedura dettagliata per [consentire alla pipeline di usare dati locali][useonpremisesdatasources] nell'articolo sulla creazione di una pipeline per spostare i dati da SQL Server locale a un archivio BLOB di Azure.


In questa procedura dettagliata si imparerà a configurare l'ambiente per consentire alla pipeline di usare i dati locali.
 
Nell'ultimo passaggio dello scenario di elaborazione dei log dalla prima procedura dettagliata con flusso di lavoro Partizione -> Arricchimento -> Analisi, l'output dell'efficacia della campagna di marketing è stato copiato in un database SQL di Azure. È anche possibile spostare questi dati in SQL Server locale per l'analisi nell'organizzazione.
 
Per copiare i dati dell'efficacia della campagna di marketing dal BLOB di Azure a SQL Server locale, è necessario creare altri servizi collegati, tabelle e pipeline locali con lo stesso set di cmdlet presentati nella prima procedura dettagliata.

In questa esercitazione si eseguirà la procedura seguente: 

1. [Passaggio 1: Creare un gateway di gestione dati](#OnPremStep1). Il gateway di gestione dati  è un agente client che fornisce accesso alle origini dati locali dell'organizzazione dal cloud. Il gateway consente il trasferimento di dati tra un'istanza di SQL Server locale e gli archivi dati di Azure.	

	È necessario che nell'ambiente aziendale sia installato almeno un gateway che deve anche essere registrato con Data factory di Azure prima di aggiungere il database SQL Server locale come servizio collegato a un'istanza di Data factory di Azure.

2. [Passaggio 2: Creare un servizio collegato per SQL Server locale](#OnPremStep2). In questo passaggio prima si creano un database e una tabella nel computer SQL Server locale e quindi si crea il servizio collegato: **OnPremSqlLinkedService**.  
3. [Passaggio 3: Creare la tabella e la pipeline](#OnPremStep3). In questo passaggio si creeranno una tabella **MarketingCampaignEffectivenessOnPremSQLTable** e una pipeline **EgressDataToOnPremPipeline**. 

4. [Passaggio 4: Monitorare la pipeline e visualizzare il risultato](#OnPremStep4). In questo passaggio si monitoreranno le pipeline, le tabelle e le sezioni di dati usando il portale di Azure.


### <a name="OnPremStep1"></a>Passaggio 1: Creare un gateway di gestione dati

Il gateway di gestione dati è un agente client che fornisce accesso alle origini dati locali dell'organizzazione dal cloud. Il gateway consente il trasferimento di dati tra un'istanza di SQL Server locale e gli archivi dati di Azure.
  
È necessario che nell'ambiente aziendale sia installato almeno un gateway che deve anche essere registrato con Data factory di Azure prima di aggiungere il database SQL Server locale come servizio collegato a un'istanza di Data factory di Azure.

Se esiste già un gateway di dati che è possibile usare, saltare questo passaggio.

1.	Creare un gateway di dati logico. Nel **portale di anteprima di Azure** fare clic su **Servizi collegati** nel pannello **DATA FACTORY**.
2.	Fare clic su **Aggiungi (+) Gateway dati** nella barra dei comandi.  
3.	Nel pannello **Nuovo gateway dati** fare clic su **CREA**.
4.	Nel pannello **Crea** immettere **MyGateway** come **nome** del gateway di dati.
5.	Fare clic su **SELEZIONA UN'AREA** e modificarla, se necessario. 
6.	Fare clic su **OK** nel pannello **Crea**. 
7.	Dovrebbe essere visualizzato il pannello **Configura**. 
8.	Nel pannello **Configura** fare clic su **Installa direttamente nel computer**. Il gateway verrà scaricato, installato e configurato nel computer e registrato con il servizio. Se nel computer è già installato un gateway che si vuole collegare a questo gateway logico sul portale, usare la chiave in questo pannello per registrare nuovamente il gateway con lo strumento di gestione configurazione del gateway di gestione dati (anteprima).

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Fare clic su **OK** per chiudere il pannello **Configura** e su **OK** per chiudere il pannello **Crea**. Attendere che lo stato di **MyGateway** nel pannello **Servizi collegati** venga impostato su **RIUSCITO**. È anche possibile avviare lo strumento **Gestione configurazione del gateway di gestione dati (anteprima)** per verificare che il nome del gateway corrisponda al nome nel portale e che lo **stato** sia **Registrato**. Potrebbe essere necessario chiudere e riaprire il pannello Servizi collegati per visualizzare l'ultimo stato. L'aggiornamento della schermata con l'ultimo stato potrebbe richiedere alcuni minuti.	

### <a name="OnPremStep2"></a>Passaggio 2: Creare un servizio collegato per SQL Server locale

In questo passaggio prima si creano il database e la tabella necessari nel computer SQL Server locale e quindi si crea il servizio collegato.

#### Preparare il database e la tabella locali

Per iniziare, è necessario creare il database SQL Server, la tabella, i tipi definiti dall'utente e le stored procedure. Questi verranno usati per spostare i risultati di **MarketingCampaignEffectiveness** dal BLOB di Azure al database SQL Server.

1.	In **Esplora risorse** passare alla sottocartella **OnPremises** in **C:\ADFWalkthrough** (o il percorso in cui si sono estratti gli esempi).
2.	Aprire **prepareOnPremDatabase&Table.ps1** nell'editor preferito, sostituire le informazioni evidenziate con quelle di SQL Server e salvare il file (fornire i dettagli dell'**autenticazione SQL**). Ai fini dell'esercitazione, abilitare l'autenticazione SQL per il database. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. In **Azure PowerShell** passare alla cartella **C:\ADFWalkthrough\OnPremises**.
4.	Eseguire **prepareOnPremDatabase&Table.ps1** **(& tra virgolette o come mostrato di seguito)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### Creare il servizio collegato

1.	Nel **portale di anteprima di Azure** fare clic sul riquadro **Servizi collegati** nel pannello **DATA FACTORY** per **LogProcessingFactory**.
2.	Nel pannello **Servizi collegati** fare clic su **Aggiungi (+) Archivio dati**.
3.	Nel pannello **Nuovo archivio dati** immettere **OnPremSqlLinkedService** come **Nome**. 
4.	Fare clic su **Tipo (Impostazioni obbligatorie)** e selezionare **SQL Server**. Ora dovrebbero essere visualizzate le impostazioni **GATEWAY DATI**, **Server**, **Database** e **CREDENZIALI** nel pannello **Nuovo archivio dati**. 
5.	Fare clic su **GATEWAY DATI (Configurare le impostazioni necessarie)** e selezionare **MyGateway** creato in precedenza. 
6.	Immettere il **nome** del server di database che ospita il database **MarketingCampaigns**. 
7.	Immettere **MarketingCampaigns** come database. 
8.	Fare clic su **CREDENZIALI**. 
9.	Nel pannello **Credenziali** fare clic su **Fare clic qui per impostare le credenziali in modo sicuro**.
10.	Viene installata per la prima volta un'applicazione con un solo clic e viene aperta la finestra di dialogo **Impostazione credenziali**. 
11.	Nella finestra di dialogo **Impostazione credenziali** immettere il **Nome utente** e la **Password** e fare clic su **OK**. Attendere che la finestra di dialogo si chiuda. 
12.	Fare clic su **OK** nel pannello **Nuovo archivio dati**. 
13.	Nel pannello **Servizi collegati** verificare che **OnPremSqlLinkedService** sia presente nell'elenco e che lo **stato** del servizio collegato sia **Riuscito**.

### <a name="OnPremStep3"></a>Passaggio 3: Creare la tabella e la pipeline

#### Creare la tabella logica locale

1.	In **Azure PowerShell** passare alla cartella **C:\ADFWalkthrough\OnPremises**. 
2.	Usare il cmdlet **New-AzureDataFactoryTable** per creare le tabelle nel modo seguente per **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Creare la pipeline per copiare i dati dal BLOB di Azure a SQL Server

1.	Usare il cmdlet **New-AzureDataFactoryPipeline** per creare la pipeline nel modo seguente per **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. Usare il cmdlet **Set-AzureDataFactoryPipelineActivePeriod** per specificare il periodo attivo per **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	Premere **"Y"** per continuare.
	
### <a name="OnPremStep4"></a>Passaggio 4: Monitorare la pipeline e visualizzare il risultato

Ora è possibile usare gli stessi passaggi illustrati nel [Passaggio 6: Monitoraggio di tabelle e pipeline](#MainStep6)  per monitorare la nuova pipeline e le sezioni di dati per la nuova tabella ADF locale.
 
Quando si vede che lo stato di una sezione della tabella **MarketingCampaignEffectivenessOnPremSQLTable** viene impostato su Ready, significa che la pipeline ha completato l'esecuzione per la sezione. Per visualizzare i risultati, eseguire una query della tabella **MarketingCampaignEffectiveness** nel database **MarketingCampaigns** in SQL Server.
 
Congratulazioni. È stata completata la procedura dettagliata per usare i dati locali. 
 
## Vedere anche

Articolo | Descrizione
------ | ---------------
[Consentire alle pipeline di usare dati locali][useonpremisesdatasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure.
[Usare Pig e Hive con Data factory][usepigandhive] | Questo articolo contiene una procedura dettagliata che mostra come usare l'attività HDInsight per eseguire uno script hive/pig per elaborare i dati di input in modo da generare i dati di output. 
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Monitorare e gestire Data factory di Azure con PowerShell][monitor-manage-using-powershell] | Questo articolo descrive come monitorare Data factory di Azure con i cmdlet di Azure PowerShell. È possibile provare gli esempi dell'articolo in ADFTutorialDataFactory.
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure. È possibile provare la procedura dettagliata di questo articolo in ADFTutorialDataFactory introducendo un errore (eliminando la tabella nel database SQL di Azure). 
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via.
[Informazioni di riferimento per i cmdlet di Data factory di Azure][cmdlet-reference] | Questo contenuto di riferimento include i dettagli su tutti i cmdlet di Data factory. 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/it-it/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/it-it/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/it-it/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/it-it/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/it-it/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


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
