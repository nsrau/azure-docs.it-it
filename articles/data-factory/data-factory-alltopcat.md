<properties
	pageTitle="Tutti gli argomenti per il servizio Azure Data Factory | Microsoft Azure"
	description="Tabella di tutti gli argomenti per il servizio Azure Data Factory presenti in http://azure.microsoft.com/documentation/articles/, con titolo e descrizione."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor=""/>  

<tags
	ms.service="data-factory"
	ms.workload="data-factory"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="spelluru"/>  


# Tutti gli argomenti per il servizio Azure Data Factory

Questo argomento fornisce un elenco di tutti gli argomenti applicabili direttamente al servizio **Azure Data Factory**. È possibile cercare la pagina Web per le parole chiave utilizzando **Ctrl+F**, per trovare gli argomenti di interesse corrente.




## Nuovo

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 1 | [Creare la prima data factory di Azure usando l'API REST di Data Factory](data-factory-build-your-first-pipeline-using-rest-api.md) | In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory usando l'API REST di Data Factory. |
| 2 | [Esercitazione: Creare una pipeline con l'attività di copia usando l'API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando l'API REST. |
| 3 | [Copia guidata di data factory](data-factory-copy-wizard.md) | Informazioni su come usare Copia guidata di data factory per copiare i dati da origini dati supportate nei sink. |
| 4 | [Gateway di gestione dati](data-factory-data-management-gateway.md) | Configurare un gateway dati per spostare dati tra origini locali e il cloud. Usare Gateway di gestione dati in Azure Data Factory per spostare dati. |
| 5 | [Spostare dati da un database Cassandra locale mediante Azure Data Factory](data-factory-onprem-cassandra-connector.md) | Informazioni su come spostare dati da un database Cassandra locale mediante Azure Data Factory. |
| 6 | [Spostare i dati da MongoDB con Azure Data Factory](data-factory-on-premises-mongodb-connector.md) | Informazioni su come spostare i dati dal database di MongoDB con Azure Data Factory. |
| 7 | [Spostare dati da Salesforce usando Azure Data Factory](data-factory-salesforce-connector.md) | Informazioni su come spostare dati da Salesforce usando Azure Data Factory. |


## Articoli aggiornati

Questa sezione fornisce un elenco degli articoli aggiornati di recente, in cui l'aggiornamento è stato importante o significativo. Per ogni articolo aggiornato, viene visualizzato un frammento di codice approssimativo del testo markdown aggiunto. Gli articoli sono stati aggiornati nell'intervallo di date dal **26-07-2016** al **21-08-2016**.

| &nbsp; | Articolo | Testo aggiornato, frammento di codice |
| --: | :-- | :-- |
| 8 | [Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante Data Factory .NET SDK](data-factory-create-data-factories-programmatically.md) | ** Accedere senza la finestra di dialogo popup** Il codice di esempio precedente avviata una finestra di dialogo per l'immissione delle credenziali di Azure. Se è necessario l'accesso a livello di codice senza usare una finestra di dialogo, vedere Autenticazione di un'entità servizio con Azure Resource Manager (resource-group-authenticate-service-principal.md authenticate-service-principal-with-certificate---powershell). ** Esempio** Creare il metodo GetAuthorizationHeaderNoPopup come illustrato di seguito: stringa statica pubblica GetAuthorizationHeaderNoPopup() { var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings "ActiveDirectoryTenantId" ); var context = new AuthenticationContext(authority.AbsoluteUri); var credential = new ClientCredential(ConfigurationManager.AppSettings "AdfClientId" , ConfigurationManager.AppSettings "AdfClientSecret" ); AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings "WindowsManagementUri" , credential).Result; |
| 9 | [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md) | ** Formati di file supportati** L'attività di copia può copiare i file così come sono tra due archivi dati basati su file, come BLOB di Azure, file system e Hadoop Distributed File System (HDFS). A tale scopo, è possibile ignorare la sezione format (data-factory-create-datasets.md) nelle definizioni dei set di dati di input e di output per copiare i dati in modo efficiente senza alcuna serializzazione o deserializzazione. L'attività di copia legge e scrive anche nei file in formati specificati: testo, Avro, ORC e JSON. Di seguito sono riportati alcuni esempi di attività di copia che è possibile eseguire: / Copiare dati in formato testo (CSV) dal BLOB di Azure e scriverli nel database SQL di Azure / Copiare file in formato testo (CSV) dal file system locale e scriverli nel BLOB di Azure in formato Avro / Copiare i dati nel database SQL di Azure e scriverli nell'HDFS locale in formato ORC ** .a name="global"../a.Spostamento dati disponibile a livello globale** Il servizio su cui si basa l'attività di copia è disponibile a livello globale nelle aree geografiche seguenti, anche se Azure Data Factory è disponibile solo negli Stati Uniti occidentali, negli Stati Uniti orientali e in Europa settentrionale.|
| 10 | [Spostare i dati da un'origine OData usando Azure Data Factory](data-factory-odata-connector.md) | ** Uso dell'autenticazione di Windows per accedere all'origine OData locale** { "name": "inputLinkedService", "properties": { "type": "OData", "typeProperties": { "url": ".endpoint of on-premises OData source e.g. Dynamics CRM.", "authenticationType": "Windows", "username": "domain\\user", "password": "password", "gatewayName": "mygateway" } } } |





## Esercitazioni

| &nbsp; | Titolo | Description |
| --: | :-- | :-- |
| 11 | [Esercitazione: Creare la prima pipeline per elaborare i dati usando il cluster Hadoop](data-factory-build-your-first-pipeline.md) | Questa esercitazione di Azure Data Factory illustra come creare e pianificare una data factory che elabora i dati usando uno script Hive in un cluster Hadoop. |
| 12 | [Esercitazione: Creare la prima data factory di Azure usando il modello di Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) | In questa esercitazione si creerà una pipeline di esempio di Data factory di Azure usando il modello di Gestione risorse di Azure. |
| 13 | [Creare la prima data factory di Azure usando il portale di Azure/l'editor di Data Factory](data-factory-build-your-first-pipeline-using-editor.md) | In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory usando l'editor di Data Factory nel portale di Azure. |
| 14 | [Creare la prima data factory di Azure con Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) | In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory usando Azure PowerShell. |
| 15 | [Creare la prima data factory di Azure con Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory usando Visual Studio. |
| 16 | [Esercitazione: Creare una pipeline con l’attività Copia utilizzando Data Factory Editor](data-factory-copy-activity-tutorial-using-azure-portal.md) | In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory con un'attività di copia usando l'editor di Data Factory nel portale di Azure. |
| 17 | [Esercitazione: Creare una pipeline con l’attività Copia utilizzando Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) | In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando Azure PowerShell. |
| 18 | [Esercitazione: Creare una pipeline con l’attività Copia utilizzando Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando Visual Studio. |
| 19 | [Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Questa esercitazione illustra come usare l'attività di copia in una pipeline di Azure Data Factory per copiare i dati da un archivio BLOB a un database SQL. |
| 20 | [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md) | In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando la Copia guidata supportata da Data Factory. |



## Spostamento dei dati

| &nbsp; | Titolo | Description |
| --: | :-- | :-- |
| 21 | [Spostare dati da e verso il BLOB di Azure mediante Data factory di Azure](data-factory-azure-blob-connector.md) | Informazioni su come copiare dati BLOB in Azure Data Factory. Usare l'esempio: Come copiare dati da e verso l'archivio BLOB di Azure e il database SQL di Azure. |
| 22 | [Spostare dati da e in Archivio Azure Data Lake con Data factory di Azure](data-factory-azure-datalake-connector.md) | Informazioni su come spostare i dati da e in Archivio Azure Data Lake con Azure Data Factory |
| 23 | [Spostare dati da e verso DocumentDB mediante Data factory di Azure](data-factory-azure-documentdb-connector.md) | Informazioni su come spostare i dati da e verso la raccolta di Azure DocumentDB mediante Data factory di Azure |
| 24 | [Spostare dati da e nel database SQL di Azure con Data factory di Azure](data-factory-azure-sql-connector.md) | Informazioni su come spostare i dati da e verso il database SQL di Azure mediante Data factory di Azure. |
| 25 | [Spostare dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure](data-factory-azure-sql-data-warehouse-connector.md) | Informazioni su come spostare i dati da e verso Azure SQL Data Warehouse mediante Data factory di Azure |
| 26 | [Spostare dati da e verso le tabelle di Azure mediante Data factory di Azure](data-factory-azure-table-connector.md) | Informazioni su come spostare i dati da e verso l'archiviazione tabelle di Azure mediante Data factory di Azure. |
| 27 | [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md) | Informazioni sui fattori chiave che influiscono sulle prestazioni di spostamento dei dati in Data factory di Azure tramite l'attività di copia. |
| 28 | [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md) | Informazioni sullo spostamento di dati in pipeline di Data Factory: migrazione di dati tra archivi cloud e tra archivi locali e cloud. Usare l'attività di copia. |
| 29 | [Note sulla versione di Gateway di gestione dati](data-factory-gateway-release-notes.md) | Note sulla versione di Gateway di gestione dati |
| 30 | [Spostare dati da HDFS locale con Data factory di Azure](data-factory-hdfs-connector.md) | Informazioni su come spostare dati da un HDFS locale con Azure Data Factory. |
| 31 | [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md) | Informazioni sull'uso dell'app di monitoraggio e gestione per monitorare e gestire le data factory e le pipeline di Azure. |
| 32 | [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) | Configurare un gateway dati per spostare dati tra origini locali e il cloud. Usare Gateway di gestione dati in Azure Data Factory per spostare dati. |
| 33 | [Spostare i dati da un'origine OData usando Azure Data Factory](data-factory-odata-connector.md) | Informazioni su come spostare i dati da origini OData usando Azure Data Factory. |
| 34 | [Spostare dati da archivi dati ODBC con Data factory di Azure](data-factory-odbc-connector.md) | Informazioni su come spostare dati da archivi dati ODBC con Azure Data Factory. |
| 35 | [Spostare i dati da DB2 mediante Data factory di Azure](data-factory-onprem-db2-connector.md) | Informazioni su come spostare i dati dal database di DB2 mediante Data factory di Azure |
| 36 | [Spostare i dati da e nel file system locale usando Data factory di Azure](data-factory-onprem-file-system-connector.md) | Informazioni su come spostare i dati da e verso il file system locale usando Azure Data Factory |
| 37 | [Spostare i dati da MySQL mediante Data factory di Azure](data-factory-onprem-mysql-connector.md) | Informazioni su come spostare i dati dal database di MySQL mediante Data factory di Azure. |
| 38 | [Spostare i dati da/verso Oracle locale con Azure Data Factory](data-factory-onprem-oracle-connector.md) | Informazioni su come spostare i dati da e verso database Oracle in locale mediante Data factory di Azure. |
| 39 | [Spostare i dati da PostgreSQL mediante Data factory di Azure](data-factory-onprem-postgresql-connector.md) | Informazioni su come spostare i dati dal database di PostgreSQL mediante Data factory di Azure. |
| 40 | [Spostare i dati da Sybase utilizzando Data factory di Azure](data-factory-onprem-sybase-connector.md) | Informazioni su come spostare i dati dal Database di Sybase utilizzando Data factory di Azure. |
| 41 | [Spostare i dati da Teradata utilizzando Data factory di Azure](data-factory-onprem-teradata-connector.md) | Informazioni su come il connettore Teradata per il servizio Data factory consente di spostare dati dal database Teradata |
| 42 | [Spostare i dati da e verso SQL Server locale o in IaaS (VM di Azure) utilizzando Data factory di Azure](data-factory-sqlserver-connector.md) | Informazioni su come spostare i dati da/verso il database di SQL Server locale o in una VM di Azure utilizzando Data factory di Azure. |
| 43 | [Spostare i dati da un'origine tabella Web usando Azure Data Factory](data-factory-web-table-connector.md) | Informazioni su come spostare dati da una tabella locale a una pagina Web con Azure Data Factory. |



## Trasformazione dei dati

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 44 | [Creare pipeline predittive con le attività di Azure Machine Learning](data-factory-azure-ml-batch-execution-activity.md) | Illustra come creare pipeline predittive usando Data factory di Azure e Azure Machine Learning |
| 45 | [Servizi collegati di calcolo](data-factory-compute-linked-services.md) | Informazioni sugli ambienti di calcolo che è possibile utilizzare nelle pipeline di Data Factory di Azure per trasformare/elaborare i dati.. |
| 46 | [Elaborazione di fogli dati su larga scala con Data Factory e Batch](data-factory-data-processing-using-batch.md) | Descrive come elaborare elevate quantità di dati in una pipeline di Data factory di Azure usando la funzionalità di elaborazione parallela di Azure Batch. |
| 47 | [Informazioni sulla trasformazione e l'analisi dei dati in Azure Data Factory](data-factory-data-transformation-activities.md) | Informazioni sulla trasformazione dei dati in Azure Data Factory. Trasformare ed elaborare i dati in un cluster HDInsight di Azure o in un'istanza di Azure Batch. |
| 48 | [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | Informazioni su come usare l'attività di Hadoop Streaming in una Data factory di Azure per eseguire i programmi di Hadoop Streaming in un cluster HDInsight personalizzato o on demand. |
| 49 | [Attività Hive](data-factory-hive-activity.md) | Informazioni su come usare l'attività Hive in una data factory di Azure per eseguire query Hive in un cluster HDInsight su richiesta o nel proprio cluster HDInsight. |
| 50 | [Richiamare i programmi MapReduce da Data factory](data-factory-map-reduce.md) | Informazioni su come elaborare i dati eseguendo programmi MapReduce in un cluster Azure HDInsight da un'istanza di Data factory di Azure. |
| 51 | [Attività di Pig](data-factory-pig-activity.md) | Informazioni su come usare l'attività Pig in una data factory di Azure per eseguire query Pig in un cluster HDInsight su richiesta o nel proprio cluster HDInsight. |
| 52 | [Chiamare i programmi Spark da Data Factory](data-factory-spark.md) | È possibile chiamare i programmi Spark da una data factory di Azure usando l'attività MapReduce. |
| 53 | [Attività di stored procedure di SQL Server](data-factory-stored-proc-activity.md) | Informazioni sull'uso dell'attività di stored procedure di SQL Server da una pipeline di Data factory per richiamare una stored procedure in un database SQL di Azure o in Azure SQL Data Warehouse. |
| 54 | [Usare attività personalizzate in una pipeline di Data factory di Azure](data-factory-use-custom-activities.md) | Informazioni su come creare attività personalizzate e usarle in una pipeline di Azure Data Factory. |
| 55 | [Eseguire script U-SQL in Azure Data Lake Analytics da Data factory di Azure](data-factory-usql-activity.md) | Informazioni su come elaborare i dati eseguendo gli script U-SQL nel servizio di calcolo di Azure Data Lake Analytics. |



## Esempi

| &nbsp; | Titolo | Description |
| --: | :-- | :-- |
| 56 | [Data factory di Azure - Esempi](data-factory-samples.md) | Fornisce informazioni dettagliate sugli esempi inclusi nel servizio Data factory di Azure. |



## Casi di utilizzo

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 57 | [Casi di studio sui clienti](data-factory-customer-case-studies.md) | Scopri come alcuni dei nostri clienti utilizzano Data Factory di Azure. |
| 58 | [Caso di utilizzo - Profilo clienti](data-factory-customer-profiling-usecase.md) | Informazioni su come utilizzare Data Factory di Azure per creare un flusso di lavoro basato sui dati (pipeline) per analizzare il profilo dei clienti di società di giochi. |
| 59 | [Caso d'uso - Consigli sui prodotti](data-factory-product-reco-usecase.md) | Descrizione di un caso d'uso implementato usando Data factory di Azure e altri servizi. |



## Monitoraggio e gestione

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 60 | [Monitorare e gestire le pipeline di Data factory di Azure](data-factory-monitor-manage-pipelines.md) | Sono disponibili informazioni sull'uso del portale di Azure e di Azure PowerShell per monitorare e gestire le data factory di Azure e le pipeline create. |



## SDK

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 61 | [Data factory di Azure: Log delle modifiche dell'API .NET](data-factory-api-change-log.md) | Vengono descritte le modifiche rilevanti, le aggiunte di funzionalità, le correzioni di bug e così via in una versione specifica dell'API .NET per Azure Data Factory. |
| 62 | [Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante Data Factory .NET SDK](data-factory-create-data-factories-programmatically.md) | Informazioni su come creare, monitorare e gestire a livello di codice le istanze di Data factory di Azure usando Data Factory SDK. |
| 63 | [Guida di riferimento per gli sviluppatori di Data factory di Azure](data-factory-sdks.md) | Informazioni sui vari modi per creare, monitorare e gestire data factory di Azure |



## Miscellaneous

| &nbsp; | Titolo | Description |
| --: | :-- | :-- |
| 64 | [Data factory di Azure - Domande frequenti](data-factory-faq.md) | Domande frequenti su Azure Data Factory. |
| 65 | [Azure Data Factory - Funzioni e variabili di sistema](data-factory-functions-variables.md) | Fornisce un elenco delle funzioni e delle variabili di sistema di Azure Data Factory |
| 66 | [Data factory di Azure - Regole di denominazione](data-factory-naming-rules.md) | Descrive le regole di denominazione per le entità di Data factory. |
| 67 | [Risolvere i problemi di Data factory](data-factory-troubleshoot.md) | Informazioni su come risolvere i problemi relativi all'uso di Data factory di Azure. |

<!---HONumber=AcomDC_0914_2016-->