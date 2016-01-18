<properties 
	pageTitle="Spostare ed elaborare i file di log con Data factory di Azure (Azure PowerShell)" 
	description="Questa esercitazione avanzata descrive uno scenario reale e lo implementa usando il servizio Data factory di Azure e Azure PowerShell." 
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
	ms.date="10/29/2015" 
	ms.author="spelluru"/>

# Esercitazione: Spostare ed elaborare i file di log con Data factory [PowerShell]
Questo articolo fornisce una procedura dettagliata end-to-end per uno scenario canonico di elaborazione dei log con Data factory di Azure per trasformare i dati da file di log a informazioni.

## Scenario
Contoso è una società di giochi online che crea giochi per più piattaforme: console di gioco, dispositivi palmari e personal computer (PC). Ognuno di questi giochi genera grandi quantità di log. L'obiettivo di Contoso è raccogliere e analizzare i log generati da questi giochi per ottenere informazioni sull'utilizzo, identificare opportunità di upselling e cross-selling, sviluppare nuove accattivanti funzionalità e così via, per migliorare il business e offrire una migliore esperienza ai clienti.
 
In questa procedura dettagliata, si raccoglieranno log di esempio, li si elaborerà e arricchirà con dati di riferimento e si trasformeranno i dati per valutare l'efficacia di una campagna di marketing lanciata di recente da Contoso.

## Preparazione per l'esercitazione
1.	Per una panoramica di Data factory di Azure e per conoscere i concetti principali, leggere [Introduzione a Data factory di Azure][adfintroduction].
2.	Per questa esercitazione, è necessario disporre di una sottoscrizione di Azure. Per informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].
3.	È necessario scaricare e installare [Azure PowerShell][download-azure-powershell] nel computer in uso.

	Questo articolo non illustra tutti i cmlet di Data factory. Vedere [Riferimento ai cmdlet di Data factory](https://msdn.microsoft.com/library/dn820234.aspx) per la documentazione completa sui cmdlet di Data factory.
    
	Se si usa una **versione precedente alla 1.0** di Azure PowerShell, sarà necessario usare i cmdlet documentati [qui][old-cmdlet-reference]. Sarà anche necessario eseguire i comandi seguenti prima di usare i cmdlet di Data factory:

	1. Eseguire **Add-AzureAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.
	2. Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	3. Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
	
	Mantenere aperto Azure PowerShell fino alla fine dell'esercitazione. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

2. Passare alla modalità AzureResourceManager perché i cmdlet di Data factory di Azure sono disponibili in questa modalità: **Switch-AzureMode AzureResourceManager**.
 
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
	- Eseguire **Login-AzureRmAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure.
	

## Panoramica
Il flusso di lavoro end-to-end è illustrato di seguito:![Flusso end-to-end dell'esercitazione][image-data-factory-tutorial-end-to-end-flow]

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


6. [Passaggio 6: monitorare le pipeline e le sezioni di dati](#MainStep6). In questo passaggio si monitoreranno le pipeline, le tabelle e le sezioni di dati usando il portale di Azure classico.

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
   
2. Verificare che al computer locale sia consentito l'accesso al database SQL di Azure. Per abilitare l'accesso, usare il [Portale di Azure classico](http://manage.windowsazure.com) o **sp\_set\_firewall\_rule** nel database master per creare una regola firewall per l'indirizzo IP del computer. Affinché la modifica diventi effettiva potrebbero essere necessari fino a cinque minuti. Vedere [Procedura: Configurare le impostazioni del firewall (database SQL di Azure)][azure-sql-firewall].
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

1. Passare ad **Azure PowerShell** se è già aperto oppure avviare **Azure PowerShell**. Se Azure PowerShell è stato chiuso e riaperto, è necessario eseguire i comandi seguenti: 
	- Eseguire **Login-AzureRmAccount** e immettere il nome utente e la password usati per accedere al portale di Azure.  
	- Eseguire **Get-AzureSubscription** per visualizzare tutte le sottoscrizioni per l'account.
	- Eseguire **Select-AzureSubscription** per selezionare la sottoscrizione da usare. La sottoscrizione deve corrispondere a quella usata nel portale di Azure. 

2. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** (se non è stato già creato) eseguendo il comando seguente.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Per alcuni dei passaggi in questa esercitazione si presuppone l'uso del gruppo di risorse denominato ADFTutorialResourceGroup. Se si usa un gruppo di risorse diverso, sarà necessario usarlo al posto di ADFTutorialResourceGroup in questa esercitazione.
4. Eseguire il cmdlet **New-AzureRmDataFactory** per creare una data factory denominata DataFactoryMyFirstPipelinePSH.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name LogProcessingFactory –Location "West US"

	> [AZURE.IMPORTANT]È necessario specificare un nome univoco globale per la Data factory di Azure. Se viene visualizzato un errore analogo a **Il nome "LogProcessingFactory" per la data factory non è disponibile**, cambiare il nome, ad esempio, nomeutenteLogProcessingFactory. Durante l'esecuzione dei passaggi in questa esercitazione, usare questo nome anziché LogProcessingFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md).
	> 
	> Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.

 
## <a name="MainStep3"></a> Passaggio 3: creare servizi collegati

> [AZURE.NOTE]Questo articolo usa Azure PowerShell per creare servizi collegati, tabelle e pipeline. Per eseguire questa esercitazione usando il portale di Azure e in particolar modo l'Editor di Data factory, vedere l'esercitazione sull'uso dell'[Editor di Data factory][adftutorial-using-editor].

In questo passaggio verranno creati i servizi collegati seguenti: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService e HDInsightLinkedService.

16. In Azure PowerShell, passare alla sottocartella **LinkedServices** in **C:\\ADFWalkthrough** oppure nella cartella nel percorso in cui si sono estratti i file.
17. Usare il comando seguente per impostare la variabile $df sul nome dell'istanza di Data factory.

		$df = “LogProcessingFactory”
17. Aprire **StorageLinkedService.json** nell'editor preferito, immettere i valori **nome account** e **chiave account** e salvare il file.
17. Usare il cmdlet **New-AzureRmDataFactoryLinkedService** per creare un servizio collegato nel modo seguente. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\StorageLinkedService.json
	
18. Aprire **StorageLinkedService.json** nell'editor preferito, immettere i valori **nome account** e **chiave account** e salvare il file.
19. Creare il **HDInsightStorageLinkedService**.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightStorageLinkedService.json
 
19. Aprire **AzureSqlLinkedService.json** nell'editor preferito, immettere i valori **nome del server di Azure SQL**, **nome utente** e **password** e salvare il file.
19. Usare il cmdlet **New-AzureRmDataFactoryLinkedService** per creare un servizio collegato nel modo seguente. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\AzureSqlLinkedService.json
19. Aprire **HDInsightLinkedService.json** nell'editor preferito e osservare che il tipo è impostato su **HDInsightOnDemandLinkedService**.

	Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, quando si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster. Ai fini dell'esempio, viene usato un cluster su richiesta.
	
	Il cmdlet HDInsightLinkedService collega un cluster HDInsight su richiesta alla Data factory. Per usare il proprio cluster HDInsight, aggiornare la sezione Proprietà del file HDInsightLinkedService.json come illustrato di seguito (sostituire clustername, username e password con i valori appropriati):
	
		"Properties": 
		{
    		"Type": "HDInsightBYOCLinkedService",
        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
	    	"UserName": "<username>",
	    	"Password": "<password>",
	    	"LinkedServiceName": "HDInsightStorageLinkedService"
		}
		


19. Usare il cmdlet **New-AzureRmDataFactoryLinkedService** per creare un servizio collegato nel modo seguente. Iniziare con l'account di archiviazione:

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Se si usa un nome diverso per ResourceGroupName, DataFactoryName o LinkedService, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON del servizio collegato se non è possibile trovare il file.

 

## <a name="MainStep4"></a> Passaggio 4: creare tabelle 
In questo passaggio si creeranno le tabelle seguenti:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Flusso end-to-end dell'esercitazione][image-data-factory-tutorial-end-to-end-flow]
 
L'immagine precedente visualizza le pipeline nella parte centrale e le tabelle nelle parti superiore e inferiore.

Poiché il portale di Azure classico non supporta ancora la creazione di set di dati/tabelle, sarà necessario usare Azure PowerShell per creare le tabelle in questa versione.

### Per creare le tabelle

1.	In Azure PowerShell passare alla cartella **Tables** (**C:\\ADFWalkthrough\\Tables**) dal percorso in cui si sono estratti gli esempi.
2.	Usare il cmdlet **New-AzureRmDataFactoryDataset** per creare i set di dati nel modo seguente per **RawGameEventsTable**.json	


		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Se si usa un nome diverso per ResourceGroupName e DataFactoryName, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON delle tabelle se non è possibile trovare il file con il cmdlet.

3. Ripetere il passaggio precedente per creare le tabelle seguenti:
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. Nel **portale di Azure** fare clic su **Set di dati** nel pannello **DATA FACTORY** per **LogProcessingFactory** e verificare che tutti i set di dati siano visualizzati (le tabelle sono set di dati rettangolari).

	![Set di dati - Tutti][image-data-factory-tutorial-datasets-all]

	È anche possibile usare il comando seguente da Azure PowerShell:
			
		Get-AzureRmDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Passaggio 5: creare e pianificare le pipeline
In questo passaggio si creeranno le pipeline seguenti: PartitionGameLogsPipeline, EnrichGameLogsPipeline e AnalyzeMarketingCampaignPipeline.

1. In **Esplora risorse** passare alla sottocartella **Pipelines** nella cartella **C:\\ADFWalkthrough** (o nel percorso in cui si sono estratti gli esempi).
2.	Aprire **PartitionGameLogsPipeline.json** nell'editor preferito, sostituire le informazioni evidenziate con le informazioni dell'account di archiviazione dati e salvare il file.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Ripetere il passaggio per creare le pipeline seguenti:
	1. **EnrichGameLogsPipeline**.json (3 occorrenze)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 occorrenze)

	**IMPORTANTE:** verificare di aver sostituito ogni <storageaccountname> con il nome dell'account di archiviazione.
 
4.  In **Azure PowerShell** passare alla sottocartella **Pipelines** nella cartella **C:\\ADFWalkthrough** (o nel percorso in cui si sono estratti gli esempi).
5.  Usare il cmdlet **New-AzureRmDataFactoryPipeline** per creare le pipeline nel modo seguente per **PartitionGameLogspeline**.json	 
			
		New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Se si usa un nome diverso per ResourceGroupName, DataFactoryName o Pipeline, farvi riferimento nel cmdlet sopra. Specificare anche il percorso completo del file JSON delle pipeline.
6. Ripetere il passaggio precedente per creare le pipeline seguenti:
	1. **EnrichGameLogsPipeline**
			
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Usare il cmdlet **Get-AzureRmDataFactoryPipeline** per ottenere l'elenco delle pipeline.
			
		Get-AzureRmDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. Una volta create le pipeline, è possibile specificare la durata dell'elaborazione dati. Specificando il periodo attivo per una pipeline, si definisce quanto tempo durerà l'elaborazione delle sezioni di dati in base alle proprietà della disponibilità definite per ogni tabella ADF.

Per specificare il periodo attivo per la pipeline, è possibile usare il cmdlet Set-AzureRmDataFactoryPipelineActivePeriod. In questa procedura dettagliata i dati di esempio vanno da 05/01 a 05/05. Usare 2014-05-01 come StartDateTime. EndDateTime è facoltativo.
			
		Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Confermare l'impostazione del periodo attivo per la pipeline.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Ripetere i due passaggi precedenti per impostare il periodo attivo per le pipeline seguenti.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. Nel **portale di Azure** fare clic sul riquadro **Pipeline** (non sui nomi delle pipeline) nel pannello **DATA FACTORY** per **LogProcessingFactory**. Verranno visualizzate le pipeline create.

	![Tutte le pipeline][image-data-factory-tutorial-pipelines-all]

12. Nel pannello **DATA FACTORY** per **LogProcessingFactory** fare clic su **Diagramma**.

	![Collegamento al diagramma][image-data-factory-tutorial-diagram-link]

13. È possibile modificare il diagramma visualizzato. Il seguente è un diagramma modificato che mostra gli input diretti nella parte superiore e gli output in quella inferiore. Come si può notare, l'output di **PartitionGameLogsPipeline** viene passato come input a EnrichGameLogsPipeline e l'output di **EnrichGameLogsPipeline** viene passato ad **AnalyzeMarketingCampaignPipeline**. Fare doppio clic su un titolo per visualizzare i dettagli sull'elemento rappresentato dal pannello.

	![Vista Diagramma][image-data-factory-tutorial-diagram-view]

	**Congratulazioni.** Sono stati creati Data factory di Azure, i servizi collegati, le pipeline, le tabelle ed è stato avviato il flusso di lavoro.


## <a name="MainStep6"></a> Passaggio 6: monitorare le pipeline e le sezioni di dati 

1.	Se il pannello DATA FACTORY per LogProcessingFactory non è aperto, è possibile eseguire una delle operazioni seguenti:
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
 
7. Nel pannello **PIPELINE** per **PartitionGameLogsPipeline** fare clic su **Prodotto**.
8. Si vedrà l'elenco di set di dati prodotti da questa pipeline: 
9. Fare clic sulla tabella **PartitionedGameEvents** nel pannello **Set di dati prodotti**. 
10.	Verificare che lo **stato** di tutte le sezioni sia impostato su **Pronto**. 
11.	Fare clic su una delle sezioni con stato **Pronto** per visualizzare il pannello **SEZIONE DI DATI** per tale sezione.

	![Pannello SEZIONE DATI RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Se si è verificato un errore, viene visualizzato lo stato **Operazione non riuscita**. È anche possibile che entrambe le sezioni abbiano lo stato **Ready** o **PendingValidation**, a seconda della velocità di elaborazione delle sezioni.
 
	Per conoscere tutti i possibili stati delle sezioni, vedere la [Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference].

12.	Nel pannello **SEZIONE DI DATI** fare clic sull'esecuzione nell'elenco **Esecuzioni attività**. Si vedrà il pannello Esecuzione attività per tale sezione. Si vedrà il pannello **DETTAGLI ESECUZIONE ATTIVITÀ** seguente.

	![Pannello Dettagli esecuzione attività][image-data-factory-monitoring-activity-run-details]

13.	Fare clic su **Download** per scaricare i file. Questa schermata è particolarmente utile quando si risolvono i problemi dovuti a errori di elaborazione di HDInsight.
	 
	
Quando tutta la pipeline ha completato l'esecuzione, è possibile visualizzare i risultati in **MarketingCampaignEffectivenessTable** nel database SQL di Azure **MarketingCampaigns**.

**Congratulazioni.** Ora è possibile monitorare e risolvere i problemi dei flussi di lavoro. In questa esercitazione si è appreso come usare Data factory di Azure per elaborare i dati e ottenere l'analisi.

## Estendere l'esercitazione all'uso dei dati locali
Nell'ultimo passaggio dello scenario di elaborazione dei log dalla procedura dettagliata in questo articolo, l'output dell'efficacia della campagna di marketing è stato copiato in un database SQL di Azure. È anche possibile spostare questi dati in SQL Server locale per l'analisi nell'organizzazione.
 
Per copiare i dati dell'efficacia della campagna di marketing dal BLOB di Azure a SQL Server locale, è necessario creare altri servizi collegati, tabelle e pipeline locali presentati nella procedura dettagliata in questo articolo.

Eseguire la [Procedura dettagliata: copiare i dati dell'efficacia di una campagna in un database SQL Server locale][tutorial-onpremises-using-powershell] per informazioni su come creare una pipeline per la copia dei dati dell'efficacia della campagna di marketing in un database SQL Server locale.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


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

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

<!---HONumber=AcomDC_0107_2016-->