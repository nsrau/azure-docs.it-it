---
title: Autenticazione
description: Informazioni su come eseguire l'autenticazione in Azure sinapsi Analytics usando Azure Active Directory (Azure AD) o l'autenticazione di SQL Server.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ed8dd902ac490b4e6a0f172029bf3ffa1d44acee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81251844"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Eseguire l'autenticazione ad Azure sinapsi Analytics

Informazioni su come eseguire l'autenticazione a SQL Analytics in sinapsi di Azure usando Azure Active Directory (AAD) o l'autenticazione di SQL Server.

Per connettersi a un pool SQL, è necessario passare le credenziali di sicurezza per scopi di autenticazione. Al momento di stabilire una connessione, alcune impostazioni di connessione sono configurate come parte della creazione della sessione di query.  

Per ulteriori informazioni sulla sicurezza e su come abilitare le connessioni al data warehouse, vedere [la pagina relativa alla protezione di un database](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Autenticazione SQL

Per connettersi al pool SQL, è necessario fornire le seguenti informazioni:

* Nome del server completo
* Specificare l'autenticazione di SQL
* Username
* Password
* Database predefinito (facoltativo)

Per impostazione predefinita, la connessione si connette al database *Master* e non al database utente. Per connettersi al database utente è possibile scegliere di effettuare una delle seguenti operazioni:

* Specificare il database predefinito per la registrazione del server con Esplora oggetti di SQL Server in SSDT, SSMS o nella stringa di connessione dell'applicazione. Ad esempio, includere il parametro InitialCatalog per una connessione ODBC.
* Selezionare il database utente prima di creare una sessione in SSDT.

> [!NOTE]
> L'istruzione Transact-SQL **USE MyDatabase;** non è supportata per la modifica del database per una connessione. Per informazioni sulla connessione a un pool SQL con SSDT, vedere l'articolo relativo alla [query con Visual Studio](sql-data-warehouse-query-visual-studio.md) .

## <a name="azure-active-directory-aad-authentication"></a>Autenticazione di Azure Active Directory (AAD)

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) autenticazione è un meccanismo di connessione al pool SQL utilizzando le identità in Azure Active Directory (Azure ad). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft. Central ID Management consente di gestire gli utenti di sinapsi di Azure in un'unica posizione e semplifica la gestione delle autorizzazioni.

### <a name="benefits"></a>Vantaggi

I vantaggi di Azure Active Directory includono i seguenti:

* Offre un'alternativa all'autenticazione di SQL Server.
* Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
* Consente la rotazione delle password in un'unica posizione.
* Gestire le autorizzazioni del database usando gruppi esterni (Azure AD).
* Consente di eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
* Usa gli utenti di database indipendente per autenticare le identità a livello di database.
* Supporta l'autenticazione basata su token per le applicazioni che si connettono al pool SQL.
* Supporta la funzionalità di autenticazione a più fattori tramite l'autenticazione Active Directory universale per diversi strumenti, tra cui [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> Azure Active Directory è ancora relativamente nuovo e presenta alcune limitazioni. Per assicurarsi che Azure Active Directory sia ideale per l'ambiente in uso, vedere [le funzionalità e le limitazioni di Azure AD](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), in particolare le considerazioni aggiuntive.

### <a name="configuration-steps"></a>Passaggi di configurazione

Seguire questa procedura per configurare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure Active Directory
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure
3. Creare un amministratore Azure Active Directory per la sinapsi di Azure
4. Configurare i computer client
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD
6. Connettersi al pool SQL usando Azure AD identità

Gli utenti di Azure Active Directory non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Informazioni dettagliate

* I passaggi per configurare e usare l'autenticazione Azure Active Directory sono quasi identici per il database SQL di Azure e l'analisi SQL in sinapsi di Azure. Seguire i passaggi dettagliati nell'argomento [connessione al database SQL o al pool SQL utilizzando l'autenticazione Azure Active Directory](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Creare ruoli di database personalizzati e aggiungere utenti ai ruoli. Concedere quindi autorizzazioni granulari ai ruoli. Per altre informazioni, vedere [Introduzione alle autorizzazioni del motore di database](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a eseguire query con Visual Studio e altre applicazioni, vedere [eseguire query con Visual Studio](sql-data-warehouse-query-visual-studio.md).
