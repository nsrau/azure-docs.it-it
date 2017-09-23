---
title: Autenticazione in Azure SQL Data Warehouse | Microsoft Docs
description: Autenticazione di Azure Active Directory (AAD) e SQL Server in Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
tags: 
ms.assetid: fefaaa75-2d0c-4e5d-aadb-410342d1ad73
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.custom: security
ms.date: 03/21/2017
ms.author: rortloff;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 92f48027051bc4aff4d6b8d66fdd6de81bba3657
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---
# <a name="authentication-to-azure-sql-data-warehouse"></a>Autenticazione in Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)
> * [Autenticazione](sql-data-warehouse-authentication.md)
> * [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
> * [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Per connettersi a SQL Data Warehouse è necessario passare le credenziali di sicurezza per scopi di autenticazione. Al momento di stabilire una connessione, alcune impostazioni di connessione sono configurate come parte della creazione della sessione di query.  

Per altre informazioni sulla sicurezza e sull'attivazione di connessioni al data warehouse, vedere l'articolo [Proteggere un database in SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Autenticazione in SQL
Per connettersi a SQL Data Warehouse è necessario fornire le informazioni seguenti:

* Nome del server completo
* Specificare l'autenticazione di SQL
* Nome utente
* Password
* Database predefinito (facoltativo)

Per impostazione predefinita la connessione si collega al database *master* e non al database utente. Per connettersi al database utente è possibile scegliere di effettuare una delle seguenti operazioni:

* Specificare il database predefinito per la registrazione del server con Esplora oggetti di SQL Server in SSDT, SSMS o nella stringa di connessione dell'applicazione. Ad esempio, includere il parametro InitialCatalog per una connessione ODBC.
* Selezionare il database utente prima di creare una sessione in SSDT.

> [!NOTE]
> L'istruzione Transact-SQL **USE MyDatabase;** non è supportata per la modifica del database per una connessione. Per informazioni sulla connessione a SQL Data Warehouse con SSDT, fare riferimento all'articolo [Eseguire query con Visual Studio][Query with Visual Studio].
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticazione di Azure Active Directory (AAD)
L'autenticazione di [Azure Active Directory][What is Azure Active Directory] è un meccanismo di connessione a SQL Data Warehouse di Microsoft Azure tramite le identità di Azure Active Directory (Azure AD). Con l'autenticazione di Azure Active Directory è possibile gestire in una posizione centrale le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti di SQL Data Warehouse in un'unica posizione e semplifica la gestione delle autorizzazioni. 

### <a name="benefits"></a>Vantaggi
I vantaggi di Azure Active Directory includono i seguenti:

* Offre un'alternativa all'autenticazione di SQL Server.
* Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
* Consente la rotazione delle password in un'unica posizione.
* Consente di gestire le autorizzazioni del database tramite gruppi (AAD) esterni.
* Consente di eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
* Usa gli utenti di database indipendente per autenticare le identità a livello di database.
* Supporta l'autenticazione basata su token per le applicazioni che si connettono a SQL Data Warehouse.
* Supporta la Multi-Factor Authentication tramite l'autenticazione universale di Active Directory per SQL Server Management Studio. Per una descrizione di Multi-Factor Authentication, vedere [Il supporto SSMS per l'MFA di Azure Active Directory con database SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory è ancora relativamente nuovo e presenta alcune limitazioni. Per assicurarsi che Azure Active Directory sia ideale per l'ambiente in uso, vedere [le funzionalità e le limitazioni di Azure AD][Azure AD features and limitations], in particolare le considerazioni aggiuntive.
> 
> 

### <a name="configuration-steps"></a>Procedura di configurazione
Seguire questa procedura per configurare l'autenticazione di Azure Active Directory.

1. Creare e popolare un'istanza di Azure Active Directory
2. Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure
3. Creare un amministratore di Azure Active Directory per Azure SQL Data Warehouse.
4. Configurare i computer client
5. Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD
6. Connettersi al data warehouse usando le identità di Azure AD

Gli utenti di Azure Active Directory non sono attualmente visualizzati in Esplora oggetti di SSDT. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Informazioni dettagliate
* La procedura per configurare e usare l'autenticazione di Azure Active Directory è quasi uguale per il database SQL di Azure e Azure SQL Data Warehouse. Seguire la procedura riportata nell'argomento [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Creare ruoli di database personalizzati e aggiungere utenti ai ruoli. Concedere quindi autorizzazioni granulari ai ruoli. Per altre informazioni, vedere [Introduzione alle autorizzazioni del motore di database](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a eseguire query sul data warehouse con Visual Studio e altre applicazioni, vedere [Eseguire query con Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations

