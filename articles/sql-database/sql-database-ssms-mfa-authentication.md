---
title: Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: Utilizzare Multi-Factored Authentication con SSMS per il database SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: security-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 04/07/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 33282f1ce0c31056524359aeb42399be550334e2
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Autenticazione universale con database SQL e SQL Data Warehouse (supporto SSMS per MFA)
Il database SQL e Azure SQL Data Warehouse supportano le connessioni da SQL Server Management Studio (SSMS) tramite l'*autenticazione universale di Active Directory*. 

- L'autenticazione universale di Active Directory supporta due modalità di autenticazione non interattiva, vale a dire l'autenticazione della password di Active Directory e l'autenticazione integrata di Active Directory. Le modalità di autenticazione integrata di Active Directory e della password di Active Directory non interattive possono essere usate in svariate applicazioni (ADO.NET, JDBC, ODBC e così via). Questi due metodi non aprono mai finestre di dialogo popup.

- L'autenticazione universale di Active Directory è una modalità interattiva che supporta *Azure Multi-Factor Authentication* (MFA). Azure MFA consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui telefonata, SMS, smart card con pin o notifica tramite app per dispositivi mobili, in modo che ogni utente possa scegliere il metodo che preferisce. La convalida di MFA interattiva con Azure AD può avvenire attraverso una finestra popup.

Per una descrizione di Multi-Factor Authentication (autenticazione a più fattori), consultare [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Parametro nome di dominio o ID tenant di Azure AD   

A partire da [SSMS versione 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), gli utenti che vengono importati nell'attuale Active Directory da altri Azure Active Directory possono fornire il nome di dominio o l'ID tenant di Azure AD al momento della connessione. L'**autenticazione universale di Active Directory** può così identificare l'autorità di autenticazione corretta. Questa opzione è inoltre necessaria per il supporto degli account Microsoft (MSA), ad esempio outlook.com, hotmail.com o live.com. Tutti gli utenti che desiderano essere autenticati usando l'autenticazione universale devono immettere il nome di dominio o l'ID tenant di AD Azure. Questo parametro rappresenta il nome di dominio/ID tenant di Azure AD a cui è collegato il server di Azure. Se ad esempio il server di Azure è associato al dominio di Azure AD `contosotest.onmicrosoft.com` in cui l'utente `joe@contosodev.onmicrosoft.com` è ospitato come utente importato dal dominio di Azure AD `contosodev.onmicrosoft.com`, il nome del dominio richiesto per l'autenticazione di questo utente è `contosotest.onmicrosoft.com`. Se l'utente è un utente nativo di Azure AD collegato al server di Azure e non è un account MSA, non è necessario usare l'ID tenant o il nome di dominio. Per specificare il parametro (a partire da SSMS versione 17), nella finestra di dialogo **Connettere un database** compilare la finestra selezionando **Autenticazione universale di Active Directory**, fare clic su **Opzioni**, selezionare la scheda **Proprietà connessione**, selezionare la casella **ID tenant o nome di dominio AD** e immettere l'autorità di autenticazione, ad esempio il nome di dominio (**contosotest.onmicrosoft.com**) o il GUID dell'ID tenant.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitazioni dell'autenticazione universale per il database SQL e SQL Data Warehouse
* SSMS è l'unico strumento attualmente abilitato per l'MFA tramite l'autenticazione universale di Active Directory.
* Solo un singolo account di Azure Active Directory può accedere a un'istanza di SSMS mediante l'autenticazione universale. Per accedere come un altro account di Azure AD, è necessario utilizzare un'altra istanza di SSMS. Questa restrizione è limitata all'autenticazione universale di Active Directory; è possibile accedere a server differenti tramite l'autenticazione con password di Active Directory, l'autenticazione integrata di Active Directory o autenticazione SQL Server.
* SSMS supporta l'autenticazione universale di Active Directory per la visualizzazione di Esplora oggetti, Editor di query e Archivio query.
* Né DacFx né Progettazione schema supportano l'autenticazione universale.
* Non ci sono requisiti software aggiuntivi per l'autenticazione universale di Active Directory ad eccezione del fatto che si utilizzi una versione supportata di SSMS.


## <a name="next-steps"></a>Passaggi successivi

* Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
* Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: concessione dell'accesso](sql-database-manage-logins.md)  
Verificare che altri utenti possano connettersi tramite il firewall: [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
* Per la configurazione e la gestione di Azure AD, vedere [Configure and manage Azure Active Directory authentication with SQL Database or SQL Data Warehouse](sql-database-aad-authentication-configure.md) (Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL o con SQL Data Warehouse).



