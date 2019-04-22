---
title: Come configurare il database SQL di Azure | Microsoft Docs
description: Informazioni su come configurare e gestire il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7db9c6400ac7d235153a59965e34e30d9b809a81
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359701"
---
# <a name="how-to-use-azure-sql-database"></a>Come usare il database SQL di Azure

In questa sezione sono disponibili varie guide, script e spiegazioni che possono essere utili per gestire e configurare il database SQL di Azure. È anche possibile trovare guide pratiche specifiche per [database singolo](sql-database-howto-single-database.md) e [Istanza gestita](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Caricare i dati

- [Copiare un database singolo o un database in pool all'interno di Azure](sql-database-copy.md)
- [Importare un database da un file BACPAC](sql-database-import.md)
- [Esportare un database in un file BACPAC](sql-database-export.md)
- [Caricare i dati con BCP](sql-database-load-from-csv-with-bcp.md)
- [Caricare i dati con ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Sincronizzazione dei dati

- [Sincronizzazione dati SQL](sql-database-sync-data.md)
- [Agente di sincronizzazione dati](sql-database-data-sync-agent.md)
- [Replicare le modifiche dello schema](sql-database-update-sync-schema.md)
- [Monitorare con OMS](sql-database-sync-monitor-oms.md)
- [Procedure consigliate per la sincronizzazione dei dati](sql-database-best-practices-data-sync.md)
- [Risolvere i problemi di sincronizzazione dei dati](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitoraggio e ottimizzazione

- [Ottimizzazione manuale](sql-database-performance-guidance.md)
- [Usare le DMV per monitorare le prestazioni](sql-database-monitoring-with-dmvs.md)
- [Usare Query Store per monitorare le prestazioni](sql-database-operate-query-store.md)
- [Risolvere i problemi delle prestazioni con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Usare il log di diagnostica di Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorare lo spazio OLTP in memoria](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Eventi estesi

- [Eventi estesi](sql-database-xevent-db-diff-from-svr.md)
- [Archiviare gli eventi estesi nel file di eventi](sql-database-xevent-code-event-file.md)
- [Archiviare gli eventi estesi nel buffer circolare](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Configurare le funzionalità

- [Configurare l'autorizzazione di Azure AD](sql-database-aad-authentication-configure.md)
- [Configurare l'accesso condizionale](sql-database-conditional-access.md)
- [Multi-Factor Authentication di AAD](sql-database-ssms-mfa-authentication.md)
- [Configurare multi-factor auth](sql-database-ssms-mfa-authentication-configure.md)
- [Configurare i criteri di conservazione temporale](sql-database-temporal-tables-retention-policy.md)
- [Configurare TDE con BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Eseguire la rotazione delle chiavi TDE BYOK](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Rimuovere la protezione TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Configurare OLTP in memoria](sql-database-in-memory-oltp-migration.md)
- [Configurare Automazione di Azure](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Sviluppare applicazioni

- [Connettività](sql-database-libraries.md)
- [Usare il connettore Spark](sql-database-spark-connector.md)
- [Autenticare l'app ](sql-database-client-id-keys.md)
- [Messaggi di errore](sql-database-develop-error-messages.md)
- [Usare l'invio in batch per ottenere prestazioni migliori](sql-database-use-batching-to-improve-performance.md)
- [Linee guida per la connettività](sql-database-connectivity-issues.md)
- [Alias DNS](dns-alias-overview.md)
- [Impostare PowerShell per l'alias DNS](dns-alias-powershell.md)
- [Porte - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C e C++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Progettare le applicazioni

- [Progettazione per il ripristino di emergenza](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Progettazione per i pool elastici](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Progettazione per gli aggiornamenti di app](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Progettazione di applicazioni SaaS multi-tenant

- [Modelli di progettazione SaaS](saas-tenancy-app-design-patterns.md)
- [Indicizzatore video SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Sicurezza delle app SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Passaggi successivi

- Ottenere altre informazioni sulle [Guide pratiche per le istanze gestite](sql-database-howto-managed-instance.md).
- Ottenere altre informazioni sulle [Guide pratiche per i database singoli](sql-database-howto-single-database.md).
