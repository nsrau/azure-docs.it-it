---
title: Esplorare le esercitazioni sul database SQL di Azure | Microsoft Docs
description: Informazioni sulle caratteristiche e sulle funzionalità del database SQL
keywords: ''
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 08/24/2016
ms.author: carlrab

---
# Esplorare le esercitazioni sul database SQL di Azure
I collegamenti seguenti indirizzano a una panoramica delle aree di funzionalità elencate e a una semplice esercitazione introduttiva per ogni area. Per esercitazioni introduttive nell'ambito della soluzione che illustrano l'uso del database SQL in una soluzione completa basata su scenari reali, vedere [Azure SQL Database Solution Quick Starts](sql-database-solution-quick-starts.md) (Procedure di avvio rapido per la soluzione di database SQL di Azure).

## Utilizzare SQL Server Management Studio
Le esercitazioni seguenti spiegano come usare SQL Server Management Studio per gestire il database SQL di Azure ed eseguire query sul database.

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Connettersi al database SQL di Azure con un accesso entità a livello di server](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login) |Questa esercitazione mostra come connettersi al database SQL di Azure con un account di accesso entità a livello del server. |
| [Connettersi a un database SQL di Azure come utente](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) |Questa esercitazione mostra come connettersi al database SQL di Azure con un account utente a livello del database. |
|  | |

## Pool elastici
Le esercitazioni seguenti mostrano come usare i [pool elastici](sql-database-elastic-pool.md) per gestire gli obiettivi di prestazioni per più database con modelli di utilizzo estremamente variabili e imprevedibili.

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Creare un pool elastico](sql-database-elastic-pool-create-portal.md) |Questa esercitazione mostra come creare un pool scalabile di database SQL di Azure. |
| [Monitorare un database elastico](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) |Questa esercitazione mostra come monitorare un singolo database elastico per individuare potenziali problemi. |
| [Aggiungere un avviso a una risorsa di pool](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) |Questa esercitazione mostra come aggiungere regole a risorse che inviano messaggi di posta elettronica a persone oppure stringhe di avviso a endpoint di URL quando la risorsa raggiunge la soglia di utilizzo impostata. |
| [Spostare un database in un pool elastico](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) |Questa esercitazione mostra come spostare un database in un pool elastico. |
| [Spostare un database da un pool elastico](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) |Questa esercitazione mostra come spostare un database da un pool elastico. |
| [Modificare le impostazioni delle prestazioni di un pool](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) |Questa esercitazione mostra come regolare i limiti delle prestazioni e dello spazio di archiviazione per un pool. |
|  | |

## Processi di database elastici
Le esercitazioni seguenti mostrano come usare i [processi di database elastico](sql-database-elastic-jobs-overview.md).

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Iniziare a utilizzare gli strumenti di database elastici](sql-database-elastic-scale-get-started.md) |Questa esercitazione mostra come usare le funzionalità degli strumenti dei database elastici tramite una semplice applicazione partizionata. |
| [Introduzione ai processi elastici del database SQL di Azure](sql-database-elastic-jobs-getting-started.md) |Questa esercitazione mostra come creare e gestire processi che gestiscono un gruppo di database correlati. |
|  | |

## Query elastiche
Le esercitazioni seguenti mostrano come eseguire le [query elastiche](sql-database-elastic-query-overview.md).

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Esecuzione di query su un database con partizionamento orizzontale](sql-database-elastic-query-getting-started.md) |Questa esercitazione mostra come creare report da tutti i database in un database con partizionamento orizzontale tramite la funzionalità di [query elastica](sql-database-elastic-query-overview.md) |
| [Esecuzione di query su database con partizionamento verticale](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) |Questa esercitazione mostra come creare report da tutti i database in un database con partizionamento verticale tramite la funzionalità di [query elastica](sql-database-elastic-query-overview.md) |
| [Eseguire la migrazione di un database esistente per aumentare il numero di istanze](sql-database-elastic-convert-to-use-elastic-tools.md) |Questa esercitazione mostra come eseguire il partizionamento del database SQL di Azure per aumentare il numero di istanze. |
|  | |

## Ottimizzazione delle prestazioni
Le esercitazioni seguenti mostrano come ottimizzare le [prestazioni di singoli database](sql-database-performance-guidance.md). Per ottimizzare le prestazioni di più database, vedere [Pool elastici](#elastic-pools).

| Esercitazione | Description |
| --- | --- | --- |
| [Modificare il livello di servizio e il livello delle prestazioni del database](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) |Questa esercitazione mostra come aumentare o ridurre le prestazioni di un database SQL di Azure usando i livelli di servizio. |
| [Informazioni dettagliate prestazioni query di SQL Database Advisor](sql-database-performance.md#performance-overview) |Questa esercitazione mostra come aprire e usare la funzionalità Informazioni dettagliate prestazioni query di SQL Database Advisor. |
| [Raccomandazioni sulle prestazioni di SQL Database Advisor](sql-database-advisor.md#viewing-recommendations) |Questa esercitazione spiega come visualizzare e applicare le raccomandazioni sulle prestazioni di SQL Database Advisor. |
| [Esaminare le query principali a livello di utilizzo di CPU](sql-database-query-performance.md#review-top-cpu-consuming-queries) |Questa esercitazione mostra come aprire e usare la funzionalità Informazioni dettagliate prestazioni query di SQL Database Advisor per esaminare le query con il maggior utilizzo di CPU. |
| [Visualizzazione dei dettagli delle singole query](sql-database-query-performance.md#viewing-individual-query-details) |Questa esercitazione mostra come aprire e usare la funzionalità Informazioni dettagliate prestazioni query di SQL Database Advisor per esaminare i dettagli delle prestazioni delle singole query. |
|  | |

## Migrazione e archiviazione del database SQL
Le esercitazioni seguenti mostrano come [eseguire la migrazione di un database SQL Server esistente nel database SQL di Azure](sql-database-cloud-migrate.md).

| Esercitazione | Description |
| --- | --- | --- |
| [Rilevamento di problemi di compatibilità con SQL Server Data Tools per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |Questa esercitazione mostra come usare SQL Server Data Tools per Visual Studio per determinare la compatibilità del database SQL di Azure. |
| [Correzione di problemi di compatibilità con SQL Server Data Tools per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) |Questa esercitazione mostra come usare SQL Server Data Tools per Visual Studio per correggere i problemi di compatibilità del database SQL di Azure. |
| [Determinare la compatibilità del database SQL utilizzando SqlPackage.exe](ql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) |Questa esercitazione mostra come usare l'utilità della riga di comando SQLPackage.exe per determinare la compatibilità del database SQL di Azure. |
| [Determinare la compatibilità del database SQL utilizzando SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |Questa esercitazione mostra come usare SQL Server Management Studio per determinare la compatibilità del database SQL di Azure. |
| [Eseguire la migrazione del database del server SQL al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) |Questa esercitazione mostra come eseguire la migrazione di un database SQL Server compatibile nel database SQL di Azure tramite la distribuzione guidata del database nel database di Microsoft Azure in SQL Server Management Studio. |
| [Esportare un database SQL Server in un file BACPAC mediante SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) |Questa esercitazione mostra come esportare un database SQL Server compatibile in un file BACPAC tramite l'esportazione guidata dell'applicazione del livello dati in SQL Server Management Studio. |
| [Esportare un database SQL Server in un file BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) |Questa esercitazione mostra come esportare un database SQL Server compatibile in un file BACPAC con l'utilità della riga di comando SQLPackage.exe. |
| [Importare da BACPAC al database SQL tramite SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) |Questa esercitazione mostra come importare un database nel database SQL di Azure da un file BACPAC tramite l'esportazione guidata dell'applicazione del livello dati in SQL Server Management Studio. |
| [Importare un file BACPAC nel database SQL di Azure con SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) |Questa esercitazione mostra come importare un database nel database SQL di Azure da un file BACPAC con l'utilità della riga di comando SQLPackage. |
| [Importare un file BACPAC nel database SQL di Azure tramite il portale di Azure](sql-database-import.md) |Questa esercitazione mostra come importare un database nel database SQL di Azure da un file BACPAC archiviato in un BLOB di Azure tramite il portale di Azure. |
| [Importare un file BACPAC in un database SQL di Azure con PowerShell](sql-database-import-powershell.md) |Questa esercitazione mostra come importare un database nel database SQL di Azure da un file BACPAC con PowerShell. |
| [Archiviare un database SQL di Azure tramite il portale di Azure](sql-database-export.md#export-your-database) |Questa esercitazione mostra come archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure. |
| [Archiviare un database SQL di Azure con PowerShell](sql-database-export-powershell.md) |Questa esercitazione mostra come archiviare un database SQL di Azure in un file BACPAC usando PowerShell. |
| [Copiare un database SQL di Azure tramite il portale di Azure](sql-database-copy.md#copy-your-sql-database) |Questa esercitazione mostra come copiare un database SQL di Azure tramite il portale di Azure. |
| [Copiare un database SQL di Azure con PowerShell](sql-database-copy-powershell.md#copy-your-sql-database) |Questa esercitazione mostra come copiare un database SQL di Azure con PowerShell. |
| [Copiare un database SQL di Azure con Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) |Questa esercitazione mostra come copiare un database SQL di Azure con Transact-SQL. |
|  | |

## Sviluppare
Le esercitazioni seguenti illustrano i concetti relativi allo [sviluppo di database SQL](sql-database-develop-overview.md) e all'uso delle [librerie di connettività](sql-database-libraries.md).

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Connettersi al database SQL tramite .NET (C#)](sql-database-develop-dotnet-simple.md) |Questa esercitazione mostra come connettersi a un database SQL di Azure tramite C#. |
| [Connettersi al database SQL tramite Java](sql-database-develop-java-simple.md) |Questa esercitazione mostra come connettersi a un database SQL di Azure tramite Java. |
| [Connettersi al Database SQL tramite Node.js](sql-database-develop-nodejs-simple.md) |Questa esercitazione mostra come connettersi a un database SQL di Azure tramite Node.js. |
| [Connettersi al database SQL tramite PHP](sql-database-develop-php-simple.md) |Questa esercitazione mostra come connettersi a un database SQL di Azure tramite PHP. |
| [Connettersi al database SQL tramite Python](sql-database-develop-python-simple.md) |Questa esercitazione mostra come connettersi a un database SQL di Azure tramite Python. |
| [Connettersi al database SQL tramite Ruby](sql-database-develop-ruby-simple.md) |Questa esercitazione mostra come connettersi a un database SQL di Azure tramite Ruby. |
|  | |

## Accesso al database
Le esercitazioni seguenti mostrano come [creare e gestire account di accesso e utenti](sql-database-manage-logins.md).

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Creare una regola firewall a livello del server del database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md) |Questa esercitazione mostra come configurare una regola firewall a livello del server del database SQL tramite il portale di Azure. |
| [Creare una regola firewall a livello del database tramite Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) |Questa esercitazione mostra come creare una regola firewall a livello del database tramite Transact-SQL. |
| [Gestire regole firewall a livello del server tramite Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) |Questa esercitazione mostra come gestire un firewall a livello del server del database SQL di Azure tramite Transact-SQL. |
| [Gestire regole firewall a livello del server tramite PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) |Questa esercitazione mostra come gestire un firewall a livello del server del database SQL di Azure tramite PowerShell. |
| [Gestire regole firewall a livello del server tramite l'API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) |Questa esercitazione mostra come gestire un firewall a livello del server del database SQL di Azure tramite l'API REST. |
| [Connettersi al database SQL di Azure con un accesso entità a livello di server](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login) |Questa esercitazione mostra come connettersi al database SQL di Azure con un account di accesso entità a livello del server. |
| [Concessione dell'accesso al database a un account di accesso](sql-database-manage-logins.md#granting-database-access-to-a-login() |Questa esercitazione mostra come concedere l'accesso al database a un account di accesso a livello del server. |
| [Creare un nuovo utente del database con SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) |Questa esercitazione mostra come creare un nuovo utente del database in un database esistente tramite SSMS. |
| [Concedere autorizzazioni db\_owner a un nuovo utente del database](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) |Questa esercitazione mostra come concedere autorizzazioni db\_owner a un utente del database esistente. |
| [Connettersi a un database SQL di Azure come utente](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) |Questa esercitazione mostra come connettersi al database SQL di Azure con un account utente a livello del database. |
|  | |

## Sicurezza dei dati
Le esercitazioni seguenti mostrano come [proteggere i dati del database SQL di Azure](sql-database-security.md).

| Esercitazione | Description |
| --- | --- | --- |
| [Abilitare il rilevamento delle minacce per il database tramite il portale di Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) |Questa esercitazione mostra come configurare il rilevamento delle minacce per il database tramite il portale di Azure. |
| [Proteggere i dati sensibili tramite la procedura guidata Crittografia sempre attiva ](sql-database-always-encrypted-azure-key-vault.md) |Questa esercitazione mostra come usare la procedura guidata Crittografia sempre attiva per proteggere i dati sensibili in un database SQL di Azure. |
| [Proteggere i dati sensibili tramite Transparent Data Encryption](https://msdn.microsoft.com/library/dn948096.aspx) |Questa esercitazione mostra come usare la tecnologia Transparent Data Encryption per proteggere i dati sensibili. |
| [Crittografare una colonna di dati](https://msdn.microsoft.com/library/ms179331.aspx) |Questa esercitazione mostra come crittografare una colonna di dati tramite Transact-SQL. |
| [Configurare la maschera dati dinamica](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) |Questa esercitazione mostra come configurare la maschera dati dinamica per il database SQL di Azure. |
|  | |

## Continuità aziendale e aumento delle istanze di query
Le esercitazioni seguenti mostrano come usare le funzionalità di [ripristino geografico e replica geografica attiva](sql-database-business-continuity.md) per eseguire operazioni di ripristino a seguito di errori, per la continuità aziendale e per la scalabilità orizzontale delle query.

| Esercitazione | Description |
| --- | --- | --- |
| [Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure](sql-database-point-in-time-restore-portal.md) |Questa esercitazione mostra come ripristinare un database a un momento precedente tramite il portale di Azure. |
| [Ripristinare un database SQL di Azure a un momento precedente con PowerShell](sql-database-point-in-time-restore-powershell.md) |Questa esercitazione mostra come ripristinare un database a un momento precedente tramite PowerShell |
| [Ripristinare un database SQL di Azure con il portale di Azure](sql-database-restore-deleted-database-portal.md) |Questa esercitazione mostra come ripristinare un database eliminato tramite il portale di Azure. |
| [Ripristinare un database SQL di Azure eliminato tramite PowerShell](sql-database-restore-deleted-database-powershell.md) |Questa esercitazione mostra come ripristinare un database eliminato tramite PowerShell. |
| [Configurare la replica geografica per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-portal.md) |Questa esercitazione mostra come configurare la replica geografica attiva usando il portale di Azure. |
| [Configurare la replica geografica per il database SQL di Azure con PowerShell](sql-database-geo-replication-powershell.md) |Questa esercitazione mostra come configurare la replica geografica attiva usando PowerShell. |
| [Configurare la replica geografica per il database SQL di Azure con Transact-SQL](sql-database-geo-replication-transact-sql.md) |Questa esercitazione mostra come configurare la replica geografica attiva usando Transact-SQL. |
| [Avviare un failover pianificato o non pianificato per il database SQL di Azure usando il portale di Azure](sql-database-geo-replication-failover-portal.md) |Questa esercitazione mostra come eseguire il failover in una replica secondaria con replica geografica tramite il portale di Azure. |
| [Avviare un failover pianificato o non pianificato per il database SQL di Azure usando PowerShell](sql-database-geo-replication-failover-powershell.md) |Questa esercitazione mostra come eseguire il failover in una replica secondaria con replica geografica con PowerShell. |
| [Avviare un failover pianificato o non pianificato per il database SQL di Azure usando Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) |Questa esercitazione mostra come eseguire il failover in una replica secondaria con replica geografica con Transact-SQL. |
|  | |

## Sincronizzazione dei dati
Questa esercitazione illustra i concetti relativi alla [sincronizzazione dei dati](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Esercitazione | Descrizione |
| --- | --- | --- |
| [Introduzione all'anteprima di sincronizzazione dati di SQL Azure](sql-database-get-started-sql-data-sync.md) |In questa esercitazione vengono fornite le nozioni di base della sincronizzazione dati SQL Azure tramite il portale di Azure classico. |
|  | |

## Passaggi successivi
[Esplorare le procedure di avvio rapido per la soluzione di database SQL di Azure](sql-database-solution-quick-starts.md)

<!---HONumber=AcomDC_0824_2016-->