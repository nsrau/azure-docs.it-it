<properties
	pageTitle="Tutti gli argomenti per il servizio di database SQL | Microsoft Azure"
	description="Tabella di tutti gli argomenti per il servizio di Azure denominato database SQL presente in http://azure.microsoft.com/documentation/articles/, titolo e descrizione."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="genemi"/>


# Tutti gli argomenti per il servizio database SQL di Azure

In questo argomento vengono elencati tutti gli argomenti che si applicano direttamente al servizio **database SQL** di Azure. È possibile cercare la pagina Web per le parole chiave utilizzando **Ctrl+F**, per trovare gli argomenti di interesse corrente.




## Nuovo

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 1 | [Introduzione alle funzionalità JSON nel database SQL di Azure](sql-database-json-features.md) | Il database SQL di Azure consente di analizzare, formattare ed eseguire query sui dati nella notazione JavaScript Object Notation (JSON). |
| 2 | [Supporto di SQL Server Management Studio (SSMS) per l'autenticazione MFA di Azure AD con database SQL e SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Utilizzare Multi-Factored Authentication con SSMS per il database SQL e SQL Data Warehouse. |


## Articoli aggiornati

Questa sezione elenca gli articoli aggiornati di recente, in cui l'aggiornamento è stato importante o significativo. Per ogni articolo aggiornato, viene visualizzato un frammento di codice del testo di markdown aggiunto. Gli articoli sono stati aggiornati entro l'intervallo di date da **26-07-2016** a **21-08-2016**.

| &nbsp; | Articolo | Testo aggiornato, frammento di codice |
| --: | :-- | :-- |
| 3 | [Archiviare un database SQL di Azure come file BACPAC tramite PowerShell](sql-database-export-powershell.md) | $subscriptionId = "YOUR AZURE SUBSCRIPTION ID" Login-AzureRmAccount Set-AzureRmContext -SubscriptionId $subscriptionId Database to export $DatabaseName = "DATABASE-NAME" $ResourceGroupName = "RESOURCE-GROUP-NAME" $ServerName = "SERVER-NAME" $serverAdmin = "ADMIN-NAME" $serverPassword = "ADMIN-PASSWORD" $securePassword = ConvertTo-SecureString ΓÇôString $serverPassword ΓÇôAsPlainText -Force $creds = New-Object ΓÇôTypeName System.Management.Automation.PSCredential ΓÇôArgumentList $serverAdmin, $securePassword Generate a unique filename for the BACPAC $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac" Storage account info for the BACPAC $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/" $BacpacUri = $BaseStorageUri + $bacpacFilename $StorageKeytype = "StorageAccessKey" $StorageKey = "YOUR STORAGE KEY" $exportRequest = New-AzureRmSqlDatabaseExpo |
| 4 | [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md) | **Scelta di un livello di servizio** Per stabilire un livello di servizio, iniziare determinando se il database sarà un database autonomo o farà parte di un pool elastico. ** Scelta di un livello di servizio per un database autonomo** Per stabilire un livello di servizio per un database autonomo, iniziare determinando le funzionalità del database richieste per scegliere l'edizione del database SQL:/Dimensioni del database (5 GB massimo per l'edizione Basic, 250 GB massimo per l'edizione Standard e da 500 GB a 1 TB massimo per l'edizione Premium, a seconda del livello di prestazioni)/Periodo di conservazione del backup del database (7 giorni per l'edizione Basic, 35 giorni per l'edizione Standard e 35 giorni per l'edizione Premium). Dopo aver determinato l'edizione del database SQL, è possibile scegliere il livello di prestazioni per il database (il numero di DTU). Si può fare una supposizione e quindi aumentare o ridurre le prestazioni in modo dinamico (sql-database-scale-up.md) in base all'esperienza effettiva. È anche possibile usare lo strumento per il calcolo di DTU (http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessario.** C |





## Introduzione

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 5 | [Compilare app multi-tenant tramite il database SQL di Azure con isolamento ed efficienza](sql-database-build-multi-tenant-apps.md) | Informazioni su come compilare app multi-tenant con il database SQL di Azure |
| 6 | [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md) | Informazioni su come connettersi a un database SQL in Azure tramite SQL Server Management Studio (SSMS). Eseguire quindi una query di esempio usando Transact-SQL (T-SQL). |
| 7 | [Connettersi al database SQL tramite .NET (C#)](sql-database-develop-dotnet-simple.md) | Usare il codice di esempio in questa guida introduttiva per creare con C# un'applicazione moderna, supportata da un database relazionale potente nel cloud con il database SQL di Azure. |
| 8 | [Creare un nuovo pool di database elastici con il portale di Azure](sql-database-elastic-pool-create-portal.md) | Come aggiungere un pool di database elastici scalabile alla configurazione del database SQL per semplificare l'amministrazione e la condivisione delle risorse tra più database. |
| 9 | [Esplorare le esercitazioni del database SQL di Azure](sql-database-explore-tutorials.md) | Informazioni sulle caratteristiche e sulle funzionalità del database SQL |
| 10 | [Esercitazione sul database SQL: Creare un database SQL in pochi minuti usando il portale di Azure](sql-database-get-started.md) | Informazioni su come configurare un server logico di database SQL, una regola firewall del server, un database SQL, usare dati di esempio, connettersi con strumenti client, configurare gli utenti e una regola firewall del database. |
| 11 | [Il database SQL di Azure facilita la sicurezza e la protezione](sql-database-helps-secures-and-protects.md) | Informazioni sul modo in cui il database SQL facilita la sicurezza e la protezione |
| 12 | [Il database SQL di Azure apprende e si adatta](sql-database-learn-and-adapt.md) | Informazioni su come il database SQL di Azure apprende e si adatta |
| 13 | [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Informazioni su come trovare l'opzione di SQL Server cloud ottimale per l'applicazione, ovvero database SQL di Azure (PaaS) o SQL Server nel cloud su macchine virtuali di Azure. |
| 14 | [Scalabilità immediata del database SQL di Azure](sql-database-scale-on-the-fly.md) | Informazioni sulla scalabilità immediata del database SQL |
| 15 | [Esplorare le procedure di avvio rapido per la soluzione di database SQL di Azure](sql-database-solution-quick-starts.md) | Informazioni sulle soluzioni del database SQL di Azure |
| 16 | [Il database SQL di Azure funziona negli ambienti personalizzati](sql-database-works-in-your-environment.md) | Informazioni sul modo in cui il database SQL facilita la sicurezza e la protezione |



## Creazione di un'applicazione

| &nbsp; | Titolo | Description |
| --: | :-- | :-- |
| 17 | [Risolvere, diagnosticare ed evitare gli errori di connessione SQL e gli errori temporanei per il database SQL](sql-database-connectivity-issues.md) | Informazioni su come risolvere, diagnosticare ed evitare un errore di connessione SQL o errore temporaneo nel database SQL di Azure. |
| 18 | [Connettersi a un database SQL con Visual Studio](sql-database-connect-query.md) | Scrivere un programma in C# per eseguire query e connettersi al database SQL. Informazioni su indirizzi IP, stringhe di connessione, accesso sicuro e Visual Studio gratuito. |
| 19 | [Porte superiori a 1433 per ADO.NET 4.5, e database SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md) | Le connessioni client da ADO.NET al database SQL V12 di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti. |
| 20 | [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md) | Informazioni sui codici di errore SQL per le applicazioni client del database SQL, ad esempio errori di connessione comuni del database, problemi di copia del database ed errori generali. |
| 21 | [Connettersi al database SQL tramite PHP in Windows](sql-database-develop-php-simple.md) | Presentazione di un programma PHP di esempio che si connette al database SQL di Azure da un client Windows e fornisce collegamenti ai componenti software necessari per il client. |
| 22 | [Prova del database SQL: usare C# per creare un database SQL con la libreria del database SQL per .NET](sql-database-get-started-csharp.md) | Provare il database SQL per lo sviluppo di app SQL e C# e creare un database SQL di Azure con C# usando la libreria di database SQL per .NET. |
| 23 | [Risoluzione dei problemi di connessione al database SQL di Azure](sql-database-troubleshoot-common-connection-issues.md) | Passaggi per identificare e risolvere gli errori di connessione comuni al database SQL di Azure. |
| 24 | [Errore "Il database nel server non è attualmente disponibile" durante la connessione al database SQL](sql-database-troubleshoot-connection.md) | Risolvere l'errore relativo al database nel server che non è attualmente disponibile quando un'applicazione si connette al database SQL. |



## Sviluppare

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 25 | [Ottenere l'ID client e la chiave per la connessione al database SQL dal codice](sql-database-client-id-keys.md) | Come ottenere l'ID client e la chiave per l'accesso al database SQL dal codice. |
| 26 | [Connettersi al database SQL tramite Java con JDBC in Windows](sql-database-develop-java-simple.md) | Presentazione di un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio usa JDBC e viene eseguito su un computer client Windows. |
| 27 | [Connettersi al Database SQL tramite Node.js](sql-database-develop-nodejs-simple.md) | Presentazione di un esempio di codice Node.js che è possibile usare per connettersi al database SQL di Azure. |
| 28 | [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md) | Informazioni sulle librerie di connettività disponibili e procedure consigliate per applicazioni che si connettono al Database SQL. |
| 29 | [Connettersi al database SQL tramite Python](sql-database-develop-python-simple.md) | Presentazione di un esempio di codice Python che è possibile usare per connettersi al database SQL di Azure. |
| 30 | [Connettersi al database SQL tramite Ruby](sql-database-develop-ruby-simple.md) | Specificare un esempio di codice Ruby che è possibile eseguire per connettersi al database SQL di Azure. |
| 31 | [Introduzione alle tabelle temporali nel database SQL di Azure](sql-database-temporal-tables.md) | Informazioni introduttive sull'uso delle tabelle temporali nel database SQL di Azure. |



## Prestazioni e scalabilità

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 32 | [Advisor per database SQL](sql-database-advisor.md) | L'advisor per database SQL di Azure offre raccomandazioni per i database SQL esistenti che consentono di migliorare le prestazioni correnti delle query. |
| 33 | [Advisor per database SQL](sql-database-advisor-portal.md) | È possibile usare Azure SQL Database Advisor nel portale di Azure per rivedere e implementare le raccomandazioni per i database SQL esistenti che consentono di migliorare le prestazioni correnti delle query. |
| 34 | [Informazioni generali sul benchmark del database SQL di Azure](sql-database-benchmark-overview.md) | Questo argomento illustra il benchmark ASDB (Azure SQL Database Benchmark) usato per la misurazione delle prestazioni del database SQL di Azure. |
| 35 | [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md) | Un pool di database elastico è una raccolta di risorse disponibili condivise da un gruppo di database elastici. Questo documento vengono fornite informazioni utili per valutare l'idoneità dell'utilizzo di un pool di database flessibile per un gruppo di database. |
| 36 | [Iniziare a utilizzare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md) | Spiegazione di base della funzionalità relativa agli strumenti di database elastici del database SQL di Azure, che include un'app di esempio di facile esecuzione. |
| 37 | [Domande frequenti sul database SQL](sql-database-faq.md) | Le risposte a domande comuni dei clienti su database cloud e Database SQL di Azure, sistema di gestione di database relazionali di Microsoft (RDBMS) e database come servizio nel cloud. |
| 38 | [Introduzione alle tecnologie in memoria (anteprima) in database SQL](sql-database-in-memory.md) | Le tecnologie SQL in memoria migliorano notevolmente le prestazioni dei carichi di lavoro transazionali e analitici. Informazioni su come sfruttare i vantaggi di queste tecnologie. |
| 39 | [Uso di OLTP in memoria (anteprima) per migliorare le prestazioni delle applicazioni nel database SQL](sql-database-in-memory-oltp-migration.md) | Adottare OLTP in memoria per migliorare le prestazioni transazionali in un database SQL esistente. |
| 40 | [Monitorare l'archiviazione OLTP in memoria](sql-database-in-memory-oltp-monitoring.md) | Stimare e monitorare l'uso e la capacità delle risorse di archiviazione in memoria XTP e risolvere l'errore di capacità 41823 |
| 41 | [Uso dell'archivio query nel database SQL di Azure](sql-database-operate-query-store.md) | Informazioni su come usare l'archivio query nel database SQL di Azure |
| 42 | [Informazioni dettagliate sulle prestazioni del database SQL](sql-database-performance.md) | Il database SQL di Azure offre strumenti per le prestazioni che consentono di identificare le aree in cui è possibile migliorare le prestazioni delle query correnti. |
| 43 | [Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](sql-database-performance-guidance.md) | Questo argomento fornisce indicazioni per stabilire quale livello di servizio rappresenta la soluzione adatta all'applicazione in uso e fornisce consigli per ottimizzare l'applicazione in modo da ottenere il massimo dal database SQL di Azure. |
| 44 | [Query Performance Insight del database SQL di Azure](sql-database-query-performance.md) | Il monitoraggio delle prestazioni delle query identifica le query principali a livello di utilizzo di CPU per un database SQL di Azure. |
| 45 | [Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL](sql-database-scale-up.md) | La modifica del livello del servizio e del livello delle prestazioni di un database SQL di Azure mostra come scalare il database SQL verso l’alto o verso il basso. Modificare il livello di prezzo di un database SQL di Azure. |
| 46 | [Monitoraggio delle prestazioni del database nel database SQL di Azure](sql-database-single-database-monitor.md) | Informazioni sulle opzioni per il monitoraggio del database con gli strumenti di Azure e le viste a gestione dinamica. |
| 47 | [Suggerimenti sull'ottimizzazione delle prestazioni del database SQL](sql-database-troubleshoot-performance.md) | Suggerimenti per l'ottimizzazione delle prestazioni nel database SQL di Azure tramite valutazione e miglioramento. |
| 48 | [Come usare l'invio in batch per migliorare le prestazioni delle applicazioni di database SQL](sql-database-use-batching-to-improve-performance.md) | Questo argomento dimostra che le operazioni di database in batch migliorano significativamente la velocità e la scalabilità delle applicazioni di database SQL di Azure. Anche se le tecniche di invio in batch funzionano con qualsiasi database SQL, questo articolo è incentrato su Azure. |



## Strumenti per la scalabilità orizzontale

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 49 | [Modelli di progettazione per le applicazioni SaaS multi-tenant e il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Questo articolo illustra i requisiti e i modelli comuni di architettura dei dati delle applicazioni di database multi-tenant in esecuzione in un ambiente cloud e i compromessi associati a tali modelli. Illustra anche l'utilità del database SQL di Azure, con i relativi pool di database elastici e strumenti elastici, per soddisfare questi requisiti senza dover scendere a compromessi. |
| 50 | [Migrate existing databases to scale-out (Eseguire la migrazione di database esistenti per la scalabilità orizzontale)](sql-database-elastic-convert-to-use-elastic-tools.md) | Convertire database partizionati per l'uso di strumenti dei database elastici mediante la creazione di un gestore mappe partizioni |
| 51 | [Creazione di database cloud scalabili](sql-database-elastic-database-client-library.md) | Creare applicazioni di database .NET scalabili con la libreria client di database elastici |
| 52 | [Contatori delle prestazioni per Gestore mappe partizioni](sql-database-elastic-database-perf-counters.md) | La classe ShardMapManager e i contatori delle prestazioni con routing dipendente dai dati |
| 53 | [Aggiunta di una partizione utilizzando gli strumenti di database elastici](sql-database-elastic-scale-add-a-shard.md) | Informazioni su come impostare API di scalabilità elastica per aggiungere nuove partizioni a un set di partizioni. |
| 54 | [Distribuire un servizio di divisione e unione](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Suddivisione e unione con gli strumenti di database elastico |
| 55 | [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md) | Come usare la classe ShardMapManager nelle app .NET per il routing basato sui dati, una funzionalità dei database elastici per il Database SQL di Azure |
| 56 | [Domande frequenti sugli strumenti di database elastici](sql-database-elastic-scale-faq.md) | Domande frequenti sulla scalabilità elastica del database SQL di Azure. |
| 57 | [Glossario sugli strumenti di database elastici](sql-database-elastic-scale-glossary.md) | Spiegazione dei termini utilizzati per gli strumenti dei database elastici |
| 58 | [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md) | Gli sviluppatori di Software come Servizio (Saas) possono facilmente creare database elastici e scalabili nel cloud utilizzando questi strumenti |
| 59 | [Credenziali usate per accedere alla libreria client dei database elastici](sql-database-elastic-scale-manage-credentials.md) | Come impostare il livello corretto di credenziali, da amministratore a sola lettura, per le app dei database elastici. |
| 60 | [Esecuzione di query su più partizioni](sql-database-elastic-scale-multishard-querying.md) | Eseguire query tra partizioni utilizzando la libreria client dei database elastici. |
| 61 | [Spostamento di dati tra database cloud con scalabilità orizzontale](sql-database-elastic-scale-overview-split-and-merge.md) | Illustra come gestire partizioni e spostare dati tramite un servizio self-hosted usando API di database elastici. |
| 62 | [Aumentare il numero di istanze dei database con il gestore delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md) | Come utilizzare ShardMapManager, libreria client dei database elastici |
| 63 | [Configurazione della sicurezza del servizio di divisione e unione](sql-database-elastic-scale-split-merge-security-configuration.md) | Impostazione dei certificati 409 per la crittografia |
| 64 | [Aggiornare un'app in modo da usare la libreria client dei database elastici più recente](sql-database-elastic-scale-upgrade-client-library.md) | Eseguire l'aggiornamento alle applicazioni e librerie mediante Nuget |
| 65 | [Libreria client dei database elastici con Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Usare la libreria client del database elastico e Entity Framework per la codifica di database |
| 66 | [Utilizzo della libreria client dei database elastici con Dapper](sql-database-elastic-scale-working-with-dapper.md) | Utilizzo della libreria client dei database elastici con Dapper. |
| 67 | [Applicazioni multi-tenant con strumenti di database elastici e sicurezza a livello di riga](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Informazioni su come utilizzare gli strumenti di database elastici insieme alla sicurezza a livello di riga per compilare un'applicazione con un livello dati altamente scalabile in un database SQL di Azure che supporta partizioni multi-tenant. |



## Processi elastici

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 68 | [Creare e gestire database SQL di Azure con scalabilità orizzontale tramite processi elastici (anteprima)](sql-database-elastic-jobs-create-and-manage.md) | Informazioni sulla gestione e la creazione di un processo elastico di database. |
| 69 | [Introduzione ai processi di Database Elastici](sql-database-elastic-jobs-getting-started.md) | come utilizzare i processi di database elastici |
| 70 | [Gestione dei database cloud con scalabilità orizzontale](sql-database-elastic-jobs-overview.md) | Viene illustrato il servizio processo di database elastico |
| 71 | [Creare e gestire processi di database elastici del database SQL tramite PowerShell (anteprima)](sql-database-elastic-jobs-powershell.md) | PowerShell viene utilizzato per gestire i pool del database SQL di Azure |
| 72 | [Installazione dei processi di database elastici (panoramica)](sql-database-elastic-jobs-service-installation.md) | Installazione dettagliata della funzionalità dei processi elastici. |
| 73 | [Disinstallare i componenti dei processi di database elastici](sql-database-elastic-jobs-uninstall.md) | Come disinstallare lo strumento processi di database elastico |



## Pool elastici

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 74 | [Che cos'è un pool di database elastici di Azure?](sql-database-elastic-pool.md) | È possibile gestire centinaia o migliaia di database con un pool. Un unico prezzo per un set di unità di prestazioni può essere distribuito nel pool. I database possono essere inseriti o rimossi in base alle esigenze. |
| 75 | [Creare un nuovo pool di database elastici con C#](sql-database-elastic-pool-create-csharp.md) | Usare tecniche di sviluppo di database in C# per creare un pool di database elastici scalabile nel database SQL di Azure per poter condividere risorse tra più database. |
| 76 | [Creare un nuovo pool di database elastici con PowerShell](sql-database-elastic-pool-create-powershell.md) | Informazioni su come usare PowerShell per la scalabilità delle risorse del database SQL di Azure tramite la creazione di un pool di database elastici scalabile per la gestione di più database. |
| 77 | [Sviluppo di database in C#: Creare e configurare un pool di database elastici per database SQL](sql-database-elastic-pool-csharp.md) | Usare tecniche di sviluppo di database in C# per creare un pool di database elastici per database SQL di Microsoft Azure in modo da poter condividere risorse tra più database. |
| 78 | [Script di PowerShell per identificare database adatti a un pool di database elastici](sql-database-elastic-pool-database-assessment-powershell.md) | Un pool di database elastico è una raccolta di risorse disponibili condivise da un gruppo di database elastici. Questo documento fornisce uno script di PowerShell per valutare l'idoneità dell'utilizzo di un pool di database elastici per un gruppo di database. |
| 79 | [Monitorare e gestire un pool di database elastici con C#](sql-database-elastic-pool-manage-csharp.md) | Usare tecniche di sviluppo di database in C# per gestire un pool di database elastici per database SQL di Microsoft Azure. |
| 80 | [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md) | Informazioni su come usare il portale di Azure e l'intelligenza integrata del database SQL per gestire, monitorare e dimensionare in modo adeguato un pool scalabile di database elastici per ottimizzare le prestazioni del database e gestire i costi. |
| 81 | [Monitorare e gestire un pool di database elastici con PowerShell](sql-database-elastic-pool-manage-powershell.md) | Informazioni su come usare PowerShell per gestire un pool di database elastici. |
| 82 | [Monitorare e gestire un pool di database elastici con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Usare T-SQL per creare un database SQL di Azure in un pool elastico. In alternativa, usare T-SQL per spostare il database all'interno e all'esterno di pool. |
| 83 | [Informazioni su prezzi e fatturazione dei pool di database elastici](sql-database-elastic-pool-price.md) | Informazioni specifiche sui prezzi dei pool di database elastici |



## Query elastica

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 84 | [Creare report in database cloud con numero maggiore di istanze (anteprima)](sql-database-elastic-query-getting-started.md) | come utilizzare tra le query di database tra database |
| 85 | [Introduzione alle query tra database (partizionamento verticale) (anteprima)](sql-database-elastic-query-getting-started-vertical.md) | Informazioni sull'uso della query del database elastico con database con partizionamento verticale. |
| 86 | [Creazione di report tra database cloud con scalabilità orizzontale (anteprima)](sql-database-elastic-query-horizontal-partitioning.md) | Come configurare query elastiche sui partizionamenti orizzontali |
| 87 | [Panoramica delle query su database elastico del database SQL di Azure (anteprima)](sql-database-elastic-query-overview.md) | Annuncia la funzionalità di query elastica |
| 88 | [Eseguire query in database cloud con schemi diversi (anteprima)](sql-database-elastic-query-vertical-partitioning.md) | Informazioni su come configurare le query tra database su partizioni verticali. |



## Transazione elastica

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 89 | [Transazioni distribuite in database cloud](sql-database-elastic-transactions-overview.md) | Panoramica delle transazioni di database elastico con il database SQL di Azure |



## Backup e ripristino

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 90 | [Backup automatici del database SQL](sql-database-automated-backups.md) | Informazioni sui backup predefiniti del database SQL che consentono di ripristinare un database SQL di Azure a un momento precedente o di copiare un database in uno nuovo in un'area geografica (fino a 35 giorni). |
| 91 | [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md) | Informazioni su come il database SQL di Azure supporta la continuità aziendale cloud e il ripristino del database e consente di mantenere le applicazioni cloud cruciali in esecuzione. |
| 92 | [Come ripristinare una singola tabella nel backup del database SQL di Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Learn how to restore a single table from Azure SQL Database backup. |
| 93 | [Progettare un'applicazione per il ripristino di emergenza cloud mediante la replica geografica attiva nel database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Informazioni su come progettare soluzioni di ripristino di emergenza cloud per la pianificazione della continuità aziendale usando la replica geografica per il backup di dati delle app con il database SQL di Azure. |
| 94 | [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md) | Informazioni su come ripristinare un database da un guasto o un'interruzione del servizio del data center a livello di area con le funzionalità di replica geografica attiva e ripristino geografico del database SQL. |
| 95 | [Esercitazione per il ripristino di emergenza](sql-database-disaster-recovery-drills.md) | Istruzioni e procedure consigliate relative all'uso del database SQL di Azure per eseguire esercitazioni per il ripristino di emergenza che consentono di mantenere la resilienza delle applicazioni aziendali cruciali in caso di errori e interruzioni del servizio. |
| 96 | [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Informazioni su come progettare la soluzione cloud per il ripristino di emergenza scegliendo il modello di failover appropriato. |
| 97 | [Uso della classe RecoveryManager per correggere i problemi delle mappe partizioni](sql-database-elastic-database-recovery-manager.md) | Usare la classe RecoveryManager per risolvere i problemi relativi alle mappe partizioni. |
| 98 | [Importare un file BACPAC per creare un nuovo database SQL di Azure](sql-database-import.md) | Creare un nuovo database SQL di Azure importando un file BACPAC esistente. |
| 99 | [Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure](sql-database-point-in-time-restore-portal.md) | Ripristinare un database SQL di Azure a un momento precedente. |
| 100 | [Ripristinare un database SQL di Azure a un momento precedente con PowerShell](sql-database-point-in-time-restore-powershell.md) | Ripristinare un database SQL di Azure a un momento precedente |
| 101 | [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md) | Ripristino temporizzato, Database SQL di Microsoft Azure, ripristino del database, recupero del database, portale di Azure classico, portale di Azure classico |
| 102 | [Ripristinare un database SQL di Azure mediante i backup automatici del database](sql-database-recovery-using-backups.md) | Informazioni sul ripristino temporizzato, che consente di ripristinare un database SQL di Azure a un momento precedente (fino a 35 giorni). |
| 103 | [Ripristinare un database SQL di Azure con il portale di Azure](sql-database-restore-deleted-database-portal.md) | Ripristinare un database SQL di Azure (Portale di Azure) |
| 104 | [Ripristinare un database SQL di Azure con PowerShell](sql-database-restore-deleted-database-powershell.md) | Ripristinare un database SQL di Azure (PowerShell). |
| 105 | [Ripristinare un database a un momento precedente, ripristinare un database eliminato o eseguire il ripristino in seguito a un'interruzione del data center](sql-database-troubleshoot-backup-and-restore.md) | Informazioni su come ripristinare un database cloud in seguito a errori e interruzioni tramite i backup e le repliche nel database SQL di Azure. |



## Migrazione

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 106 | [Esportare un database SQL Server in un file BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Database SQL di Microsoft Azure, migrazione del database, esportazione del database, esportazione del file BACPAC, sqlpackage |
| 107 | [Esportare un database SQL Server in un file BACPAC mediante SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Database SQL di Microsoft Azure, migrazione del database, esportazione del database, esportazione del file BACPAC, procedura guidata di esportazione dell’applicazione livello dati |
| 108 | [Importare nel database SQL da un file BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Database SQL di Microsoft Azure, migrazione del database, importazione del database, importazione del file BACPAC, sqlpackage |
| 109 | [Importare da BACPAC al database SQL tramite SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Database SQL di Microsoft Azure, distribuzione del database, migrazione del database, importazione del database, esportazione del database, migrazione guidata |
| 110 | [Eseguire la migrazione del database del server SQL al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Database SQL di Microsoft Azure, migrazione del database, creazione guidata database di Microsoft Azure |
| 111 | [Eseguire la migrazione del database di SQL Server al database SQL di Azure tramite la replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Database SQL di Microsoft Azure, migrazione del database, importazione del database, replica transazionale |
| 112 | [Determinare la compatibilità del database SQL utilizzando SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Database SQL di Microsoft Azure, migrazione del database, compatibilità database SQL, SqlPackage |
| 113 | [Usare SQL Server Management Studio per determinare la compatibilità del database SQL prima di eseguire la migrazione al database SQL di Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Database SQL di Microsoft Azure, migrazione del database, compatibilità database SQL, procedura guidata di esportazione dell’applicazione livello dati |
| 114 | [Usare la Migrazione guidata database SQL di Azure per risolvere i problemi di compatibilità dei database di SQL Server prima di eseguire la migrazione al database SQL di Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure |
| 115 | [Eseguire la migrazione di un database di SQL Server a un database SQL di Azure usando SQL Server Data Tools per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure, SSDT |
| 116 | [Risolvere i problemi di compatibilità del database SQL Server con SQL Server Management Studio prima di eseguire la migrazione al database SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure |
| 117 | [Archiviare un database SQL di Azure come file BACPAC tramite PowerShell](sql-database-export-powershell.md) | Archiviare un database SQL di Azure come file BACPAC tramite PowerShell |
| 118 | [Importare un file BACPAC per creare un nuovo database SQL di Azure tramite PowerShell](sql-database-import-powershell.md) | Importare un file BACPAC per creare un nuovo database SQL di Azure tramite PowerShell |
| 119 | [Caricare dati da CSV in Azure SQL Data Warehouse (file flat)](sql-database-load-from-csv-with-bcp.md) | Per dati di piccole dimensioni, usa bcp per importare dati nel database SQL di Azure. |
| 120 | [Spostare database tra server, tra sottoscrizioni e all'interno e all'esterno di Azure](sql-database-troubleshoot-moving-data.md) | Azioni rapide per copiare, spostare ed eseguire la migrazione di dati e database nel database SQL di Azure. |



## Spostare i dati in entrata e in uscita

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 121 | [Migrazione di un database SQL Server al database SQL nel cloud](sql-database-cloud-migrate.md) | Informazioni sulla migrazione del database SQL Server locale al database SQL di Azure nel cloud. Usare gli strumenti di migrazione del database per verificare la compatibilità prima della migrazione del database. |
| 122 | [Copiare un database SQL di Azure](sql-database-copy.md) | Creare una copia di un database SQL di Azure |
| 123 | [Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure](sql-database-export.md) | Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure |



## Sicurezza

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 124 | [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) | Informazioni su come connettersi database SQL con l'autenticazione di Azure Active Directory. |
| 125 | [Crittografia sempre attiva: proteggere i dati sensibili nel database SQL e archiviare le chiavi di crittografia nell'archivio certificati di Windows](sql-database-always-encrypted.md) | Proteggere i dati sensibili nel database SQL in pochi minuti. |
| 126 | [Crittografia sempre attiva: proteggere i dati sensibili nel database SQL e archiviare le chiavi di crittografia nell'insieme di credenziali delle chiavi di Azure](sql-database-always-encrypted-azure-key-vault.md) | Proteggere i dati sensibili nel database SQL in pochi minuti. |
| 127 | [Database SQL: supporto dei client di livello inferiore e modifiche all'endpoint IP per Controllo](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Informazioni sul supporto dei client di livello inferiore del database SQL e modifiche all'endpoint IP per Controllo |
| 128 | [Configurare le regole firewall a livello di server per il database SQL di Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md) | Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure. |
| 129 | [Configurare regole firewall a livello di server del database SQL di Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md) | Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure. |
| 130 | [Configurare le regole firewall a livello di server e di database per il database SQL di Azure tramite T-SQL](sql-database-configure-firewall-settings-tsql.md) | Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure. |
| 131 | [Introduzione alla Maschera dati dinamica del database SQL (portale di Azure)](sql-database-dynamic-data-masking-get-started.md) | Introduzione alla Maschera dati dinamica del database SQL nel portale di Azure |
| 132 | [Introduzione alla maschera dati dinamica del database SQL (portale di Azure classico)](sql-database-dynamic-data-masking-get-started-portal.md) | Introduzione al maschera dati dinamica del database SQL nel portale di Azure classico |
| 133 | [Configurare le regole del firewall per il database SQL di Azure - Panoramica](sql-database-firewall-configure.md) | Informazioni su come configurare un firewall del database SQL con regole del firewall a livello di server e a livello di database per la gestione dell'accesso. |
| 134 | [Esercitazione sul database SQL: creare account utente del database SQL per accedere a un database e gestirlo](sql-database-get-started-security.md) | Informazioni su come creare account utente per accedere a un database e gestirlo. |
| 135 | [Autenticazione e autorizzazione per database SQL: concessione dell'accesso](sql-database-manage-logins.md) | Informazioni sulla gestione della sicurezza del database SQL, in particolare la modalità di gestione dell'accesso al database e la sicurezza degli account di accesso tramite l'account delle entità di sicurezza a livello di server. |
| 136 | [Linee guida e limitazioni per la sicurezza per il database SQL di Azure](sql-database-security-guidelines.md) | Linee guida per il Database SQL di Microsoft Azure e le limitazioni relative alla sicurezza. |
| 137 | [Introduzione al rilevamento delle minacce nel database SQL](sql-database-threat-detection-get-started.md) | Introduzione al rilevamento delle minacce nel database SQL nel portale di Azure |
| 138 | [Come eseguire attività amministrative comuni come il ripristino della password amministratore nel database SQL di Azure](sql-database-troubleshoot-permissions.md) | Illustra come eseguire attività amministrative comuni nel database SQL di Azure. Ad esempio, reimpostazione delle password amministratore, concessione e rimozione dell'accesso. |



## Gestione del database

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 139 | [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md) | Introduzione al controllo del database SQL |
| 140 | [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md) | Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure. |
| 141 | [Copiare un database SQL di Azure tramite il portale di Azure](sql-database-copy-portal.md) | Creare una copia di un database SQL di Azure |
| 142 | [Gestire i database SQL usando Automazione di Azure](sql-database-manage-automation.md) | Informazioni su come è possibile usare il servizio Automazione di Azure per gestire database SQL su vasta scala. |
| 143 | [Panoramica: strumenti di gestione per database SQL](sql-database-manage-overview.md) | Confronta strumenti e le opzioni per la gestione di database SQL di Azure |
| 144 | [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md) | Informazioni su come rilevare e diagnosticare i problemi di prestazioni comuni utilizzando visualizzazioni a gestione dinamica per monitorare il Database SQL di Microsoft Azure. |
| 145 | [Protezione del Database SQL](sql-database-security.md) | In questo argomento vengono fornite informazioni sulla sicurezza database SQL Azure e SQL Server, incluse le differenze tra il cloud e SQL Server locale per quanto riguarda l'autenticazione, l'autorizzazione, la sicurezza della connessione, la crittografia e conformità. |



## Eventi estesi

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 146 | [Codice di destinazione del file evento per eventi estesi nel database SQL](sql-database-xevent-code-event-file.md) | Fornisce PowerShell e Transact-SQL per un esempio di codice in due fasi che illustra la destinazione del file evento in un evento esteso in Azure SQL Database. Archiviazione di Azure è una parte necessaria di questo scenario. |
| 147 | [Codice di destinazione del buffer circolare per eventi estesi nel database SQL](sql-database-xevent-code-ring-buffer.md) | Fornisce un esempio di codice Transact-SQL reso semplice e veloce tramite l'uso della destinazione del buffer circolare nel database SQL di Azure. |
| 148 | [Eventi estesi nel database SQL](sql-database-xevent-db-diff-from-svr.md) | Vengono descritti gli eventi estesi (XEvent) in Azure SQL Database e come le sessioni di eventi sono leggermente diverse da sessioni di eventi in Microsoft SQL Server. |



## Replica geografica

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 149 | [Avviare un failover pianificato o non pianificato per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-failover-portal.md) | Avviare un failover pianificato o non pianificato per il database SQL di Azure usando il portale di Azure |
| 150 | [Avviare un failover pianificato o non pianificato per il database SQL di Azure con PowerShell](sql-database-geo-replication-failover-powershell.md) | Avviare un failover pianificato o non pianificato per il database SQL di Azure usando PowerShell |
| 151 | [Avviare un failover pianificato o non pianificato per il database SQL di Azure con Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Avviare un failover pianificato o non pianificato per il database SQL di Azure usando Transact-SQL |
| 152 | [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md) | La replica geografica attiva consente di configurare 4 repliche del database in qualsiasi data center di Azure. |
| 153 | [Configurare la replica geografica per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-portal.md) | Configurare la replica geografica per il database SQL di Azure con il portale di Azure |
| 154 | [Configurare la replica geografica per il database SQL di Azure con PowerShell](sql-database-geo-replication-powershell.md) | Configurare la replica geografica attiva per il database SQL di Azure con PowerShell |
| 155 | [Come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) | In questo argomento viene trattato l'aspetto di gestione della sicurezza dopo il ripristino o il failover di un database. |
| 156 | [Configurare la replica geografica per il database SQL di Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurare la replica geografica per il database SQL di Azure con Transact-SQL |
| 157 | [Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica tramite il portale di Azure](sql-database-geo-restore-portal.md) | Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica (portale di Azure). |
| 158 | [Ripristinare un database SQL di Azure da un backup con ridondanza geografica tramite PowerShell](sql-database-geo-restore-powershell.md) | Ripristinare un database SQL di Azure in un nuovo server da un backup con ridondanza geografica |



## Aggiornamento

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 159 | [Miglioramento delle prestazioni delle query con il livello di compatibilità 130 nel database SQL di Azure](sql-database-compatibility-level-query-performance-130.md) | Passaggi e strumenti per determinare il livello di compatibilità ottimale per il database in Microsoft SQL Server o nel database SQL di Azure |
| 160 | [Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva del database SQL](sql-database-manage-application-rolling-upgrade.md) | Informazioni su come usare la replica geografica del database SQL di Azure per supportare gli aggiornamenti online dell'applicazione cloud. |
| 161 | [Eseguire l'aggiornamento a database SQL V12 di Azure tramite il portale di Azure](sql-database-upgrade-server-portal.md) | Viene spiegato come eseguire l'aggiornamento al database SQL V12 di Azure, come eseguire l'aggiornamento di database Web e Business e come aggiornare un server V11 che sta migrando i database direttamente in un pool di database elastici mediante il portale di Azure. |
| 162 | [Eseguire l'aggiornamento a database SQL V12 di Azure tramite PowerShell](sql-database-upgrade-server-powershell.md) | Viene spiegato come eseguire l'aggiornamento alla V12 del Database SQL di Azure, come eseguire l'aggiornamento di database Web e Business e come aggiornare un server V11 che sta migrando i database direttamente in un pool di database elastici con PowerShell. |
| 163 | [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md) | In questo argomento vengono descritte le operazioni di preparazione e le limitazioni relative all'aggiornamento del database SQL di Azure alla versione 12. |
| 164 | [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md) | Descrive i motivi per cui i sistemi aziendali che usano il database SQL di Azure nel cloud otterranno vantaggi dall'aggiornamento alla versione 12. |
| 165 | [Domande frequenti sul ritiro dell'edizione Web e Business](sql-database-web-business-sunset-faq.md) | Informazioni sul ritiro dei database Web e Business di SQL Azure e sulle caratteristiche e funzionalità dei nuovi livelli di servizio. |



## Strumenti

| &nbsp; | Titolo | Description |
| --: | :-- | :-- |
| 166 | [Gestire il database SQL di Azure con PowerShell](sql-database-command-line-tools.md) | Gestione del database SQL di Azure con PowerShell. |
| 167 | [Esercitazione sul database SQL: Connettere Excel a un database SQL di Azure e creare un report](sql-database-connect-excel.md) | Informazioni su come connettere Microsoft Excel al database SQL di Azure nel cloud. Importare i dati in Excel per creare report ed esplorare i dati. |
| 168 | [Creare un database SQL ed eseguire comuni attività di configurazione del database con i cmdlet di PowerShell](sql-database-get-started-powershell.md) | Informazioni su come creare un database SQL con PowerShell. Le comuni attività di configurazione del database possono essere gestite tramite i cmdlet di PowerShell. |
| 169 | [Introduzione all'anteprima di sincronizzazione dati di SQL Azure](sql-database-get-started-sql-data-sync.md) | Questa esercitazione fornisce informazioni per iniziare a usare anteprima di sincronizzazione dati di SQL Azure. |
| 170 | [Gestione del database SQL di Azure tramite SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Informazioni su come usare SQL Server Management Studio per gestire database e server di database SQL. |
| 171 | [Gestione dei database SQL di Azure tramite il portale di Azure](sql-database-manage-portal.md) | Informazioni su come usare il portale di Azure per gestire un database relazionale nel cloud tramite il portale di Azure. |
| 172 | [Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL con PowerShell](sql-database-scale-up-powershell.md) | La modifica del livello del servizio e del livello delle prestazioni di un database SQL di Azure mostra come scalare il database SQL verso l’alto o verso il basso con PowerShell. Modificare il livello di prezzi di un database SQL di Azure con PowerShell. |
| 173 | [Usare SQL Server Management Studio in Azure RemoteApp per connettersi al database SQL](sql-database-ssms-remoteapp.md) | Questa esercitazione spiega come imparare a usare SQL Server Management Studio in Azure RemoteApp per la sicurezza e le prestazioni durante la connessione al database SQL |



## riferimento

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 174 | [Raccolte di connessioni per database SQL e Server SQL](sql-database-libraries.md) | In questo argomento viene elencato il numero di versione minima per ogni driver che è possibile utilizzare nei programmi client per la connessione al database SQL di Azure o a Microsoft SQL Server. È disponibile un collegamento per le informazioni sulla versione relative ai driver rilasciati dalla community anziché da Microsoft. |
| 175 | [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md) | In questa pagina vengono descritti alcuni limiti di risorse comuni per il Database SQL Azure. |
| 176 | [Differenze di Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md) | Istruzioni Transact-SQL non completamente supportate nel Database SQL di Azure |



## Miscellaneous

| &nbsp; | Titolo | Descrizione |
| --: | :-- | :-- |
| 177 | [Copiare un database SQL di Azure con PowerShell](sql-database-copy-powershell.md) | Creare una copia di un database SQL di Azure tramite PowerShell |
| 178 | [Copiare un database SQL di Azure con Transact-SQL](sql-database-copy-transact-sql.md) | Creare una copia di un database SQL di Azure tramite Transact-SQL |
| 179 | [Limitazioni e linee guida generali per il database SQL di Azure](sql-database-general-limitations.md) | Questa pagina descrive alcune limitazioni generali per il database SQL di Azure, oltre agli ambiti dell'interoperabilità e del supporto. |
| 180 | [Indicazioni sui livelli di prezzo del database SQL](sql-database-service-tier-advisor.md) | Quando si modificano i livelli di prezzo nel portale di Azure, vengono forniti suggerimenti in merito ai livelli di prezzo che consentono di individuare il livello più adatto all'esecuzione di un carico di lavoro di un database SQL Azure esistente. Il livello dei prezzi descrive il livello di servizio e il livello di prestazioni di un database SQL |


& nbsp;

<hr/>

<a name="extras_append"></a>

## Dati aggiuntivi

<a name="extra_related_articles"></a>

### Articoli correlati da altri servizi di Azure

- [Eseguire il backup dell'app Servizio app di Azure in Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### Strumenti di documentazione

- [Aggiornamenti annunciati per il database SQL di Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Ricerca di tutti i tipi di documentazione di Microsoft Azure, con l'uso di filtri](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### Grafici dei percorsi di apprendimento

- [Grafici del percorso di apprendimento per tutti i servizi Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Percorso di apprendimento: informazioni sul database SQL di Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Percorso di apprendimento: funzionalità e strumenti del database elastico di SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->

<!---HONumber=AcomDC_0824_2016-->