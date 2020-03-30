---
title: Authentication
description: Informazioni su come eseguire l'autenticazione ad Azure Synapse Analytics usando Azure Active Directory (AAD) o l'autenticazione di SQL Server.Learn how to authenticate to Azure Synapse Analytics by using Azure Active Directory (AAD) or SQL Server authentication.
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
ms.openlocfilehash: c998e3789a29d3cfeaf18a583913871f7edc5af1
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350717"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Eseguire l'autenticazione ad Azure Synapse AnalyticsAuthenticate to Azure Synapse Analytics
Informazioni su come eseguire l'autenticazione a SQL Analytics in Azure Synapse usando Azure Active Directory (AAD) o l'autenticazione di SQL Server.Learn how to authenticate to SQL Analytics in Azure Synapse by using Azure Active Directory (AAD) or SQL Server authentication.

Per connettersi a un pool SQL, è necessario passare le credenziali di sicurezza per scopi di autenticazione. Al momento di stabilire una connessione, alcune impostazioni di connessione sono configurate come parte della creazione della sessione di query.  

Per ulteriori informazioni sulla sicurezza e su come abilitare le connessioni al data warehouse, vedere la documentazione relativa alla [protezione di un database.](sql-data-warehouse-overview-manage-security.md)

## <a name="sql-authentication"></a>Autenticazione SQL
Per connettersi al pool SQL, è necessario fornire le informazioni seguenti:To connect to SQL pool, you must provide the following information:

* Nome del server completo
* Specificare l'autenticazione di SQL
* Username
* Password
* Database predefinito (facoltativo)

Per impostazione predefinita, la connessione si connette al database *master* e non al database utente. Per connettersi al database utente è possibile scegliere di effettuare una delle seguenti operazioni:

* Specificare il database predefinito per la registrazione del server con Esplora oggetti di SQL Server in SSDT, SSMS o nella stringa di connessione dell'applicazione. Ad esempio, includere il parametro InitialCatalog per una connessione ODBC.
* Selezionare il database utente prima di creare una sessione in SSDT.

> [!NOTE]
> L'istruzione Transact-SQL **USE MyDatabase;** non è supportata per la modifica del database per una connessione. Per indicazioni sulla connessione a un pool SQL con SSDT, vedere l'articolo [Query with Visual Studio.For](sql-data-warehouse-query-visual-studio.md) guidance connecting to a SQL pool with SSDT, refer to the Query with Visual Studio article.
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticazione di Azure Active Directory (AAD)
L'autenticazione di [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) è un meccanismo di connessione al pool SQL tramite identità in Azure Active Directory (Azure AD). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft. La gestione degli ID centralizzati offre un'unica posizione in cui gestire gli utenti di Azure Synapse e semplifica la gestione delle autorizzazioni. 

### <a name="benefits"></a>Vantaggi
I vantaggi di Azure Active Directory includono i seguenti:

* Offre un'alternativa all'autenticazione di SQL Server.
* Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
* Consente la rotazione delle password in un'unica posizione.
* Consente di gestire le autorizzazioni del database tramite gruppi (AAD) esterni.
* Consente di eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
* Usa gli utenti di database indipendente per autenticare le identità a livello di database.
* Supporta l'autenticazione basata su token per le applicazioni che si connettono al pool SQL.
* Supporta l'autenticazione a più fattori tramite l'autenticazione universale di Active Directory per vari strumenti, tra cui [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md) e SQL Server Data [Tools.](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)

> [!NOTE]
> Azure Active Directory è ancora relativamente nuovo e presenta alcune limitazioni. Per assicurarsi che Azure Active Directory sia ideale per l'ambiente in uso, vedere [le funzionalità e le limitazioni di Azure AD](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), in particolare le considerazioni aggiuntive.
> 
> 

### <a name="configuration-steps"></a>Passaggi di configurazione
Seguire questa procedura per configurare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure Active Directory
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure
3. Creare un amministratore di Azure Active Directory per Azure SynapseCreate an Azure Active Directory administrator for Azure Synapse
4. Configurare i computer client
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD
6. Connettersi al pool SQL usando le identità di Azure ADConnect to your SQL pool by using Azure AD identities

Gli utenti di Azure Active Directory non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Informazioni dettagliate
* I passaggi per configurare e usare l'autenticazione di Azure Active Directory sono quasi identici per il database SQL di Azure e l'analisi SQL in Azure Synapse.The steps to configure and use Azure Active Directory authentication are nearly identical for Azure SQL Database and SQL Analytics in Azure Synapse. Seguire i passaggi dettagliati nell'argomento Connessione al database SQL o al [pool SQL tramite l'autenticazione](../../sql-database/sql-database-aad-authentication.md)di Azure Active Directory .
* Creare ruoli di database personalizzati e aggiungere utenti ai ruoli. Concedere quindi autorizzazioni granulari ai ruoli. Per altre informazioni, vedere [Introduzione alle autorizzazioni del motore di database](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Passaggi successivi
Per avviare l'esecuzione di query con Visual Studio e altre applicazioni, vedere [Query con Visual Studio](sql-data-warehouse-query-visual-studio.md).
