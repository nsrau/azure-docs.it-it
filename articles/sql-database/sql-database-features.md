---
title: "Panoramica sulle funzionalità di database SQL di Azure | Documentazione Microsoft"
description: "Questa pagina fornisce una panoramica dei database e server logici del database SQL di Azure e include una matrice di supporto delle funzionalità con collegamenti per ogni funzionalità elencata."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 7dc5210c073a3130bfc9ffdbc9ce33e19ca5bc1a
ms.lasthandoff: 04/10/2017


---
# <a name="azure-sql-database-features"></a>Funzionalità di database SQL di Azure
Questo argomento fornisce una panoramica dei database e server logici del database SQL di Azure e include una matrice di supporto delle funzionalità con collegamenti per ogni funzionalità elencata. 

## <a name="what-is-an-azure-sql-database-logical-server"></a>Informazioni sul server logico del database SQL di Azure
Un server logico del database SQL di Azure funge da punto di gestione centrale per più database. Nel database SQL un server è un costrutto logico diverso da un'istanza di SQL Server, familiare al mondo locale. In particolare, il servizio del database SQL non fornisce alcuna garanzia sulla posizione dei database in relazione ai server logici collegati e non espone funzionalità o accessi a livello di istanza. Per altre informazioni sui server logici SQL di Azure, vedere [Logical servers](sql-database-server-overview.md) (Server logici). 

## <a name="what-is-an-azure-sql-database"></a>Che cos'è un database SQL di Azure?
Ogni database SQL di Azure è associato a un server logico. Il database può essere:

- Un database singolo con relativo [set di risorse](sql-database-what-is-a-dtu.md#what-are-database-transaction-units-dtus) (DTU)
- Parte di un [pool di database](sql-database-elastic-pool.md) che [condivide un set di risorse](sql-database-what-is-a-dtu.md#what-are-elastic-database-transaction-units-edtus) (eDTU)
- Parte di un [set di scalabilità orizzontale di database partizionati](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), che può essere singolo o in pool
- Parte di un set di database che fanno parte di uno [schema progettuale SaaS multi-tenant](sql-database-design-patterns-multi-tenancy-saas-applications.md), i cui database possono essere singoli o in pool (o entrambi) 

Per altre informazioni sui database SQL di Azure, vedere [Database SQL](sql-database-overview.md).

## <a name="what-features-are-supported"></a>Funzionalità supportate

Le tabelle seguenti elencano le principali funzionalità del database SQL di Azure e di SQL Server, specificano il supporto e forniscono il collegamento a pagine informative sulle funzionalità in ogni piattaforma. Per le funzionalità di Transact-SQL, seguire il collegamento nella tabella relativo alla categoria della funzionalità. Per altre informazioni sui motivi del mancato supporto per determinati tipi di funzionalità, vedere anche [Differenze di Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md).

Altre funzionalità continueranno a essere aggiunte al database SQL. È quindi consigliabile visitare la pagina Web Aggiornamenti del servizio di Azure e usare i filtri disponibili:

* Filtrato per [servizio Database SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrato per Disponibilità generale [annunci](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) per le funzionalità del database SQL.

> [!TIP]
> Per testare la compatibilità di un database esistente con il database SQL di Azure, vedere l'articolo relativo alla [migrazione di un database SQL Server in Azure](sql-database-cloud-migrate.md).
>

| **Funzionalità** | **SQL Server** | **Database SQL di Azure** | 
| --- | :---: | :---: | 
| Replica geografica attiva | Non supportato: vedere [Gruppi di disponibilità AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) | [Supportato](sql-database-geo-replication-overview.md)
| Always Encrypted | [Supportato](https://msdn.microsoft.com/library/mt163865.aspx) | [Supportato](sql-database-always-encrypted.md) |
| Gruppi di disponibilità AlwaysOn | [Supportato](https://msdn.microsoft.com/library/hh510230.aspx) | Non supportato: vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md) |
| Collegamento di un database | [Supportato](https://msdn.microsoft.com/library/ms190209.aspx) | Non supportate |
| Ruoli applicazione | [Supportato](https://msdn.microsoft.com/library/ms190998.aspx) | [Supportato](https://msdn.microsoft.com/library/ms190998.aspx) |
| Scalabilità automatica | Non supportate | [Supportato](sql-database-service-tiers.md) |
| Azure Active Directory | Non supportate | [Supportato](sql-database-aad-authentication.md) |
| Data factory di Azure | [Supportato](../data-factory/data-factory-introduction.md) | [Supportato](../data-factory/data-factory-introduction.md) |
| Controllo | [Supportato](https://msdn.microsoft.com/library/cc280386.aspx) | [Supportato](sql-database-auditing.md) |
| File BACPAC (esportazione) | [Supportato](https://msdn.microsoft.com/library/hh213241.aspx) | [Supportato](sql-database-export.md) |
| File BACPAC (importazione) | [Supportato](https://msdn.microsoft.com/library/hh710052.aspx) | [Supportato](sql-database-import-portal.md) |
| Istruzioni BACKUP e RESTORE | [Supportato](https://msdn.microsoft.com/library/ff848768.aspx) | Non supportate |
| Funzioni predefinite | [Supportato](https://msdn.microsoft.com/library/ms174318.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ms174318.aspx) |
| Change Data Capture | [Supportato](https://msdn.microsoft.com/library/cc645937.aspx) | Non supportate |
| Rilevamento modifiche | [Supportato](https://msdn.microsoft.com/library/bb933875.aspx) | [Supportato](https://msdn.microsoft.com/library/bb933875.aspx) |
| Istruzioni sulle regole di confronto | [Supportato](https://msdn.microsoft.com/library/ff848763.aspx) | [Supportato](https://msdn.microsoft.com/library/ff848763.aspx) |
| Indici Columnstore | [Supportato](https://msdn.microsoft.com/library/gg492088.aspx) | [Solo edizione Premium](https://msdn.microsoft.com/library/gg492088.aspx) |
| Common Language Runtime (CLR) | [Supportato](https://msdn.microsoft.com/library/ms131102.aspx) | Non supportate |
| Database indipendenti | [Supportato](https://msdn.microsoft.com/library/ff929071.aspx) | Predefinito |
| Utenti indipendenti | [Supportato](https://msdn.microsoft.com/library/ff929188.aspx) | [Supportato](sql-database-manage-logins.md#non-administrator-users) |
| Controllo delle parole chiave del linguaggio di flusso | [Supportato](https://msdn.microsoft.com/library/ms174290.aspx) | [Supportato](https://msdn.microsoft.com/library/ms174290.aspx) |
| Query tra database | [Supportato](https://msdn.microsoft.com/library/dn584627.aspx) | [Query elastiche](sql-database-elastic-query-overview.md) |
| Cursori | [Supportato](https://msdn.microsoft.com/library/ms181441.aspx) | [Supportato](https://msdn.microsoft.com/library/ms181441.aspx) | 
| Compressione dei dati | [Supportato](https://msdn.microsoft.com/library/cc280449.aspx) | [Supportato](https://msdn.microsoft.com/library/cc280449.aspx) |
| Backup del database | [Esposti per gli utenti](https://msdn.microsoft.com/library/ms187048.aspx) | [Predefinito](sql-database-automated-backups.md) |
| Posta elettronica database | [Supportato](https://msdn.microsoft.com/library/ms189635.aspx) | Non supportate |
| Mirroring del database | [Supportato](https://msdn.microsoft.com/library/ms189852.aspx) | Non supportate |
| Opzioni di configurazione di base | [Supportato](https://msdn.microsoft.com/library/mt629158.aspx) | [Supportato](https://msdn.microsoft.com/library/mt629158.aspx) |
| Data Quality Services (DQS) | [Supportato](https://msdn.microsoft.com/library/ff877925.aspx) | Non supportate |
| Snapshot del database | [Supportato](https://msdn.microsoft.com/library/ms175158.aspx) | Non supportate |
| Tipi di dati | [Supportato](https://msdn.microsoft.com/library/ms187752.aspx) | [Supportato](https://msdn.microsoft.com/library/ms187752.aspx) |  
| Istruzioni DBCC | [Tutti](https://msdn.microsoft.com/library/ms188796.aspx) | [Alcuni](https://msdn.microsoft.com/library/ms188796.aspx) |
| Istruzioni DDL | [Supportato](https://msdn.microsoft.com/library/ff848799.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ff848799.aspx)
| Trigger DDL | [Supportato](https://msdn.microsoft.com/library/ms175941.aspx) | [Solo database](https://msdn.microsoft.com/library/ms175941.aspx) |
| Transazioni distribuite | [MS DTC](https://msdn.microsoft.com/library/ms131665.aspx) | Solo per scenari limitati interni al database SQL |
| Istruzioni DML | [Supportato](https://msdn.microsoft.com/library/ff848766.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ff848766.aspx) |
| Trigger DML | [Supportato](https://msdn.microsoft.com/library/ms178110.aspx) | [Supportato](https://msdn.microsoft.com/library/ms178110.aspx) |
| Viste a gestione dinamica | [Tutti](https://msdn.microsoft.com/library/ms188754.aspx) | [Alcuni](https://msdn.microsoft.com/library/ms188754.aspx) |
| pool elastici | Non supportate | [Supportato](sql-database-elastic-pool.md) |
| Processi elastici | Non supportato: vedere [SQL Server Agent](https://msdn.microsoft.com/library/ms189237.aspx) | [Supportato](sql-database-elastic-jobs-getting-started.md) | 
| Query elastiche | Non supportato: vedere [Query tra database](https://msdn.microsoft.com/library/dn584627.aspx) | [Supportato](sql-database-elastic-query-overview.md) |
| Notifiche degli eventi | [Supportato](https://msdn.microsoft.com/library/ms186376.aspx) | [Supportato](sql-database-insights-alerts-portal.md) |
| Espressioni | [Supportato](https://msdn.microsoft.com/library/ms190286.aspx) | [Supportato](https://msdn.microsoft.com/library/ms190286.aspx) |
| Eventi estesi | [Supportato](https://msdn.microsoft.com/library/bb630282.aspx) | [Alcuni](sql-database-xevent-db-diff-from-svr.md) |
| Stored procedure estese | [Supportato](https://msdn.microsoft.com/library/ms164627.aspx) | Non supportate |
| Filegroup | [Supportato](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) | [Solo primario](https://msdn.microsoft.com/library/ms189563.aspx#Anchor_2) |
| Filestream | [Supportato](https://msdn.microsoft.com/library/gg471497.aspx) | Non supportate |
| Ricerca full-text | [Supportato](https://msdn.microsoft.com/library/ms142571.aspx) | [Word breaker di terze parti non supportati](https://msdn.microsoft.com/library/ms142571.aspx) |
| Funzioni | [Supportato](https://msdn.microsoft.com/library/ms174318.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ms174318.aspx) |
| Ottimizzazione in memoria | [Supportato](https://msdn.microsoft.com/library/dn133186.aspx) | [Solo edizione Premium](https://msdn.microsoft.com/library/dn133186.aspx) |
| Processi | [Agente SQL Server](https://msdn.microsoft.com/library/ms189237.aspx) | [Supportato](sql-database-elastic-jobs-getting-started.md) |
| Supporto di dati JSON | [Supportato](https://msdn.microsoft.com/library/dn921897.aspx) | [Supportato](sql-database-json-features.md) |
| Elementi del linguaggio | [Supportato](https://msdn.microsoft.com/library/ff848807.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ff848807.aspx) |  
| Server collegati | [Supportato](https://msdn.microsoft.com/library/ms188279.aspx) | Non supportato: vedere [Query elastica](sql-database-elastic-query-horizontal-partitioning.md) |
| Log shipping | [Supportato](https://msdn.microsoft.com/library/ms187103.aspx) | Non supportato: vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md) |
| Comandi di gestione | [Supportato](https://msdn.microsoft.com/library/ms190286.aspx)| [Non supportato](https://msdn.microsoft.com/library/ms190286.aspx) |
| Master Data Services (MDS) | [Supportato](https://msdn.microsoft.com/library/ff487003.aspx) | Non supportate |
| Registrazione minima nell'importazione bulk | [Supportato](https://msdn.microsoft.com/library/ms190422.aspx) | Non supportate |
| Modifica dei dati di sistema | [Supportato](https://msdn.microsoft.com/library/ms178028.aspx) | Non supportate |
| Operazioni online sugli indici | [Supportato](https://msdn.microsoft.com/library/ms177442.aspx) | [Dimensioni delle transazioni limitate dal livello di servizio](https://msdn.microsoft.com/library/ms177442.aspx) |
| Operatori | [Supportato](https://msdn.microsoft.com/library/ms174986.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ms174986.aspx) |
| Ripristino temporizzato di un database | [Supportato](https://msdn.microsoft.com/library/ms179451.aspx) | [Supportato](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Supportato](https://msdn.microsoft.com/library/mt143171.aspx) | [Non supportato]
| Gestione basata su criteri | [Supportato](https://msdn.microsoft.com/library/bb510667.aspx) | Non supportate |
| Predicati | [Supportato](https://msdn.microsoft.com/library/ms189523.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ms189523.aspx)
| Servizi R | [Supportato](https://msdn.microsoft.com/library/mt604845.aspx)
| Resource governor | [Supportato](https://msdn.microsoft.com/library/bb933866.aspx) | Non supportate |
| Ripristino del database da backup | [Supportato](https://msdn.microsoft.com/library/ms187048.aspx#anchor_6) | [Solo da backup predefiniti](sql-database-recovery-using-backups.md) |
| Sicurezza a livello di riga | [Supportato](https://msdn.microsoft.com/library/dn765131.aspx) | [Supportato](https://msdn.microsoft.com/library/dn765131.aspx) |
| Istruzioni per la sicurezza | [Supportato](https://msdn.microsoft.com/library/ff848791.aspx) | [Alcuni](https://msdn.microsoft.com/library/ff848791.aspx) |
| Ricerca semantica | [Supportato](https://msdn.microsoft.com/library/gg492075.aspx) | Non supportate |
| Numeri di sequenza | [Supportato](https://msdn.microsoft.com/library/ff878058.aspx) | [Supportato](https://msdn.microsoft.com/library/ff878058.aspx) |
| Broker di servizio | [Supportato](https://msdn.microsoft.com/library/bb522893.aspx) | Non supportate |
| Opzioni di configurazione del server | [Supportato](https://msdn.microsoft.com/library/ms189631.aspx) | Non supportato: vedere [Opzioni di configurazione del database](https://msdn.microsoft.com/library/mt629158.aspx) |
| Istruzioni SET | [Supportato](https://msdn.microsoft.com/library/ms190356.aspx) | [La maggior parte](https://msdn.microsoft.com/library/ms190356.aspx) 
| Spatial | [Supportato](https://msdn.microsoft.com/library/bb933790.aspx) | [Supportato](https://msdn.microsoft.com/library/bb933790.aspx) |
| Agente SQL Server | [Supportato](https://msdn.microsoft.com/library/ms189237.aspx) | Non supportato: vedere [Introduzione ai processi di Database Elastici](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Supportato](https://msdn.microsoft.com/library/bb522607.aspx) | Non supportato: vedere [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Supportato](https://msdn.microsoft.com/library/ms141026.aspx) | Non supportato: vedere [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Supportato](https://msdn.microsoft.com/library/hh245198.aspx) | [Supportato](https://msdn.microsoft.com/library/hh245198.aspx) |
| SQL Server Profiler | [Supportato](https://msdn.microsoft.com/library/ms181091.aspx) | Non supportato: vedere [Eventi estesi](https://msdn.microsoft.com/library/ms181091.aspx) |
| Replica SQL Server | [Supportato](https://msdn.microsoft.com/library/ms151198.aspx) | [Solo per iscritti alla replica transazionale e snapshot](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Supportato](https://msdn.microsoft.com/library/ms159106.aspx) | Non supportate |
| Stored procedure | [Supportato](https://msdn.microsoft.com/library/ms190782.aspx) | [Supportato](https://msdn.microsoft.com/library/ms190782.aspx) |
| Funzioni archiviate dal sistema | [Supportato](https://msdn.microsoft.com/library/ff848780.aspx) | [Alcuni](https://msdn.microsoft.com/library/ff848780.aspx) |
| Stored procedure di sistema | [Supportato](https://msdn.microsoft.com/library/ms187961.aspx) | [Alcuni](https://msdn.microsoft.com/library/ms187961.aspx) |
| Tabelle di sistema | [Supportato](https://msdn.microsoft.com/library/ms179932.aspx) | [Alcuni](https://msdn.microsoft.com/library/ms179932.aspx) |
| Viste di sistema | [Supportato](https://msdn.microsoft.com/library/ms177862.aspx) | [Alcuni](https://msdn.microsoft.com/library/ms177862.aspx)
| Partizionamento di tabelle | [Supportato](https://msdn.microsoft.com/library/ms190787.aspx) | [Solo filegroup primari](https://msdn.microsoft.com/library/ms190787.aspx) |
| Tabelle temporanee | [Locali e globali](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) | [Solo locali](https://msdn.microsoft.com/library/ms174979.aspx#Anchor_4) |
| Tabelle temporali | [Supportato](https://msdn.microsoft.com/library/dn935015.aspx) | [Supportato](sql-database-temporal-tables.md) |
| Istruzioni TRANSACTION | [Supportato](https://msdn.microsoft.com/library/ms174377.aspx) | [Supportato](https://msdn.microsoft.com/library/ms174377.aspx) |
| Variabili | [Supportato](https://msdn.microsoft.com/library/ff848809.aspx) | | [Supportato](https://msdn.microsoft.com/library/ff848809.aspx) | 
| Transparent data encryption (TDE)  | [Supportato](https://msdn.microsoft.com/library/bb934049.aspx) | [Supportato](https://msdn.microsoft.com/dn948096.aspx) |
| Windows Server Failover Clustering | [Supportato](https://msdn.microsoft.com/library/hh270278.aspx) | Non supportato: vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md) |
| Indici XML | [Supportato](http://msdn.microsoft.com/library/bb934097.aspx) | [Supportato](http://msdn.microsoft.com/library/bb934097.aspx) |
| Istruzioni XML | [Supportato](https://msdn.microsoft.com/library/ff848798.aspx) | [Supportato](https://msdn.microsoft.com/library/ff848798.aspx) |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul servizio di Database SQL di Azure, vedere [Informazioni sul database SQL](sql-database-technical-overview.md)
- Per una panoramica sui server logici di SQL Azure, vedere [SQL Database logical server overview](sql-database-server-overview.md) (Introduzione al server logico del database SQL)
- Per una panoramica sui database SQL di Azure, vedere [Panoramica del database SQL](sql-database-overview.md).
- Per informazioni sul supporto di Transact-SQL e sulle differenze, vedere [Differenze di Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md).
- Informazioni sulle quote specifiche di risorse e sulle limitazioni in base al **livello di servizio**. Per una panoramica dei livelli di servizio, vedere [Livelli di servizio del Database SQL](sql-database-service-tiers.md).
- Per una panoramica della sicurezza, vedere [Azure SQL Database Security Overview](sql-database-security-overview.md) (Panoramica della sicurezza del database SQL di Azure).
- Per informazioni sulla disponibilità di driver e sul supporto per il database SQL, vedere [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md).

