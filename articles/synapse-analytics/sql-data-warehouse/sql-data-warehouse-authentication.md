---
title: Autenticazione per il pool SQL dedicato (in precedenza SQL DW)
description: Informazioni su come eseguire l'autenticazione in un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics usando Azure Active Directory (Azure AD) o l'autenticazione di SQL Server.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: b2b5ca024046c5bc46fff756c55688d3ff0cfea1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451959"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Eseguire l'autenticazione a un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics

Informazioni su come eseguire l'autenticazione in un pool SQL dedicato (in precedenza SQL DW) in sinapsi di Azure usando Azure Active Directory (Azure AD) o l'autenticazione di SQL Server.

Per connettersi a un pool SQL dedicato (in precedenza SQL DW), è necessario passare le credenziali di sicurezza per scopi di autenticazione. Al momento di stabilire una connessione, alcune impostazioni di connessione sono configurate come parte della creazione della sessione di query.  

Per ulteriori informazioni sulla sicurezza e su come abilitare le connessioni al pool SQL dedicato (in precedenza SQL DW), vedere [la pagina relativa alla protezione di un database](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Autenticazione SQL

Per connettersi a un pool SQL dedicato (in precedenza SQL DW), è necessario fornire le seguenti informazioni:

* Nome del server completo
* Specificare l'autenticazione di SQL
* Username
* Password
* Database predefinito (facoltativo)

Per impostazione predefinita, la connessione viene stabilita con il database *master* e non con il database utente. Per connettersi al database utente è possibile scegliere di effettuare una delle seguenti operazioni:

* Specificare il database predefinito per la registrazione del server con Esplora oggetti di SQL Server in SSDT, SSMS o nella stringa di connessione dell'applicazione. Ad esempio, includere il parametro InitialCatalog per una connessione ODBC.
* Selezionare il database utente prima di creare una sessione in SSDT.

> [!NOTE]
> L'istruzione Transact-SQL **USE MyDatabase;** non è supportata per la modifica del database per una connessione. Per informazioni sulla connessione a un pool SQL con SSDT, vedere l'articolo [Eseguire query con Visual Studio](sql-data-warehouse-query-visual-studio.md).

## <a name="azure-active-directory-authentication"></a>Autenticazione di Azure Active Directory

L'autenticazione di [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) è un meccanismo di connessione a un pool SQL tramite identità in Azure Active Directory (Azure AD). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft. Central ID Management consente di gestire un pool SQL dedicato (in precedenza SQL DW) e semplifica la gestione delle autorizzazioni.

### <a name="benefits"></a>Vantaggi

I vantaggi di Azure Active Directory includono i seguenti:

* Offre un'alternativa all'autenticazione di SQL Server.
* Contribuisce ad arrestare la proliferazione delle identità utente nei server.
* Consente la rotazione delle password in un'unica posizione.
* Consente di gestire le autorizzazioni del database tramite gruppi di Azure AD esterni.
* Consente di eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
* Usa gli utenti di database indipendente per autenticare le identità a livello di database.
* Supporta l'autenticazione basata su token per le applicazioni che si connettono al pool SQL.
* Supporta l'autenticazione a più fattori tramite Autenticazione universale di Active Directory per vari strumenti, tra cui [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Azure Active Directory è ancora relativamente nuovo e presenta alcune limitazioni. Per assicurarsi che Azure Active Directory sia ideale per l'ambiente di riferimento, vedere [le funzionalità e le limitazioni di Azure AD](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), in particolare le Considerazioni aggiuntive.

### <a name="configuration-steps"></a>Passaggi di configurazione

Seguire questa procedura per configurare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure Active Directory
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure
3. Creare un amministratore di Azure Active Directory per Azure Synapse
4. Configurare i computer client
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD
6. Connettersi al pool SQL usando le identità di Azure AD

Gli utenti di Azure Active Directory non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Informazioni dettagliate

* La procedura per configurare e usare l'autenticazione di Azure Active Directory è quasi identica per Database SQL di Azure e Synapse SQL in Azure Synapse. Seguire la procedura dettagliata riportata nell'argomento [Connessione al database SQL o al pool SQL con l'autenticazione di Azure Active Directory](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Creare ruoli di database personalizzati e aggiungere utenti ai ruoli. Concedere quindi autorizzazioni granulari ai ruoli. Per altre informazioni, vedere [Introduzione alle autorizzazioni del motore di database](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a eseguire query con Visual Studio e altre applicazioni, vedere [Eseguire query con Visual Studio](sql-data-warehouse-query-visual-studio.md).
