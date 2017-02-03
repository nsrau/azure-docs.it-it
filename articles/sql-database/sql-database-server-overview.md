---
title: Panoramica del server logico del database SQL di Azure | Documentazione Microsoft
description: Questa pagina fornisce considerazioni e linee guida per l&quot;uso dei server logici di SQL di Azure.
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: 17e2830dceeaa313dd0fd7d406bf68a75b6f900e


---
# <a name="azure-sql-database-logical-servers"></a>Server logici del database SQL di Azure

Questo argomento fornisce considerazioni e linee guida per l'uso dei server logici di SQL di Azure. Per informazioni sui database SQL di Azure, vedere [Database SQL](sql-database-overview.md).

## <a name="what-is-an-azure-sql-database-logical-server"></a>Informazioni sul server logico del database SQL di Azure
Un server logico del database SQL di Azure funge da punto di gestione centrale per più database. Nel database SQL un server è un costrutto logico diverso da un'istanza di SQL Server, familiare al mondo locale. In particolare, il servizio del database SQL non fornisce alcuna garanzia sulla posizione dei database in relazione ai server logici collegati e non espone funzionalità o accessi a livello di istanza. Per altre informazioni sui server logici SQL di Azure, vedere [Logical servers](sql-database-server-overview.md) (Server logici). 

Un server logico del database di Azure:

- Viene creato all'interno di una sottoscrizione di Azure, ma può essere spostato con le risorse contenute in un'altra sottoscrizione.
- È la risorsa padre per database, pool elastici e data warehouse.
- Fornisce lo spazio dei nomi a database, pool elastici e data warehouse.
- È un contenitore logico con semantica di lunga: l'eliminazione di un server comporta l'eliminazione di database, pool elastici e data warehouse in esso contenuti.
- Partecipa al controllo degli accessi basato su ruoli (RBAC) di Azure: i database e i pool elastici all'interno di un server ereditano i diritti di accesso dal server.
- È un elemento di ordine superiore dell'identità del database e dei pool elastici per la gestione delle risorse di Azure. Vedere lo schema dell'URL per database e pool.
- Colloca risorse in un'area.
- Fornisce un endpoint di connessione per l'accesso ai database (<serverName>.database.windows.net).
- Fornisce accesso ai metadati riguardanti le risorse contenute tramite DMV, connettendosi a un database master. 
- Fornisce l'ambito per i criteri di gestione che si applicano ai database: account di accesso, firewall, controllo, rilevamento minacce e così via. 
- È limitato da una quota nella sottoscrizione padre: sei server per sottoscrizione. Vedere i limiti relativi alle sottoscrizioni [qui](../azure-subscription-service-limits.md).
- Fornisce l'ambito di una quota del database e DTU per le risorse in che esso contenute, ad esempio 45000 DTU nella versione 12.
- Rappresenta l'ambito di controllo delle versioni per le funzionalità abilitate sulle risorse contenute: la versione più recente è la versione 12.
- Gli account di accesso all'entità a livello di server possono gestire tutti i database in un server
- Può contenere account di accesso simili a quelli delle istanze di SQL Server in locale che dispongono dell'accesso a uno o più database nel server ed è possibile concedere diritti amministrativi limitati. Per altre informazioni, vedere [Autenticazione e autorizzazione per database SQL: concessione dell'accesso](sql-database-manage-logins.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database-logical-server"></a>Come connettersi ed eseguire l'autenticazione a un server logico del database SQL Azure

- **Autenticazione e autorizzazione**: il database SQL Azure supporta l'autenticazione di SQL e l'autenticazione di Azure Active Directory, con alcune limitazioni: vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) per l'autenticazione. È possibile connettersi ed eseguire l’autenticazione al database SQL di Azure tramite il database master del server oppure direttamente a un database utente. Per altre informazioni, vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md). L'autenticazione Windows non è supportata. 
- **TDS**: il database SQL di Microsoft Azure supporta la versione client 7.3 o successiva del protocollo TDS (Tabular Data Stream).
- **TCP/IP**: sono consentite solo connessioni TCP/IP.
- **Firewall del database SQL**: per proteggere i dati, il firewall del database SQL impedisce qualsiasi accesso al server di database o ai propri database finché non vengono specificati i computer autorizzati. Vedere [Panoramica sulle regole del firewall per il database SQL di Azure](sql-database-firewall-configure.md).

## <a name="what-collations-are-supported"></a>Regole di confronto supportate

La regola di confronto del database predefinito usato dal database SQL di Microsoft Azure (che comprende il database master) è **SQL_LATIN1_GENERAL_CP1_CI_AS**, in cui **LATIN1_GENERAL** indica la lingua inglese (Stati Uniti), **CP1** la pagina del codice 1252, **CI** l'assenza di distinzione tra maiuscole e minuscole e **AS** l'assenza di distinzione tra caratteri accentati e non accentati. È sconsigliabile modificare le regole di confronto per i database versione 12 dopo la creazione. Per altre informazioni sulle regole di confronto, vedere [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Requisiti di denominazione per oggetti del database

I nomi di tutti i nuovi oggetti devono essere conformi alle regole di SQL Server per gli identificatori. Per altre informazioni, vedere [Identificatori](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported"></a>Funzionalità supportate

Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](sql-database-features.md) (Funzioni del database SQL di Azure). Per altre informazioni sui motivi del mancato supporto per determinati tipi di funzionalità, vedere anche [Differenze di Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md).

## <a name="how-do-i-manage-a-logical-server"></a>Gestione di un server logico

È possibile gestire i server logici del database SQL di Azure in diversi modi:
- [Portale di Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul servizio di Database SQL di Azure, vedere [Informazioni sul database SQL](sql-database-technical-overview.md)
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](sql-database-features.md) (Funzioni del database SQL di Azure)
- Per una panoramica sui database SQL di Azure, vedere [Panoramica del database SQL](sql-database-overview.md).
- Per informazioni sul supporto di Transact-SQL e sulle differenze, vedere [Differenze di Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md).
- Informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**. Per una panoramica dei livelli di servizio, vedere [Livelli di servizio del Database SQL](sql-database-service-tiers.md).
- Per una panoramica della sicurezza, vedere [Azure SQL Database Security Overview](sql-database-security-overview.md) (Panoramica della sicurezza del database SQL di Azure).
- Per informazioni sulla disponibilità di driver e sul supporto per il database SQL, vedere [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO4-->


