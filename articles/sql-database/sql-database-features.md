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
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9daf8be93216aefbcf02e3fcba44d048ab95b43d
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="azure-sql-database-features"></a>Funzionalità di database SQL di Azure

Le tabelle seguenti elencano le principali funzionalità del database SQL di Azure e le funzionalità equivalenti di SQL Server. Indicano anche se la singola funzionalità è supportata e riportano un collegamento ad altre informazioni sulla funzionalità in ogni piattaforma. Per le differenze di Transact-SQL da considerare quando si esegue la migrazione di una soluzione di database esistente, vedere [Risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](sql-database-transact-sql-information.md).

Altre funzionalità continueranno a essere aggiunte al database SQL. È quindi consigliabile visitare la pagina Web Aggiornamenti del servizio di Azure e usare i filtri disponibili:

* Filtrato per [servizio Database SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrato per Disponibilità generale [annunci](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) per le funzionalità del database SQL.

| **Funzionalità** | **SQL Server** | **Database SQL di Azure** | 
| --- | :---: | :---: | 
| Replica geografica attiva | Non supportata, vedere [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Supportato](sql-database-geo-replication-overview.md)
| Always Encrypted | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Supportata, vedere [Archivio certificati](sql-database-always-encrypted.md) e [Key Vault](sql-database-always-encrypted-azure-key-vault.md)|
| Gruppi di disponibilità AlwaysOn | [Supportato](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Non supportato: vedere [Replica geografica attiva](sql-database-geo-replication-overview.md) |
| Collegamento di un database | [Supportato](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Non supportate |
| Ruoli applicazione | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Scalabilità automatica | Non supportate | Supportata, vedere [Livelli di servizio](sql-database-service-tiers.md) |
| Azure Active Directory | Non supportate | [Supportato](sql-database-aad-authentication.md) |
| Data factory di Azure | [Supportato](../data-factory/data-factory-introduction.md) | [Supportato](../data-factory/data-factory-introduction.md) |
| Controllo | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Supportato](sql-database-auditing.md) |
| File BACPAC (esportazione) | [Supportato](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Supportato](sql-database-export.md) |
| File BACPAC (importazione) | [Supportato](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Supportato](sql-database-import.md) |
| BACKUP | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Non supportate |
| Funzioni predefinite | [Supportato](https://docs.microsoft.com/sql/t-sql/functions/functions) | Supportate per la maggior parte, vedere le [singole funzioni](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Change Data Capture | [Supportato](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Non supportate |
| Rilevamento modifiche | [Supportato](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Supportato](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Istruzioni sulle regole di confronto | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Indici Columnstore | [Supportato](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Solo edizione Premium](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| Common Language Runtime (CLR) | [Supportato](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Non supportate |
| Database indipendenti | [Supportato](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Supportato](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Utenti indipendenti | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Supportato](sql-database-manage-logins.md#non-administrator-users) |
| Controllo delle parole chiave del linguaggio di flusso | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Query tra database | [Supportato](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Supportate parzialmente, vedere [Query elastiche](sql-database-elastic-query-overview.md) |
| Cursori | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Compressione dei dati | [Supportato](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Supportato](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Backup del database | [Gestiti dall'utente](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Gestiti dal servizio database SQL](sql-database-automated-backups.md) |
| Posta elettronica database | [Supportato](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Non supportate |
| Mirroring del database | [Supportato](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Non supportate |
| Impostazioni di configurazione del database | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [Supportato](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Non supportate |
| Snapshot del database | [Supportato](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Non supportate |
| Tipi di dati | [Supportato](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Supportato](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| Istruzioni DBCC | [Supportato](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Supportate per la maggior parte, vedere le [singole istruzioni](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| Istruzioni DDL | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/statements) | Supportate per la maggior parte, vedere le [singole istruzioni](https://docs.microsoft.com/sql/t-sql/statements/statements)
| Trigger DDL | [Supportato](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Solo database](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Transazioni distribuite | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Solo per scenari limitati interni al database SQL |
| Istruzioni DML | [Supportato](https://docs.microsoft.com/sql/t-sql/queries/queries) | Supportate per la maggior parte, vedere le [singole istruzioni](https://docs.microsoft.com/sql/t-sql/queries/queries) |
| Trigger DML | [Supportato](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Supportato](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| Viste a gestione dinamica | [Tutti](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Supportate in alcuni casi, vedere le [singole viste a gestione dinamica](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| Pool elastici | Non supportate | [Supportato](sql-database-elastic-pool.md) |
| Processi elastici | Non supportato: vedere [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [Supportato](sql-database-elastic-jobs-getting-started.md) | 
| Query elastiche | Non supportato: vedere [Query tra database](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [Supportato](sql-database-elastic-query-overview.md) |
| Notifiche degli eventi | [Supportato](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Supportato](sql-database-insights-alerts-portal.md) |
| Espressioni | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Eventi estesi | [Supportato](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Supportati in alcuni casi, vedere i [singoli eventi](sql-database-xevent-db-diff-from-svr.md) |
| Stored procedure estese | [Supportato](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Non supportate |
| File e gruppi di file | [Supportato](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Solo primario](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Filestream | [Supportato](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Non supportate |
| Ricerca full-text | [Supportato](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [Word breaker di terze parti non supportati](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Funzioni | [Supportato](https://docs.microsoft.com/sql/t-sql/functions/functions) | Supportate per la maggior parte, vedere le [singole funzioni](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Ottimizzazione in memoria | [Supportato](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Solo edizione Premium](sql-database-in-memory.md) |
| Processi | Vedere [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Vedere [Processi elastici](sql-database-elastic-jobs-getting-started.md) |
| Supporto di dati JSON | [Supportato](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Supportato](sql-database-json-features.md) |
| Elementi del linguaggio | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Supportati per la maggior parte, vedere i [singoli elementi](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| Server collegati | [Supportato](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Non supportato: vedere [Query elastica](sql-database-elastic-query-horizontal-partitioning.md) |
| Log shipping | [Supportato](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Non supportato: vedere [Replica geografica attiva](sql-database-geo-replication-overview.md) |
| Master Data Services (MDS) | [Supportato](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Non supportate |
| Registrazione minima nell'importazione bulk | [Supportato](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Non supportate |
| Modifica dei dati di sistema | [Supportato](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Non supportate |
| Operazioni online sugli indici | [Supportato](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Supportate, dimensioni delle transazioni limitate dal livello di servizio](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Operatori | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Supportati per la maggior parte, vedere i [singoli operatori](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| Ripristino temporizzato di un database | [Supportato](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Supportato](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Supportato](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Non supportate
| Gestione basata su criteri | [Supportato](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Non supportate |
| Predicati | [Supportato](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Supportati per la maggior parte, vedere i [singoli predicati](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| Servizi R | [Supportato](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Resource governor | [Supportato](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Non supportate |
| Istruzioni RESTORE | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Non supportato | 
| Ripristino del database da backup | [Supportato](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [Solo da backup predefiniti](sql-database-recovery-using-backups.md) |
| Sicurezza a livello di riga | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Ricerca semantica | [Supportato](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Non supportate |
| Numeri di sequenza | [Supportato](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Supportato](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Broker di servizio | [Supportato](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Non supportate |
| Impostazioni di configurazione del server | [Supportato](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Non supportato: vedere [Opzioni di configurazione del database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Istruzioni SET | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Supportate per la maggior parte, vedere le [singole istruzioni](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| Spatial | [Supportato](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Supportato](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| Agente SQL Server | [Supportato](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Non supportato: vedere [Introduzione ai processi di Database Elastici](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Supportato](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Non supportato: vedere [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Supportato](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Non supportato: vedere [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Supportato](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Supportato](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server Profiler | [Supportato](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Non supportato: vedere [Eventi estesi](sql-database-xevent-db-diff-from-svr.md) |
| Replica SQL Server | [Supportato](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Solo per iscritti alla replica transazionale e snapshot](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Supportato](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Non supportate |
| Stored procedure | [Supportato](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Supportato](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| Funzioni archiviate dal sistema | [Supportato](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Supportate in alcuni casi, vedere le [singole funzioni](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| Stored procedure di sistema | [Supportato](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Supportate in alcuni casi, vedere le [singole stored procedure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Tabelle di sistema | [Supportato](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Supportate in alcuni casi, vedere le [singole tabelle](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Viste del catalogo di sistema | [Supportato](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Supportate in alcuni casi, vedere le [singole viste](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| Partizionamento di tabelle | [Supportato](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Supportato, [solo filegroup primari](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| Tabelle temporanee | [Locali e globali](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Solo locali](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Tabelle temporali | [Supportato](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Supportato](sql-database-temporal-tables.md) |
| Transazioni | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| Variabili | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Supportato](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Transparent data encryption (TDE)  | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Supportato](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server Failover Clustering | [Supportato](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Non supportato: vedere [Replica geografica attiva](sql-database-geo-replication-overview.md) |
| Indici XML | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Supportato](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul servizio di Database SQL di Azure, vedere [Informazioni sul database SQL](sql-database-technical-overview.md)
- Per informazioni sul supporto di Transact-SQL e sulle differenze, vedere [Risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](sql-database-transact-sql-information.md).

