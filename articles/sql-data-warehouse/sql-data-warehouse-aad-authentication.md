<properties
   pageTitle="Connettersi ad Azure SQL Data Warehouse con l'autenticazione di Azure Active Directory | Microsoft Azure"
   description="Informazioni su come connettersi ad Azure SQL Data Warehouse con l'autenticazione di Azure Active Directory."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/11/2016"
   ms.author="rick.byham@microsoft.com"/>

# Connessione a SQL Data Warehouse con l'autenticazione di Azure Active Directory


L'autenticazione di Azure Active Directory è un meccanismo di connessione a SQL Data Warehouse di Microsoft Azure tramite le identità di Azure Active Directory (Azure AD). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti di SQL Data Warehouse in un'unica posizione e semplifica la gestione delle autorizzazioni. Ecco alcuni vantaggi:

- Offre un'alternativa all'autenticazione di SQL Server.
- Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
- Consente la rotazione delle password in un'unica posizione.
- I clienti possono gestire le autorizzazioni del database tramite gruppi (AAD) esterni.
- Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
- L'autenticazione di Azure Active Directory usa gli utenti di database indipendente per autenticare le identità a livello di database.
- Azure Active Directory supporta l'autenticazione basata su token per le applicazioni che si connettono a SQL Data Warehouse.

> [AZURE.IMPORTANT] L'autenticazione di Azure Active Directory è una funzionalità in anteprima ed è soggetta alle condizioni per l'anteprima del contratto di licenza (ad esempio, il Contratto Enterprise, il Contratto di Microsoft Azure o il Contratto di Sottoscrizione Microsoft Online), nonché alle [Condizioni Supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure Active Directory
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure
3. Creare un amministratore di Azure Active Directory per Azure SQL Data Warehouse.
4. Configurare i computer client
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD
6. Connettersi al data warehouse usando le identità di Azure AD

La procedura per configurare e usare l'autenticazione di Azure Active Directory è quasi uguale per il database SQL di Azure e Azure SQL Data Warehouse. Seguire la procedure riportata nell'argomento [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](../sql-database/sql-database-aad-authentication.md).

La differenza principale tra l'uso dell'autenticazione di Azure Active Directory con il database SQL di Azure e Azure SQL Data Warehouse consiste nella necessità di usare SQL Server Data Tools invece di SQL Server Management Studio per connettersi a SQL Data Warehouse. SQL Data Warehouse richiede almeno la versione di aprile 2016 (versione 14.0.60311.1) di SQL Server Data Tools per Visual Studio 2015. Gli utenti di Azure Active Directory non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx).

<!---HONumber=AcomDC_0518_2016-->