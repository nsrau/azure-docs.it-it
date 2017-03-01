---
title: Multi-Factor Authentication - Azure SQL | Documentazione Microsoft
description: Utilizzare Multi-Factored Authentication con SSMS per il database SQL e SQL Data Warehouse.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6e68ff56eaab3de8166711c872bf1b5707dbdf56
ms.openlocfilehash: ce77a059a05f83d74e80c71a87a22de6755b2dbb
ms.lasthandoff: 02/16/2017


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Supporto di SQL Server Management Studio (SSMS) per l'autenticazione MFA di Azure AD con database SQL e SQL Data Warehouse
Il database SQL e SQL Data Warehouse di Azure adesso supportano le connessioni da SQL Server Management Studio (SSMS) tramite l' *autenticazione universale di Active Directory*. L'autenticazione universale di Active Directory è un flusso di lavoro interattivo che supporta *Azure Multi-Factor Authentication* (MFA). Azure MFA consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui telefonata, SMS, smart card con pin o notifica tramite app per dispositivi mobili, in modo che ogni utente possa scegliere il metodo che preferisce. 

* Per una descrizione di Multi-Factor Authentication (autenticazione a più fattori), consultare [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
* Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

## <a name="multi-factor-options"></a>Opzioni Multi-Factor

SSMS ora supporta:

* MFA interattiva con Azure AD con il potenziale per la convalida della casella di dialogo popup.
* I metodi di autenticazione integrata di Active Directory e tramite password di Active Directory non interattivi che possono essere utilizzati in svariate applicazioni diverse (ADO.NET, JDBC, ODBC, e così via). Questi due metodi non aprono mai finestre di dialogo popup.

Quando l'account utente è configurato per l'MFA, il flusso di lavoro dell'autenticazione interattiva richiede l'interazione supplementare dell'utente tramite l'uso di finestre di dialogo popup, smart card, ecc. Quando l'account utente è configurato per l'MFA, l'utente deve selezionare l'autenticazione universale di Azure per connettersi. Se l'account utente non richiede l'MFA, l'utente può comunque utilizzare le altre due opzioni di autenticazione di Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitazioni dell'autenticazione universale per il database SQL e SQL Data Warehouse
* SSMS è l'unico strumento attualmente abilitato per l'MFA tramite l'autenticazione universale di Active Directory.
* Solo un singolo account di Azure Active Directory può accedere a un'istanza di SSMS mediante l'autenticazione universale. Per accedere come un altro account di Azure AD, è necessario utilizzare un'altra istanza di SSMS. Questa restrizione è limitata all'autenticazione universale di Active Directory; è possibile accedere a server differenti tramite l'autenticazione con password di Active Directory, l'autenticazione integrata di Active Directory o autenticazione SQL Server.
* SSMS supporta l'autenticazione universale di Active Directory per la visualizzazione di Esplora oggetti, Editor di query e Archivio query.
* Né DacFx né Progettazione schema supportano l'autenticazione universale.
* Gli account MSA non sono supportati per l'autenticazione universale di Active Directory.
* L'autenticazione universale di Active Directory non è supportata in SSMS per gli utenti importati nell'istanza di Active Directory corrente da altre istanze di Active Directory di Azure. Questi utenti non sono supportati, in quanto per convalidare gli account è necessario un ID tenant e non esiste alcun meccanismo per fornire tale ID.
* Non ci sono requisiti software aggiuntivi per l'autenticazione universale di Active Directory ad eccezione del fatto che si utilizzi una versione supportata di SSMS.



## <a name="next-steps"></a>Passaggi successivi

* Per la procedura di configurazione, vedere [Configurare Multi-Factor Authentication con database SQL di Azure per SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
* Concedere ad altri utenti l'accesso al database: [Autenticazione e autorizzazione per il database SQL: concessione dell'accesso](sql-database-manage-logins.md)  
Verificare che altri utenti possano connettersi tramite il firewall: [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)



