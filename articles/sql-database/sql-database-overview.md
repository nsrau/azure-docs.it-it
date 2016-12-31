---
title: Panoramica del database SQL di Azure | Documentazione Microsoft
description: Questa pagina offre una panoramica sui database SQL di Azure.
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 6d5f4640556f98f9601139c318ffc578e9df2539


---
# <a name="azure-sql-database-overview"></a>Panoramica del database SQL di Azure
Questo argomento offre una panoramica sui database SQL di Azure. Per informazioni sui server logici SQL di Azure, vedere [Logical servers](sql-database-server-overview.md) (Server logici).

## <a name="what-is-azure-sql-database"></a>Informazioni sul database SQL
Ogni database SQL di Azure è associato a un server logico. Il database può essere:

- Un database autonomo con relativo [set di risorse](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- Parte di un [pool elastico](sql-database-elastic-pool.md) che [condivide un set di risorse](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU)
- Parte di un [set di scalabilità orizzontale di database partizionati](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), che può essere singolo o in pool
- Parte di un set di database che fanno parte di uno [schema progettuale SaaS multi-tenant](sql-database-design-patterns-multi-tenancy-saas-applications.md), i cui database possono essere singoli o in pool (o entrambi) 

## <a name="how-do-i-connect-and-authenticate-to-an-azure-sql-database"></a>Come connettersi ed eseguire l'autenticazione a un database SQL Azure

- **Autenticazione e autorizzazione**: il database SQL Azure supporta l'autenticazione di SQL e l’autenticazione di Azure Active Directory, con alcune limitazioni: vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) per l'autenticazione. È possibile connettersi ed eseguire l’autenticazione al database SQL di Azure tramite il database master del server oppure direttamente a un database utente. Per altre informazioni, vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md). L'autenticazione Windows non è supportata. 
- **TDS**: il database SQL di Microsoft Azure supporta la versione client 7.3 o successiva del protocollo TDS (Tabular Data Stream).
- **TCP/IP**: sono consentite solo connessioni TCP/IP.
- **Firewall del database SQL**: per proteggere i dati, il firewall del database SQL impedisce qualsiasi accesso al server di database o ai propri database finché non vengono specificati i computer autorizzati. Vedere [Panoramica sulle regole del firewall per il database SQL di Azure](sql-database-firewall-configure.md).

## <a name="what-collations-are-supported"></a>Regole di confronto supportate
La regola di confronto del database predefinito usato dal database SQL di Microsoft Azure è **SQL_LATIN1_GENERAL_CP1_CI_AS**, in cui **LATIN1_GENERAL** indica la lingua inglese (Stati Uniti), **CP1** la pagina del codice 1252, **CI** l'assenza di distinzione tra maiuscole e minuscole e **AS** l'assenza di distinzione tra caratteri accentati e non accentati. Non è possibile modificare le regole di confronto per i database V12. Per altre informazioni su come impostare le regole di confronto, vedere [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="what-are-the-naming-requirements-for-database-objects"></a>Requisiti di denominazione per oggetti del database

I nomi di tutti i nuovi oggetti devono essere conformi alle regole di SQL Server per gli identificatori. Per altre informazioni, vedere [Identificatori](https://msdn.microsoft.com/library/ms175874.aspx).

## <a name="what-features-are-supported-by-azure-sql-databases"></a>Funzionalità supportate dal database SQL di Azure

Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](sql-database-features.md) (Funzioni del database SQL di Azure). Per altre informazioni sui motivi del mancato supporto per determinati tipi di funzionalità, vedere anche [Differenze di Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md).

## <a name="how-do-i-manage-an-azure-sql-database"></a>Gestione di un database SQL di Azure

È possibile gestire i server logici del database SQL di Azure in diversi modi:
- [Portale di Azure](sql-database-manage-portal.md)
- [PowerShell](sql-database-manage-powershell.md)
- [Transact-SQL](sql-database-manage-azure-ssms.md)
- [REST](/rest/api/sql/)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul servizio di Database SQL di Azure, vedere [Informazioni sul database SQL](sql-database-technical-overview.md)
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](sql-database-features.md) (Funzioni del database SQL di Azure)
- Per una panoramica sui server logici di SQL Azure, vedere [SQL Database logical server overview](sql-database-server-overview.md) (Introduzione al server logico del database SQL)
- Per informazioni sul supporto di Transact-SQL e sulle differenze, vedere [Differenze di Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md).
- Informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**. Per una panoramica dei livelli di servizio, vedere [Livelli di servizio del Database SQL](sql-database-service-tiers.md).
- Per le linee guida relative alla sicurezza, vedere [Linee guida e limitazioni per il database SQL di Azure](sql-database-security-guidelines.md).
- Per informazioni sulla disponibilità di driver e sul supporto per il database SQL, vedere [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md).




<!--HONumber=Dec16_HO2-->


