<properties 
	pageTitle="Data factory - Note sulla versione | Microsoft Azure" 
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
	ms.date="10/09/2015" 
	ms.author="spelluru"/>

# Note sulla versione di Data factory di Azure
Vedere l'articolo [Data Factory - Log delle modifiche .NET SDK](data-factory-api-change-log.md) per altre informazioni sulle modifiche apportate a Data Factory .NET SDK in una versione specifica.

## Note sulla versione di Data factory - 17/07/2015
Le modifiche JSON seguenti sono state introdotte nella versione di luglio 2015 di Azure PowerShell.

## Aggiornamento ai tipi di servizi collegati, tabelle e attività
Tipo di risorsa | Nome corrente in JSON | Nuovo nome in JSON
------------- | -------------------- | ----------------
Servizio collegato (origine dati) | AzureSqlLinkedService | AzureSqlDatabase
Servizio collegato (origine dati) | AzureStorageLinkedService | AzureStorage
Servizio collegato (origine dati) | DocumentDbLinkedService | DocumentDb
Servizio collegato (origine dati) | OnPremisesFileSystemLinkedService | OnPremisesFileServer
Servizio collegato (origine dati) | OnPremisesOracleLinkedService | OnPremisesOracle
Servizio collegato (origine dati) | OnPremisesSqlLinkedService | OnPremisesSqlServer
Servizio collegato (origine dati) | OnPremisesMySqlLinkedService | OnPremisesMySql
Servizio collegato (origine dati) | OnPremisesDb2LinkedService | OnPremisesDb2
Servizio collegato (origine dati) | OnPremisesTeradataLinkedService | OnPremisesTeradata
Servizio collegato (origine dati) | OnPremisesSybaseLinkedService | OnPremisesSybase
Servizio collegato (origine dati) | OnPremisesPostgreSqlLinkedService | OnPremisesPostgreSql
Servizio collegato (calcolo) | AzureMlLinkedService | AzureML
Servizio collegato (calcolo) | HDInsightBYOCLinkedService | HDInsight
Servizio collegato (calcolo) | HDInsightOnDemandLinkedService | HDInsightOnDemand
Servizio collegato (calcolo) | AzureBatchLinkedService | AzureBatch
Set di dati | AzureBlobLocation | AzureBlob
Set di dati | AzureTableLocation | AzureTable
Set di dati | AzureSqlTableLocation | AzureSqlTable
Set di dati | DocumentDbCollectionLocation | DocumentDbCollection 
Set di dati | OnPremisesFileSystemLocation | FileShare
Set di dati | OnPremisesOracleTableLocation | OracleTable
Set di dati | OnPremisesSqlServerTableLocation | SqlServerTable
Set di dati | RelationTableLocation | RelationalTable
Attività | CopyActivity | Copia
Attività | HDInsightActivity (trasformazione Hive) | HDInsightHive
Attività | HDInsightActivity (trasformazione Pig) | HDInsightPig
Attività | HDInsightActivity (trasformazione MapReduce) | HDInsightMapReduce
Attività | HDInsightActivity (streaming) | HDInsightHadoopStreaming
Attività | AzureMLBatchScoringActivity | AzureMLBatchScoring
Attività | StoredProcedureActivity | SqlServerStoredProcedure

## Nuovo elemento typeProperties
Il nuovo elemento **typeProperties** contiene proprietà specifiche del tipo per un servizio collegato/tabella/attività.

### JSON servizio collegato precedente
	{
	    "name": "StorageLinkedService",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

### Nuovo JSON servizio collegato
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

Tenere presente quanto segue:

- La proprietà **type** è stata spostata di un livello verso l'alto e impostata su **AzureStorage** (passando da **AzureStorageLinkedService** ad **AzureStorage**) 
- Nuovo elemento **typeProperties** contenente le proprietà supportate dal servizio collegato di archiviazione di Azure (in questo esempio, **connectionString**).  

### JSON set di dati precedente
	{
	    "name": "EmpTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureTableLocation",
	            "tableName": "myazuretable",
	            "linkedServiceName": "MyLinkedService"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

### Nuovo JSON set di dati

	{
	    "name": "EmpTable",
	    "properties": {
	        "type": "AzureTable",
	        "linkedServiceName": "MyLinkedServiceName",
	        "typeProperties": {
	            "tableName": "myazuretable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Tenere presente quanto segue:

- La proprietà **type** è stata spostata di un livello verso l'alto e il nome tipo **AzureTableLocation** è stato sostituito da **AzureTable**.
- L'elemento **linkedServiceName** è stato spostato di un livello verso l'alto. 
- L'elemento **location** è stato rimosso e le proprietà specifiche del tipo, ad esempio **tableName**, specificate nella sezione **location**, vengono specificate nella nuova sezione **typeProperties**.  

### JSON attività precedente

	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "transformation":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}   

### Nuovo JSON attività
	
	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}

Tenere presente quanto segue:

- L'elemento **transformation** è stato sostituito con il nuovo elemento **typeProperties**.

## L'elemento waitOnExternal è stato rimosso
L'elemento **waitOnExternal** è stato sostituito con le nuove proprietà **external** ed **externalData**.

### JSON precedente
	{
	    "name": "EmpTableFromBlob",
	    "properties":
	    {
	        "location": 
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "adftutorial/",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	            "linkedServiceName": "StorageLinkedService"
	        },
	        "availability": 
	        {
	            "frequency": "hour",
	            "interval": 1,
                "waitOnExternal": 
				{
			        "retryInterval": "00:01:00",
			        "retryTimeout": "00:10:00",
			        "maximumRetry": "3"			
				}
	        }
	    }
	} 

### Nuovo JSON
	{
	  "name": "EmpTableFromBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": "3"
	      }
	    }
	  }
	}

Tenere presente quanto segue:

- La proprietà **waitOnExternal** è stata rimossa dalla sezione **availability** 
- Una nuova proprietà **external** è stata aggiunta un livello più in alto e impostata su **true** per una tabella esterna. 
- Le proprietà dell'elemento **waitOnExternal**, ad esempio **retryInterval**, sono state aggiunte alla nuova sezione **externalData** nell'elemento **Policy**.
- L'elemento **externalData** è facoltativo. 
- Quando si usa l'elemento **externalData**, è necessario che la proprietà **external** sia impostata su **true**. 
 

## Nuova proprietà copyBehavior per BlobSink
**BlobSink** supporta la nuova proprietà denominata **copyBehavior**. Questa proprietà definisce il comportamento di copia quando l'origine è **BlobSource** o **FileSystem**. Sono disponibili tre possibili valori per la proprietà **copyBehavior**.

**PreserveHierarchy**: conserva la gerarchia dei file nella cartella di destinazione, ad esempio, il percorso relativo del file di origine alla cartella di origine è uguale al percorso relativo del file di destinazione alla cartella di destinazione.


**FlattenHierarchy**: tutti i file della cartella di origine saranno nel primo livello della cartella di destinazione. Il nome dei file di destinazione verrà generato automaticamente.


**MergeFiles**: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente.
 
## Nuova proprietà getDebugInfo per tutte le attività di HDInsight
Le attività di HDInsight (Hive, Pig, MapReduce, Hadoop Streaming) supportano la nuova proprietà **getDebugInfo**. La proprietà **getDebugInfo** è un elemento facoltativo. Quando viene impostata su **Failure**, i log vengono scaricati solo in caso di errore di esecuzione. Quando viene impostata su **All**, i log vengono sempre scaricati indipendentemente dallo stato dell'esecuzione. Quando viene impostata su **None**, non viene scaricato alcun log.

  
     

## Note sulla versione di Data factory - 10/04/2015
Gli elenchi **Sezioni aggiornate di recente** e **Sezioni non riuscite di recente** sono ora visualizzati nel pannello **TABELLA**. Questi elenchi sono ordinati in base all'ora di aggiornamento della sezione. L'ora di aggiornamento di una sezione viene modificata nelle situazioni seguenti.

-  Lo stato della sezione viene aggiornato manualmente, ad esempio usando **Set-AzureRmDataFactorySliceStatus** oppure facendo clic su **ESEGUI** nel pannello **SEZIONE** della sezione.
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
- È possibile richiamare una stored procedure con parametri aggiuntivi quando si esegue la copia in SQL Sink.    

Per altre informazioni ed esempi, vedere il post di blog [Aggiornamento di Data factory di Azure - Nuovi archivi dati](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/).

## Note sulla versione di Data factory - 27/02/2015

### Nuovi miglioramenti
- **Editor di Data factory di Azure**. L'editor di Data factory, incluso nel portale di Azure, permette di creare, modificare e distribuire file JSON che definiscono servizi collegati, set di dati e pipeline. L'obiettivo principale dell'editor consiste nell'offrire un'interfaccia utente veloce e leggera per creare elementi di Data factory di Azure senza richiedere l'installazione di Azure PowerShell e l'uso intenso dei cmdlet di PowerShell. Per una breve panoramica e un video sull'editor di Data factory, vedere il post di blog [Editor di Data factory di Azure - Editor Web leggero][adf-editor-blog]. Per una panoramica dettagliata dell'editor, vedere l'articolo [Editor di Data factory][adf-editor].          

### Modifiche

## Note sulla versione di Data factory - 26/01/2015 ##

### Modifiche
- Il pacchetto di installazione aggiornato per **Gateway di gestione dati** è stato inserito nell'[Area download Microsoft][adf-gateway-download]. A partire da questa versione, è possibile trovare il Gateway di gestione dati più recente da utilizzare con Data factory di Azure in questo percorso di download. Il pacchetto di installazione viene usato sia per il servizio Data factory di Azure che per il servizio Power BI per Office 365. Se si usano entrambi i servizi, si noti che è necessario installare i gateway per Data factory e Power BI in computer diversi e configurarli in modo diverso, come indicato nella documentazione relativa a Data factory o Power BI.
- L'**attività di copia** supporta ora la copia di dati tra il database SQL Server locale e un database SQL di Azure. 
- **SqlSink** supporta una nuova proprietà, **WriteBatchTimeout**. Questa proprietà offre la flessibilità necessaria per configurare il tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout dell'operazione. Per una copia ibrida, ovvero un'operazione di copia che coinvolge un'origine dati locale e un'origine dati cloud, è necessario disporre del gateway versione 1.4 o superiore per usare questa proprietà. 
- Il **servizio collegato SQL Server** supporta ora l'**Autenticazione di Windows**. 
	- Quando si crea un servizio collegato SQL Server usando il portale, è ora possibile scegliere di usare l'Autenticazione di Windows e impostare credenziali appropriate. Ciò richiede la disponibilità del gateway versione 1.4 o superiore. 
	- Quando si crea un servizio collegato SQL Server mediante Azure PowerShell, è possibile specificare le informazioni di connessione in testo normale oppure crittografarle usando il [cmdlet New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) aggiornato e quindi usare la stringa crittografata per la proprietà della stringa di connessione nel payload JSON del servizio collegato. La funzionalità di crittografia non è ancora supportata dal cmdlet New-AzureRmDataFactoryEncryptValue. 

## Note sulla versione di Data factory - 11/12/2014 ##

### Nuovi miglioramenti

- Integrazione con Azure Machine Learning
	- Questa versione del servizio Data factory di Azure consente di integrare Data factory di Azure con Azure Machine Learning (ML) mediante **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Per informazioni dettagliate, vedere [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning][adf-azure-ml]. 
- Viene fornito lo stato della versione del gateway
	- Se è disponibile una versione più recente del gateway rispetto a quella attualmente installata, verrà visualizzato lo stato "NewVersionAvailable" nel portale di Azure e nell'output del cmdlet AzureRmDataFactoryGateway. È quindi possibile seguire il percorso nel portale per scaricare il nuovo file di installazione (.msi) ed eseguirlo per installare il gateway più recente. Non è necessaria alcuna configurazione aggiuntiva.

### Modifiche

- Viene rimossa la proprietà JobsContainer in HdInsightOnDemandLinkedService
	- Nella definizione JSON per un HDInsightOnDemandLinkedService non è più necessario specificare la proprietà **jobsContainer**. Se è specificata per un servizio collegato su richiesta, la proprietà viene ignorata. È possibile rimuovere la proprietà dalla definizione JSON per il servizio collegato e aggiornare la definizione del servizio collegato tramite il cmdlet New-AzureRmDataFactoryLinkedService.
- Parametri di configurazione facoltativi per HDInsightOnDemandLinkedService
	- Questa versione introduce il supporto per alcuni parametri di configurazione facoltativi per HDInsightOnDemandLinked (cluster di HDInsight su richiesta). Per informazioni dettagliate, vedere la pagina relativa alle [proprietà ClusterCreateParameters][on-demand-hdi-parameters].
- Viene rimosso il percorso del gateway
	- Quando si crea un gateway di Data factory di Azure tramite il portale o tramite PowerShell (New-AzureRmDataFactoryGateway), non è più necessario specificare il percorso per il gateway. Verrà ereditata l'area di Data factory. Analogamente, per configurare un collegato servizio di SQL Server mediante JSON, la proprietà "gatewayLocation" non è più necessaria. .NET SDK di Data factory è stato aggiornato in base a queste modifiche.
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
[adf-editor]: data-factory-editor.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=AcomDC_1210_2015-->