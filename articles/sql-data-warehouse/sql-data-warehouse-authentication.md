<properties
   pageTitle="Autenticazione in Azure SQL Data Warehouse | Microsoft Azure"
   description="Autenticazione di Azure Active Directory (AAD) e SQL Server in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/17/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# Autenticazione in Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-connect-overview.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Driver](sql-data-warehouse-connection-strings.md)

Per connettersi a SQL Data Warehouse sarà necessario passare le credenziali di sicurezza per scopi di autenticazione. Al momento di stabilire una connessione si potrà notare che alcune impostazioni di connessione sono configurate come parte della creazione della sessione di query.

Per altre informazioni sulla sicurezza e sull'attivazione di connessioni al data warehouse, vedere anche l'articolo [Proteggere un database in SQL Data Warehouse][].

## Autenticazione in SQL
Per connettersi a SQL Data Warehouse è necessario fornire le informazioni seguenti:

- Nome del server completo
- Specificare l'autenticazione di SQL
- Nome utente
- Password
- Database predefinito (facoltativo)

È importante notare che gli utenti devono essere autenticati utilizzando l'autenticazione di SQL. Attualmente l'autenticazione attendibile non è supportata.

Per impostazione predefinita la connessione si connetterà al database master e non al database utente. Per connettersi al database utente è possibile scegliere di effettuare una delle seguenti operazioni:

1. Specificare il database predefinito per la registrazione del server con Esplora oggetti di SQL Server in SSDT o nella stringa di connessione dell’applicazione. Ad esempio, includendo il parametro InitialCatalog per una connessione ODBC.
2. Selezionare innanzitutto il database utente prima di creare una sessione in SSDT.

> [AZURE.NOTE] Per informazioni sulla connessione a SQL Data Warehouse con SSDT, consultare nuovamente l’articolo su come [Eseguire query in Azure SQL Data Warehouse (Visual Studio)][].

Anche in questo caso è importante notare che l'istruzione Transact-SQL **USE <your DB>** non è supportata per la modifica del database per una connessione


## Autenticazione di Azure Active Directory (AAD)

L'autenticazione di Azure Active Directory è un meccanismo di connessione a SQL Data Warehouse di Microsoft Azure tramite le identità di Azure Active Directory (Azure AD). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti di SQL Data Warehouse in un'unica posizione e semplifica la gestione delle autorizzazioni.

### Vantaggi

Ecco alcuni vantaggi:

- Offre un'alternativa all'autenticazione di SQL Server.
- Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
- Consente la rotazione delle password in un'unica posizione.
- I clienti possono gestire le autorizzazioni del database tramite gruppi (AAD) esterni.
- Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
- L'autenticazione di Azure Active Directory usa gli utenti di database indipendente per autenticare le identità a livello di database.
- Azure Active Directory supporta l'autenticazione basata su token per le applicazioni che si connettono a SQL Data Warehouse.

> [AZURE.IMPORTANT] L'autenticazione di Azure Active Directory è una funzionalità in anteprima ed è soggetta alle condizioni per l'anteprima del contratto di licenza (ad esempio, il Contratto Enterprise, il Contratto di Microsoft Azure o il Contratto di Sottoscrizione Microsoft Online), nonché alle [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### Procedura di configurazione

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure Active Directory
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure
3. Creare un amministratore di Azure Active Directory per Azure SQL Data Warehouse.
4. Configurare i computer client
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD
6. Connettersi al data warehouse usando le identità di Azure AD

La differenza principale tra l'uso dell'autenticazione di Azure Active Directory con il database SQL di Azure e Azure SQL Data Warehouse consiste nella necessità di usare SQL Server Data Tools invece di SQL Server Management Studio per connettersi a SQL Data Warehouse. SQL Data Warehouse richiede almeno la versione di aprile 2016 (versione 14.0.60311.1) di SQL Server Data Tools per Visual Studio 2015. Gli utenti di Azure Active Directory non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### Informazioni dettagliate
- Completare la procedura dettagliata. La procedura per configurare e usare l'autenticazione di Azure Active Directory è quasi uguale per il database SQL di Azure e Azure SQL Data Warehouse. Seguire la procedura riportata nell'argomento [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
- Creare ruoli di database personalizzati e aggiungere utenti ai ruoli. Concedere quindi autorizzazioni granulari ai ruoli. Per altre informazioni, vedere [Introduzione alle autorizzazioni del motore di database](https://msdn.microsoft.com/library/mt667986.aspx).

## Passaggi successivi

Per iniziare a eseguire query sul data warehouse con Visual Studio e altre applicazioni, vedere [Eseguire query in Azure SQL Data Warehouse (Visual Studio)][].

<!-- Article references -->
[Proteggere un database in SQL Data Warehouse]: sql-data-warehouse-overview-manage-security.md
[Eseguire query in Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md

<!---HONumber=AcomDC_0622_2016-->