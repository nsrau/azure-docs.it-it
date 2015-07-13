<properties 
	pageTitle="Data factory - Note sulla versione | Azure" 
	description="Note sulla versione di Data factory" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Note sulla versione di Data factory di Azure

## Note sulla versione di Data factory - 10/04/2015
Gli elenchi **Sezioni aggiornate di recente** e **Sezioni non riuscite di recente** sono ora visualizzati nel pannello **TABELLA**. Questi elenchi sono ordinati in base all'ora di aggiornamento della sezione. L'ora di aggiornamento di una sezione viene modificata nelle situazioni seguenti.

-  Lo stato della sezione viene aggiornato manualmente, ad esempio usando **Set-AzureDataFactorySliceStatus** oppure facendo clic su **ESEGUI** nel pannello **SEZIONE** della sezione.
-  Lo stato della sezione cambia a causa di un'esecuzione, ad esempio un'esecuzione avviata, un'esecuzione terminata con errore, un'esecuzione terminata correttamente e così via.

Fare clic sul titolo degli elenchi oppure sui **... (puntini di sospensione)** per visualizzare un elenco più ampio delle sezioni. Fare clic su **Filtro** sulla barra degli strumenti per filtrare le sezioni.
 
È comunque possibile visualizzare le sezioni ordinate in base all'ora della sezione facendo clic sul riquadro **Sezioni dati (in base all'ora della sezione)**. Le sezioni in tali raccolte sono ordinate in base all'ora della sezione. Ad esempio, se si tratta di una pianificazione oraria, le sezioni saranno le seguenti: - 4/4/2015 17.00 In corso - 4/4/2015 16.00 Completata - 4/4/2015 15.00 Non riuscita

Se tuttavia viene rieseguita una sezione precedente, non verrà visualizzata all'inizio dell'elenco, anche se probabilmente l'utente sarà maggiormente interessato a tale sezione.

## Note sulla versione di Data factory - 31/03/2015
- Il pacchetto di installazione aggiornato per **Gateway di gestione dati** è stato inserito nell'[Area download Microsoft][adf-gateway-download].
- La copia dal **file system locale al BLOB di Azure** è ora supportata. Per altre informazioni, vedere gli argomenti seguenti.
	-  [Servizio collegato di tipo file system locale](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Proprietà OnPremisesFileSystemLocation in una tabella JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Origini e sink supportati](https://msdn.microsoft.com/library/dn894007.aspx). Visualizzare la matrice di copia aggiornata e le proprietà **FileSystemSource**. 
-  La copia dal **database Oracle locale al BLOB di Azure** è ora supportata. Per altre informazioni, vedere gli argomenti seguenti. 
	-  [Servizio collegato Oracle locale](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Proprietà OnPremisesOracleTableLocation in una tabella JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Origini e sink supportati](https://msdn.microsoft.com/library/dn894007.aspx). Visualizzare la matrice di copia aggiornata e le proprietà **OracleSource**.
-  È possibile specificare la codifica per i file di testo in un BLOB di Azure. Vedere la nuova [proprietà encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- È possibile richiamare una stored procedure con parametri aggiuntivi quando si esegue la copia in SQL Sink. Per informazioni dettagliate, vedere [Richiamare stored procedure per SQL Sink][adf-copy-advanced].   

Per altre informazioni ed esempi, vedere il post di blog [Aggiornamento di Data factory di Azure - Nuovi archivi dati](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/).

## Note sulla versione di Data factory - 27/02/2015

### Nuovi miglioramenti
- **Editor di Data factory di Azure**. L'editor di Data factory, incluso nel portale di anteprima di Azure, permette di creare, modificare e distribuire file JSON che definiscono servizi collegati, set di dati e pipeline. L'obiettivo principale dell'editor consiste nell'offrire un'interfaccia utente veloce e leggera per creare elementi di Data factory di Azure senza richiedere l'installazione di Azure PowerShell e l'uso intenso dei cmdlet di PowerShell. Per una breve panoramica e un video sull'editor di Data factory, vedere il post di blog [Editor di Data factory di Azure - Editor Web leggero][adf-editor-blog]. Per una panoramica dettagliata dell'editor, vedere l'articolo [Editor di Data factory][adf-editor].          

### Modifiche

## Note sulla versione di Data factory - 26/01/2015 ##

### Modifiche
- Il pacchetto di installazione aggiornato per **Gateway di gestione dati** è stato inserito nell'[Area download Microsoft][adf-gateway-download]. A partire da questa versione, è possibile trovare il Gateway di gestione dati più recente da utilizzare con Data factory di Azure in questo percorso di download. Il pacchetto di installazione viene usato sia per il servizio Data factory di Azure che per il servizio Power BI per Office 365. Se si usano entrambi i servizi, si noti che è necessario installare i gateway per Data factory e Power BI in computer diversi e configurarli in modo diverso, come indicato nella documentazione relativa a Data factory o Power BI.
- L'**attività di copia** supporta ora la copia di dati tra il database SQL Server locale e un database SQL di Azure. Vedere [Attività di copia][adf-copy-activity] per informazioni dettagliate e [GitHub][adf-github-samples] per esempi JSON.
- **SqlSink** supporta una nuova proprietà, **WriteBatchTimeout**. Questa proprietà offre la flessibilità necessaria per configurare il tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout dell'operazione. Per una copia ibrida, ovvero un'operazione di copia che coinvolge un'origine dati locale e un'origine dati cloud, è necessario disporre del gateway versione 1.4 o superiore per usare questa proprietà. 
- Il **servizio collegato SQL Server** supporta ora l'**Autenticazione di Windows**. 
	- Quando si crea un servizio collegato SQL Server usando il portale, è ora possibile scegliere di usare l'Autenticazione di Windows e impostare credenziali appropriate. Ciò richiede la disponibilità del gateway versione 1.4 o superiore. 
	- Quando si crea un servizio collegato SQL Server mediante Azure PowerShell, è possibile specificare le informazioni di connessione in testo normale oppure crittografarle usando il [cmdlet New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] aggiornato, quindi usare la stringa crittografata per la proprietà della stringa di connessione nel payload JSON del servizio collegato. Per informazioni dettagliate sulla definizione di un servizio collegato in JSON, vedere [Servizi collegati][adf-msdn-linked-services]. La funzionalità di crittografia non è ancora supportata dal cmdlet New-AzureDataFactoryEncryptValue. 

## Note sulla versione di Data factory - 11/12/2014 ##

### Nuovi miglioramenti

- Integrazione con Azure Machine Learning
	- Questa versione del servizio Data factory di Azure consente di integrare Data factory di Azure con Azure Machine Learning (ML) mediante **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Per informazioni dettagliate, vedere [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning][adf-azure-ml]. 
- Viene fornito lo stato della versione del gateway
	- Se è disponibile una versione più recente del gateway rispetto a quella attualmente installata, verrà visualizzato lo stato "NewVersionAvailable" nel portale di anteprima di Azure e nell'output del cmdlet Get-AzureDataFactoryGateway. È quindi possibile seguire il percorso nel portale per scaricare il nuovo file di installazione (.msi) ed eseguirlo per installare il gateway più recente. Non è necessaria alcuna configurazione aggiuntiva.

### Modifiche

- Viene rimossa la proprietà JobsContainer in HdInsightOnDemandLinkedService
	- Nella definizione JSON per un HDInsightOnDemandLinkedService non è più necessario specificare la proprietà **jobsContainer**. Se è specificata per un servizio collegato su richiesta, la proprietà viene ignorata. È possibile rimuovere la proprietà dalla definizione JSON per il servizio collegato e aggiornare la definizione del servizio collegato tramite il cmdlet New-AzureDataFactoryLinkedService.
- Parametri di configurazione facoltativi per HDInsightOnDemandLinkedService
	- Questa versione introduce il supporto per alcuni parametri di configurazione facoltativi per HDInsightOnDemandLinked (cluster di HDInsight su richiesta). Per informazioni dettagliate, vedere la pagina relativa alle [proprietà ClusterCreateParameters][on-demand-hdi-parameters].
- Viene rimosso il percorso del gateway
	- Quando si crea un gateway di Data factory di Azure tramite il portale o tramite PowerShell (New-AzureDataFactoryGateway), non è più necessario specificare il percorso per il gateway. Verrà ereditata l'area di Data factory. Analogamente, per configurare un collegato servizio di SQL Server mediante JSON, la proprietà "gatewayLocation" non è più necessaria. .NET SDK di Data factory è stato aggiornato in base a queste modifiche.
	- Se si usa una versione precedente dell'SDK e di Azure PowerShell, è necessario fornire l'impostazione del percorso.
 
     

#### Modifiche di rilievo
	
- Da CustomActivity a DotNetActivity
	- L'interfaccia **ICustomActivity** è stata rinominata in **IDotNetActivity**. Sarà necessario aggiornare i pacchetti NuGet di Data factory e cambiare ICustomActivity in IDotNetActivity nel codice sorgente per l'attività personalizzata.  
	- Il tipo di attività personalizzata nella definizione JSON per l'attività personalizzata deve essere cambiato da **CustomActivity** a **DotNetActivity**. 
	- Le classi **CustomActivity** e **CustomActivityProperties** sono state rinominate in **DotNetActivity** e **DotNetActivityProperties** con lo stesso set di proprietà.

		Se si usa una versione precedente dell'SDK e di Azure PowerShell, è possibile continuare a usare CustomActivity anziché DotNetActivity.
    
  		Per una procedura dettagliata su come creare un'attività personalizzata e usarla in una pipeline di Data factory di Azure, vedere [Usare attività personalizzate in una pipeline di Data factory di Azure][adf-custom-activities].

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=62-->