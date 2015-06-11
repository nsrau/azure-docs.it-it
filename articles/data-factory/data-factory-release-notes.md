<properties 
	pageTitle="Data factory - Note sulla versione | Azure" 
	description="Note sulla versione di dati Factory" 
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

## Note sulla versione 10/04/2015 della Factory di dati
Verrà visualizzato il **aggiornato di recente sezioni** e **Impossibile recentemente sezioni** sono elencate nella **tabella** ora blade. Questi elenchi vengono ordinati i tempi di aggiornamento della sezione. I tempi di aggiornamento di una sezione viene modificato nelle situazioni seguenti.

-  È aggiornare lo stato della sezione manualmente, ad esempio, utilizzando il **Set AzureDataFactorySliceStatus** (o) facendo clic su **eseguire** sul **sezione** blade della sezione.
-  La sezione Cambia stato a causa di un'esecuzione (ad esempio, una sequenza di avvio, un'esecuzione terminata e non è riuscita, un'esecuzione terminata e ha avuto esito positivo, ecc).

Fare clic sul titolo degli elenchi o **... (puntini di sospensione)** Per visualizzare l'elenco di intervalli di dimensioni maggiore. Fare clic su **filtro** sulla barra degli strumenti per filtrare le sezioni.
 
È comunque possibile visualizzare intervalli ordinati i tempi di sezioni facendo clic su **sezioni di dati (per ora slice)** riquadro. Le sezioni in tali raccolte sono ordinate per intervallo tempo. Ad esempio, se si tratta di una pianificazione oraria, le sezioni sarebbe: 4/4/2015 - 5 pm In corso - 4 di 4/4/2015 pm Succeeded - 3 di 4/4/2015 pm non riuscita

Tuttavia, se una sezione precedente viene rieseguita, esso potrebbe non visualizzato all'inizio di questo elenco, anche se è probabilmente l'utente è più interessato.

## Note sulla versione 31/3/2015 della Factory di dati
- Aggiornato **Gateway di gestione dati** pacchetto di installazione è stata registrata per [Microsoft Download Center][adf-gateway-download].
- Copia da **locale del file system al blob di Azure** è attualmente supportato. Vedere gli argomenti seguenti per ulteriori informazioni.
	-  [File System collegato servizio on-premise](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Proprietà OnPremisesFileSystemLocation in una tabella JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Supportate origini e sink](https://msdn.microsoft.com/library/dn894007.aspx). Visualizzare la matrice copia aggiornata e **FileSystemSource** proprietà. 
-  Copia da **database Oracle in locale per il blob di Azure** è attualmente supportato. Vedere gli argomenti seguenti per ulteriori informazioni. 
	-  [Oracle locale collegato servizio](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Proprietà OnPremisesOracleTableLocation in una tabella JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Supportate origini e sink](https://msdn.microsoft.com/library/dn894007.aspx). Visualizzare la matrice copia aggiornata e **OracleSource** proprietà.
-  È possibile specificare la codifica per i file di testo in un Blob di Azure. Vedere il nuovo [encodingName proprietà](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- È possibile richiamare una stored procedure con parametri aggiuntivi durante la copia nel Sink SQL. Vedere [richiama stored procedure per SQL Sink][adf-copy-advanced] per informazioni dettagliate.   

Vedere il post del blob: [Azure dati Factory aggiornamento - nuovi archivi dati](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) per ulteriori informazioni ed esempi.

## Note sulla versione 2/27/2015 della Factory di dati

### Nuovi miglioramenti
- **Azure dati Factory Editor**. L'Editor di Factory dei dati, che fa parte del portale di anteprima di Azure, consente di creare, modificare e distribuire file JSON che definiscono servizi collegati, set di dati e pipeline. L'obiettivo principale dell'editor è fornire una veloce e leggero-interfaccia utente (UI) per creare elementi di Factory di dati di Azure senza la necessità di installare Azure PowerShell e sfumatura di utilizzo dei cmdlet PowerShell. Vedere il [Azure dati Factory Editor - Editor di Web A Light Weight][adf-editor-blog] post di blog per una rapida panoramica e un video su dati Factory Editor. Per una panoramica dettagliata dell'editor, vedere il [dati Factory Editor][adf-editor] articolo.          

### Modifiche

## Note sulla versione di 26/1/2015 della Factory di dati ##

### Modifiche
- Aggiornato **Gateway di gestione dati** pacchetto di installazione è stata registrata per [Microsoft Download Center][adf-gateway-download]. A partire da questa versione, è possibile trovare il Gateway di gestione dati più recenti da utilizzare con Azure dati Factory in questo percorso di download. Questo pacchetto di installazione viene utilizzato Azure dati Factory e Power BI per servizi di Office 365. Se si utilizza sia i servizi, tenere presente che deve essere installato in computer diversi e configurato in modo diverso in base alle linee guida dalla documentazione del produttore di dati o Power BI gateway per la Factory di dati e Power BI.
- Il **attività Copia** ora supporta la copia di dati tra database di SQL Server locale e un database SQL Azure. Vedere [attività Copia][adf-copy-activity] per informazioni dettagliate e [GitHub][adf-github-samples] per gli esempi JSON.
- **SqlSink** supporta una nuova proprietà: **WriteBatchTimeout**. Questa proprietà fornisce la flessibilità necessaria per configurare il tempo di attesa per l'operazione di inserimento batch da completare prima del timeout dell'operazione. Per una copia di ibrido (operazione di copia che prevede un'origine dati locale e un'origine dati cloud), è necessario che il gateway della versione 1.4 o versione successiva per utilizzare questa proprietà. 
- **SQL Server collegato servizio** supporta ora **l'autenticazione di Windows**. 
	- Quando la creazione di SQL Server collegato tramite il portale del servizio, è ora possibile utilizzare l'autenticazione di Windows e impostare le credenziali appropriate. Questo richiede il gateway della versione 1.4 o versione successiva. 
	- Quando la creazione di SQL Server collegato servizio utilizzando Azure PowerShell, è possibile specificare le informazioni di connessione in testo normale o crittografare le informazioni di connessione utilizzando aggiornato [cmdlet New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] e quindi utilizzare la stringa crittografata per la proprietà di stringa di connessione nel payload JSON servizi collegati. Vedere [servizi collegati][adf-msdn-linked-services] per informazioni dettagliate sulla definizione di un servizio collegato in JSON. La funzionalità di crittografia non è ancora supportata dal cmdlet New-AzureDataFactoryEncryptValue. 

## Note sulla versione 11/12/2014 della Factory di dati ##

### Nuovi miglioramenti

- Integrazione con Azure Machine Learning
	- Questa versione del servizio Factory di dati di Azure consente di integrare la Factory di dati di Azure con Azure Machine Learning (ML) utilizzando **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Vedere [creare pipeline predittive utilizzando dati Factory e Azure Machine Learning][adf-azure-ml] per informazioni dettagliate. 
- Viene fornito lo stato della versione del gateway
	- Se è disponibile una versione più recente del gateway rispetto a quella attualmente installata, verrà visualizzato lo stato "NewVersionAvailable" nel portale di anteprima di Azure e nell'output del cmdlet Get-AzureDataFactoryGateway. È quindi possibile seguire il percorso nel portale per scaricare il nuovo file di installazione (.msi) ed eseguirlo per installare il gateway più recente. Non è necessaria alcuna configurazione aggiuntiva.

### Modifiche

- Viene rimossa la proprietà JobsContainer in HdInsightOnDemandLinkedService
	- Nella definizione di JSON per un HDInsightOnDemandLinkedService, non è necessario specificare **jobsContainer** più proprietà. Se è specificata per un servizio collegato su richiesta, la proprietà viene ignorata. È possibile rimuovere la proprietà dalla definizione JSON per il servizio collegato e aggiornare la definizione del servizio collegato tramite il cmdlet New-AzureDataFactoryLinkedService.
- Parametri di configurazione facoltativi per HDInsightOnDemandLinkedService
	- Questa versione introduce il supporto per alcuni parametri di configurazione facoltativi per HDInsightOnDemandLinked (cluster di HDInsight su richiesta). Vedere [ClusterCreateParameters proprietà][on-demand-hdi-parameters] per informazioni dettagliate.
- Viene rimosso il percorso del gateway
	- Quando si crea un gateway di Data factory di Azure tramite il portale o tramite PowerShell (New-AzureDataFactoryGateway), non è più necessario specificare il percorso per il gateway. Verrà ereditata l'area di Data factory. Analogamente, per configurare un collegato servizio di SQL Server mediante JSON, la proprietà "gatewayLocation" non è più necessaria. .NET SDK di Data factory è stato aggiornato in base a queste modifiche.
	- Se si usa una versione precedente dell'SDK e di Azure PowerShell, è necessario fornire l'impostazione del percorso.
 
     

#### Modifiche di rilievo
	
- Da CustomActivity a DotNetActivity
	- **ICustomActivity** interfaccia viene rinominato in **IDotNetActivity**. Sarà necessario aggiornare i pacchetti NuGet di Data factory e cambiare ICustomActivity in IDotNetActivity nel codice sorgente per l'attività personalizzata.  
	- Il tipo di attività personalizzata nella definizione JSON per attività personalizzata deve essere modificato da **CustomActivity** a **DotNetActivity**. 
	- Il **CustomActivity** e **CustomActivityProperties** classi sono state rinominate in **DotNetActivity** e **DotNetActivityProperties** con lo stesso set di proprietà.

		Se si usa una versione precedente dell'SDK e di Azure PowerShell, è possibile continuare a usare CustomActivity anziché DotNetActivity.
    
  		Vedere [utilizzare attività personalizzate in una pipeline di Azure dati Factory][adf-custom-activities] per una procedura dettagliata su come creare un'attività personalizzata e utilizzare in una pipeline di Factory di dati di Azure.

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

<!---HONumber=GIT-SubDir--> 