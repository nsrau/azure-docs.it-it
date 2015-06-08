<properties 
	pageTitle="Spostare ed elaborare i file di log con Data factory di Azure" 
	description="Questa esercitazione avanzata descrive uno scenario reale e lo implementa usando il servizio Data factory di Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Esercitazione: Spostare ed elaborare i file di log utilizzando dati Factory [PowerShell]
Questo articolo fornisce una procedura dettagliata end-to-end per uno scenario canonico di elaborazione dei log con Data factory di Azure per trasformare i dati da file di log a informazioni.

## Scenario
Contoso è una società di giochi che crea i giochi per più piattaforme: gioco console, dispositivi portatile e personal computer (computer). Ognuno di questi giochi genera grandi quantità di log. L'obiettivo di Contoso è raccogliere e analizzare i log generati da questi giochi per ottenere informazioni sull'utilizzo, identificare opportunità di upselling e cross-selling, sviluppare nuove accattivanti funzionalità e così via, per migliorare il business e offrire una migliore esperienza ai clienti.
 
In questa procedura dettagliata, si raccoglieranno log di esempio, li si elaborerà e arricchirà con dati di riferimento e si trasformeranno i dati per valutare l'efficacia di una campagna di marketing lanciata di recente da Contoso.

## Preparazione per l'esercitazione
1.	Lettura [Introduzione a Azure dati Factory][adfintroduction] per ottenere una panoramica della Factory di dati di Azure e comprensione dei concetti di livello superiore.
2.	Per questa esercitazione, è necessario disporre di una sottoscrizione di Azure. Per informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].
3.	È necessario scaricare e installare [Azure PowerShell][download-azure-powershell] nel computer. 
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
Di seguito è illustrato il flusso di lavoro end-to-end: ![Esercitazione sul flusso End-to-End][image-data-factory-tutorial-end-to-end-flow]

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

1.	Accedendo alla [portale Azure Preview][azure-preview-portal], fare clic su **nuovo** dall'angolo inferiore sinistro, quindi fare clic su **dati Factory** sul **nuovo** blade. 

	![Nuovo -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Se non viene visualizzato **dati Factory** sul **nuovo** blade, scorrere verso il basso.
	
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

> [AZURE.NOTE]In questo articolo utilizza Azure PowerShell per creare pipeline, tabelle e i servizi collegati. Vedere [esercitazione utilizzando dati Factory Editor][adftutorial-using-editor] se si desidera eseguire questa esercitazione sull'utilizzo del portale di Azure, in particolare dati Factory Editor.

In questo passaggio si creerà i seguenti servizi collegati: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService e HDInsightLinkedService.


1.	Nel **LogProcessingFactory** blade, fare clic su **servizi collegati** riquadro.

	![Sezione servizi collegati][image-data-factory-tutorial-linkedservice-tile]

2. Nel **servizi collegati** blade, fare clic su **+ archivio dati** dalla barra dei comandi.

	![Servizi collegati - Aggiungi archivio][image-data-factory-tutorial-linkedservices-add-datstore]

3. Nel **nuovo archivio dati** blade, immettere **StorageLinkedService** per il **nome**, fare clic su **tipo (impostazioni necessarie)**, e selezionare **account di archiviazione Azure**.

	![Tipo di archivio dati - archiviazione di Azure][image-data-factory-tutorial-datastoretype-azurestorage]

4. Nel **nuovo archivio dati** blade, verrà visualizzato due nuovi campi: **nome Account** e **chiave Account**. Immettere nome account e la chiave dell'account per il **Account di archiviazione Azure**.

	![Impostazioni di archiviazione di Azure][image-data-factory-tutorial-azurestorage-settings]

	È possibile ottenere il nome account e la chiave dell'account per l'account di archiviazione di Azure dal portale come mostrato di seguito:

	![Chiave di archiviazione][image-data-factory-tutorial-storage-key]
  
5. Dopo aver fatto clic **OK** sui nuovi dati archiviare blade, dovrebbe essere visualizzato **StorageLinkedService** nell'elenco di **archivi dati** sul **servizi collegati** blade. Controllare **notifiche** Hub (a sinistra) per visualizzare tutti i messaggi.

	![Servizi collegati Blade con spazio di archiviazione][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Ripetere **i passaggi da 2 a 5** per creare un altro collegate servizio denominato: **HDInsightStorageLinkedService**. Questa è la risorsa di archiviazione usata dal cluster HDInsight.
7. Verificare che entrambi **StorageLinkedService** e **HDInsightStorageLinkedService** nell'elenco nel blade servizi collegati.
8. Nel **servizi collegati** blade, fare clic su **Aggiungi archivio dati (+)** dalla barra dei comandi.
9. Immettere **AzureSqlLinkedService** per il nome.
10. Fare clic su **tipo (impostazioni necessarie)**, selezionare **Database SQL di Azure**.
11. A questo punto, è consigliabile i seguenti campi aggiuntivi nel **nuovo archivio dati** blade. Immettere il nome del Database di SQL Azure **server**, **database** nome **nome utente**, e **password**, e fare clic su **OK**.
	1. Immettere **MarketingCampaigns** per il **database**. Questo è il database di SQL Azure creato dagli script che è stato eseguito nel passaggio 1. È necessario verificare che questo database sia stato effettivamente creato dagli script (nel caso in cui si sono verificati errori).
		
 		![Impostazioni di SQL Azure][image-data-factory-tutorial-azuresql-settings]

		Per ottenere questi valori dal portale di gestione di Azure: fare clic su stringhe di connessione View SQL Database per database MarketingCampaigns

		![Stringa di connessione del Database SQL di Azure][image-data-factory-tutorial-azuresql-database-connection-string]

12. Verificare che tutti gli archivi di dati è stato creato: **StorageLinkedService**, **HDInsightStorageLinkedService**, e **AzureSqlLinkedService**.
13. È necessario creare un altro servizio collegato, ma si tratta di un servizio di calcolo, in particolare **cluster HDInsight di Azure**. Il portale non supporta ancora la creazione di un servizio collegato di calcolo. Quindi è necessario usare Azure PowerShell per creare questo servizio collegato. 
14. Passare a **Azure PowerShell** se si dispone di è già aperta (o) avviare **Azure PowerShell**. È stato chiuso e riaperto Azure PowerShell, è necessario eseguire i comandi seguenti: 
	- Eseguire **Add-AzureAccount** e immettere il nome utente e password utilizzati per accedere al portale di anteprima di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per questo account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione che si desidera utilizzare. La sottoscrizione deve corrispondere a quella usata nel portale di anteprima di Azure. 
15. Passare a **AzureResourceManager** modalità cmdlet Factory di dati di Azure sono disponibili in questa modalità.

		Switch-AzureMode AzureResourceManager

16. Individuare il **LinkedServices** sottocartella **C:\ADFWalkthrough** (o) dalla cartella dal percorso in cui si sono estratti i file.
17. Aprire **HDInsightLinkedService.json** l'editor preferito e si noti che il tipo è impostato su **HDInsightOnDemandLinkedService**.


	> [AZURE.NOTE]Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, quando si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster. Ai fini dell'esempio, viene usato un cluster su richiesta. Il HDInsightLinkedService collega un cluster HDInsight su richiesta per la factory di dati. Per utilizzare il proprio cluster HDInsight, aggiornare la sezione proprietà del file HDInsightLinkedService.json come illustrato di seguito (Sostituisci NomeCluster, nome utente e password con i valori appropriati):
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. Usare il comando seguente per impostare la variabile $df sul nome dell'istanza di Data factory.

		$df = “LogProcessingFactory”
19. Utilizzare il cmdlet **Nuovo AzureDataFactoryLinkedService** per creare un servizio collegato come indicato di seguito. Iniziare con l'account di archiviazione:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Se si usa un nome diverso per ResourceGroupName, DataFactoryName o LinkedService, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON del servizio collegato se non è possibile trovare il file.
20. Tutti i quattro servizi collegati in verrà visualizzato il **servizi collegati** blade, come illustrato di seguito. Se il pannello servizi collegato non è aperto, fare clic su servizi collegati nel **dati FACTORY** pagina per **LogProcessingFactory**. L'aggiornamento del pannello Servizi collegati potrebbe richiedere alcuni secondi.

	![Collegati servizi tutti][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Passaggio 4: Creare tabelle 
In questo passaggio si creeranno le tabelle seguenti:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Esercitazione sul flusso End-to-End][image-data-factory-tutorial-end-to-end-flow]
 
L'immagine precedente visualizza le pipeline nella parte centrale e le tabelle nelle parti superiore e inferiore.

Poiché il portale di Azure non supporta ancora la creazione di set di dati/tabelle, sarà necessario usare Azure PowerShell per creare le tabelle in questa versione.

### Per creare le tabelle

1.	In Azure PowerShell, passare al **tabelle** cartella (* * C:\ADFWalkthrough\Tables**) dalla posizione in cui sono stati estratti gli esempi. 
2.	Utilizzare il cmdlet **Nuovo AzureDataFactoryTable** per creare le tabelle come indicato di seguito per **RawGameEventsTable**. JSON	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Se si usa un nome diverso per ResourceGroupName e DataFactoryName, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON delle tabelle se non è possibile trovare il file con il cmdlet.

3. Ripetere il passaggio precedente per creare le tabelle seguenti:
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. Nel **portale Azure Preview**, fare clic su **set di dati** nel **dati FACTORY** blade per **LogProcessingFactory** e verificare che tutti i set di dati (tabelle sono set di dati rettangolare).

	![Imposta tutti i dati][image-data-factory-tutorial-datasets-all]

	È anche possibile usare il comando seguente da Azure PowerShell:
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Passaggio 5: Creare e pianificare le pipeline
In questo passaggio si creerà le pipeline seguenti: PartitionGameLogsPipeline, EnrichGameLogsPipeline e AnalyzeMarketingCampaignPipeline.

1. In **Esplora**, individuare il **pipeline** sottocartella in **C:\ADFWalkthrough** cartella (o dalla posizione in cui sono stati estratti gli esempi).
2.	Aprire **PartitionGameLogsPipeline.json** in un editor, sostituire evidenziato con l'account di archiviazione per le informazioni sull'account di archiviazione dati e salvare il file.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Ripetere il passaggio per creare le pipeline seguenti:
	1. **EnrichGameLogsPipeline**. JSON (3 occorrenze)
	2. **AnalyzeMarketingCampaignPipeline**. JSON (3 occorrenze)

	**Importante:** confermare che sono sostituiti tutti <storageaccountname> con il nome di account di archiviazione.
 
4.  In **Azure PowerShell**, individuare il **pipeline** sottocartella in **C:\ADFWalkthrough** cartella (o dalla posizione in cui sono stati estratti gli esempi).
5.  Utilizzare il cmdlet **Nuovo AzureDataFactoryPipeline** per creare la pipeline come indicato di seguito per **PartitionGameLogspeline**. JSON	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Se si usa un nome diverso per ResourceGroupName, DataFactoryName o Pipeline, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON delle pipeline.
6. Ripetere il passaggio precedente per creare le pipeline seguenti:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Utilizzare il cmdlet **Get AzureDataFactoryPipeline** per ottenere l'elenco delle pipeline.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Una volta create le pipeline, è possibile specificare la durata dell'elaborazione dati. Specificando il periodo attivo per una pipeline, si definisce quanto tempo durerà l'elaborazione delle sezioni di dati in base alle proprietà della disponibilità definite per ogni tabella ADF.

Per specificare il periodo attivo per la pipeline, è possibile usare il cmdlet Set-AzureDataFactoryPipelineActivePeriod. In questa procedura dettagliata i dati di esempio sono da 05/01 al 05/05. Utilizzare 2014-05-01 come il valore StartDateTime. EndDateTime è facoltativo.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Confermare l'impostazione del periodo attivo per la pipeline.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Ripetere i due passaggi precedenti per impostare il periodo attivo per le pipeline seguenti.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. Nel **portale Azure Preview**, fare clic su **pipeline** riquadro (non i nomi delle pipeline) nel **FACTORY dati** blade per il **LogProcessingFactory**, si noterà che la pipeline è stato creato.

	![Tutte le pipeline][image-data-factory-tutorial-pipelines-all]

12. Nel **dati FACTORY** blade per il **LogProcessingFactory**, fare clic su **diagramma**.

	![Collegamento di diagramma][image-data-factory-tutorial-diagram-link]

13. È possibile modificare il diagramma visualizzato. Il seguente è un diagramma modificato che mostra gli input diretti nella parte superiore e gli output in quella inferiore. È possibile vedere che l'output del **PartitionGameLogsPipeline** viene passato come input per l'EnrichGameLogsPipeline e l'output del **EnrichGameLogsPipeline** viene passato alla **AnalyzeMarketingCampaignPipeline**. Fare doppio clic su un titolo per visualizzare i dettagli sull'elemento rappresentato dal pannello.

	![Vista diagramma][image-data-factory-tutorial-diagram-view]

	**Congratulazioni**. Sono stati creati Data factory di Azure, i servizi collegati, le pipeline, le tabelle ed è stato avviato il flusso di lavoro.


## <a name="MainStep6"></a> Passaggio 6: Monitorare porzioni di dati e delle pipeline 

1.	Se il pannello DATA FACTORY per LogProcessingFactory non è aperto, è possibile eseguire una delle operazioni seguenti:
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
 
7. In questo punto, il **PIPELINE** blade per **PartiionGameLogsPipeline**, fare clic su **prodotte**.
8. Si vedrà l'elenco di set di dati prodotti da questa pipeline: 
9. Fare clic su **PartitionedGameEvents** tabella il **prodotto DataSet** blade. 
10.	Verificare che il **stato** di tutte le sezioni è impostato su **Pronto**. 
11.	Fare clic su una delle sezioni che è **Pronto** per visualizzare il **sezione di dati** blade per tale sezione.

	![SEZIONE di dati RawGameEventsTable blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se si è verificato un errore, si vedrà un **Failed **qui. È possibile visualizzare entrambe le sezioni con stato **Pronto**, o entrambi con stato **PendingValidation**, a seconda la velocità di elaborazione delle sezioni.
 
	Consultare la [riferimenti per sviluppatori di Azure dati Factory][developer-reference] per ottenere le informazioni di tutti gli stati possibili di sezione.

12.	Nel **sezione di dati** blade, fare clic su Esegui dal **esegue attività** elenco. Si vedrà il pannello Esecuzione attività per tale sezione. Si dovrebbe vedere il seguente **dettagli di esecuzione di attività** blade.

	![Blade Dettagli esecuzione di attività][image-data-factory-monitoring-activity-run-details]

13.	Fare clic su **Download** per scaricare i file. Questa schermata è particolarmente utile quando si risolvono i problemi dovuti a errori di elaborazione di HDInsight.
	 
	
Quando viene completata l'esecuzione di tutte le pipeline, è possibile esaminare il **MarketingCampaignEffectivenessTable** nel **MarketingCampaigns** database SQL Azure per visualizzare i risultati.

**Congratulazioni**. Ora è possibile monitorare e risolvere i problemi dei flussi di lavoro. In questa esercitazione si è appreso come usare Data factory di Azure per elaborare i dati e ottenere l'analisi.

## Estendere l'esercitazione per l'utilizzo di dati locali
Nell'ultimo passaggio del log elaborazione scenario della procedura dettagliata in questo articolo, l'output di efficacia campagna marketing è stato copiato un database SQL Azure. È anche possibile spostare questi dati in SQL Server locale per l'analisi nell'organizzazione.
 
Per copiare i dati di efficacia campagna marketing dal Blob di Azure per SQL Server locale, è necessario creare i servizi collegati locali aggiuntivi, tabella e della Pipeline introdotto nella procedura dettagliata in questo articolo.

Esercitazione il [procedura dettagliata: utilizzo di on-premise origine dati][tutorial-onpremises-using-powershell] per imparare a creare una pipeline per copiare i dati di efficacia campagna marketing in un database di SQL Server locale.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir-->