---
title: Configurare & gestire il riferimento al contenuto
description: Trovare un riferimento di contenuto che insegna la configurazione e la gestione del database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791767"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Configurare e gestire i riferimenti al contenuto-database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In questo articolo sono disponibili informazioni di riferimento sui contenuti di diverse guide, script e spiegazioni che consentono di gestire e configurare il database SQL di Azure. 

## <a name="load-data"></a>Caricare dati

- [Eseguire la migrazione al database SQL](migrate-to-database-from-sql-server.md)
- Informazioni su come [gestire il database SQL dopo la migrazione](manage-data-after-migrating-to-database.md).
- [Copiare un database](database-copy.md)
- [Importare un database da un file BACPAC](database-import.md)
- [Esportare un database in un file BACPAC](database-export.md)
- [Caricare dati con BCP](../load-from-csv-with-bcp.md)
- [Caricare dati con ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Configurare le funzionalità

- [Configurare l'autenticazione Azure Active Directory (Azure AD)](authentication-aad-configure.md)
- [Configurare l'accesso condizionale](conditional-access-configure.md)
- [Autenticazione di Azure AD a più fattori](authentication-mfa-ssms-overview.md)
- [Configurare Multi-Factor Authentication](authentication-mfa-ssms-configure.md)
- [Configurare i criteri di conservazione temporale](temporal-tables-retention-policy.md)
- [Configurare TDE con BYOK](transparent-data-encryption-byok-configure.md)
- [Eseguire la rotazione delle chiavi TDE BYOK](transparent-data-encryption-byok-key-rotation.md)
- [Rimuovere la protezione TDE](transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurare OLTP in memoria](../in-memory-oltp-configure.md)
- [Configurare Automazione di Azure](automation-manage.md)
- [Configurare la replica transazionale](replication-to-sql-database.md) per replicare i dati tra database.
- [Configurare il rilevamento delle minacce](threat-detection-configure.md) per consentire al database SQL di Azure di identificare le attività sospette, ad esempio attacchi SQL injection o l'accesso da posizioni sospette.
- [Configurare la maschera dati dinamica](dynamic-data-masking-configure-portal.md) per proteggere i dati sensibili.
- [Configurare la conservazione dei backup](long-term-backup-retention-configure.md) di un database in modo che vengano conservati nell'archivio BLOB di Azure. 
- [Configurare la replica geografica](active-geo-replication-overview.md) per mantenere una replica del database in un'altra area.
- [Configurare la sicurezza per le repliche geografiche](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Monitorare e ottimizzare il database

- [Ottimizzazione manuale](performance-guidance.md)
- [Usare le viste a gestione dinamica per monitorare le prestazioni](monitoring-with-dmvs.md)
- [Usare Query Store per monitorare le prestazioni](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Abilitare l'ottimizzazione automatica](automatic-tuning-enable.md) per consentire al database SQL di Azure di ottimizzare le prestazioni del carico di lavoro.
- [Abilitare le notifiche di posta elettronica per l'ottimizzazione automatica](automatic-tuning-email-notifications-configure.md) per ottenere informazioni sui suggerimenti per l'ottimizzazione.
- [Applicare i suggerimenti per le prestazioni](database-advisor-find-recommendations-portal.md) e ottimizzare il database.
- [Creare avvisi](alerts-insights-configure-portal.md) per avere notifiche dal database SQL di Azure.
- [Risolvere i problemi di connettività](troubleshoot-common-errors-issues.md) eventualmente rilevati tra le applicazioni e il database. È anche possibile usare [Integrità risorse per i problemi di connettività](resource-health-to-troubleshoot-connectivity.md).
- [Risolvere i problemi delle prestazioni con Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Gestire lo spazio file](file-space-manage.md) per monitorare l'utilizzo dello spazio di archiviazione nel database.
- [Usare il log di diagnostica di Intelligent Insights](intelligent-insights-use-diagnostics-log.md)
- [Monitorare lo spazio OLTP in memoria](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventi estesi

- [Eventi estesi](xevent-db-diff-from-svr.md)
- [Archiviare gli eventi estesi nel file di eventi](xevent-code-event-file.md)
- [Archiviare gli eventi estesi nel buffer circolare](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Eseguire query sui dati distribuiti

- [Eseguire query sui dati partizionati verticalmente](elastic-query-getting-started-vertical.md) su più database.
- [Report nel livello dati con scalabilità orizzontale](elastic-query-horizontal-partitioning.md).
- [Eseguire query tra tabelle con schemi diversi](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Sincronizzazione dei dati

- [Sincronizzazione dati SQL](sql-data-sync-data-sql-server-sql-database.md)
- [Agente di sincronizzazione dati](sql-data-sync-agent-overview.md)
- [Replicare le modifiche dello schema](sql-data-sync-update-sync-schema.md)
- [Monitorare con OMS](./monitor-tune-overview.md)
- [Procedure consigliate per la sincronizzazione dei dati](sql-data-sync-best-practices.md)
- [Risolvere i problemi di sincronizzazione dei dati](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Processi di database elastici

- [Creazione e gestione](elastic-jobs-powershell-create.md) Processi di database elastici con PowerShell.
- [Creare e gestire](elastic-jobs-tsql-create-manage.md) i processi di database elastici tramite Transact-SQL.
- [Eseguire la migrazione da un processo elastico precedente](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Partizionamento orizzontale del database

- [Aggiornare la libreria client del database elastico](elastic-scale-upgrade-client-library.md).
- [Creare un'app partizionata](elastic-scale-get-started.md).
- [Eseguire query sui dati partizionati orizzontalmente](elastic-query-getting-started.md).
- Eseguire [query su più partizioni](elastic-scale-multishard-querying.md).
- [Spostare i dati partizionati](elastic-scale-configure-deploy-split-and-merge.md).
- [Configurare la sicurezza](elastic-scale-split-merge-security-configuration.md) nelle partizioni di database.
- [Aggiungere una partizione](elastic-scale-add-a-shard.md) al set corrente di partizioni di database.
- [Risolvere i problemi della mappa](elastic-database-recovery-manager.md)partizioni.
- [Eseguire la migrazione del database partizionato](elastic-convert-to-use-elastic-tools.md).
- [Crea contatori](elastic-database-perf-counters.md).
- [Usare Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) per eseguire query sui dati partizionati.
- [Usare Dapper Framework](elastic-scale-working-with-dapper.md) per eseguire query sui dati partizionati.

## <a name="develop-applications"></a>Sviluppare applicazioni

- [Connettività](connect-query-content-reference-guide.md#libraries)
- [Usare il connettore Spark](spark-connector.md)
- [Autenticare l'app](application-authentication-get-client-id-keys.md)
- [Usare l'invio in batch per ottenere prestazioni migliori](../performance-improve-use-batching.md)
- [Linee guida per la connettività](troubleshoot-common-connectivity-issues.md)
- [Alias DNS](dns-alias-overview.md)
- [Impostare PowerShell per l'alias DNS](dns-alias-powershell-create.md)
- [Porte: ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C e C++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Progettare le applicazioni

- [Progettazione per il ripristino di emergenza](designing-cloud-solutions-for-disaster-recovery.md)
- [Progettazione per i pool elastici](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Progettazione per gli aggiornamenti di app](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Progettare applicazioni SaaS (multi-tenant Software as a Service)

- [Modelli di progettazione SaaS](saas-tenancy-app-design-patterns.md)
- [Indicizzatore video SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Sicurezza delle app SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulle [guide alle procedure per istanza gestita SQL di Azure](../managed-instance/how-to-content-reference-guide.md)