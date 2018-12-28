---
title: Come configurare il database SQL di Azure | Microsoft Docs
description: Informazioni su come configurare e gestire il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439538"
---
# <a name="how-to-use-azure-sql-database"></a>Come usare il database SQL di Azure

In questa sezione sono disponibili varie guide, script e spiegazioni che possono essere utili per gestire e configurare il database SQL di Azure. È anche possibile trovare Guide alle procedure specifiche per [Database singolo](sql-database-howto-single-database.md) e [Istanza gestita](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Caricare i dati

- [Copiare un database singolo all'interno di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [Importare un database da un file BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [Esportare un database in un file BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [Caricare i dati con BCP](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [Caricare i dati con ADF](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Sincronizzazione dei dati

- [Sincronizzazione dati SQL](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [Agente di sincronizzazione dati](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [Replicare le modifiche dello schema](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [Monitorare con OMS](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [Procedure consigliate per la sincronizzazione dei dati](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [Risolvere i problemi di sincronizzazione dei dati](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>Monitoraggio e ottimizzazione

-  [Ottimizzazione manuale](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Usare le DMV per monitorare le prestazioni](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Usare Query Store per monitorare le prestazioni](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [Risolvere i problemi delle prestazioni con Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [Usare il log di diagnostica di Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [Monitorare lo spazio OLTP in memoria](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>Eventi estesi

- [Eventi estesi](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [Archiviare gli eventi estesi nel file di eventi](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [Archiviare gli eventi estesi nel buffer circolare](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>Configurare le funzionalità

- [Configurare l'autorizzazione di Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [Configurare l'accesso condizionale](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [Multi-Factor Authentication di AAD](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Configurare multi-factor auth](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [Configurare i criteri di conservazione temporale](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [Configurare TDE con BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [Eseguire la rotazione delle chiavi TDE BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [Rimuovere la protezione TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [Configurare OLTP in memoria](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [Configurare Automazione di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>Sviluppare applicazioni

- [Connettività](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [Usare il connettore Spark](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [Autenticare l'app ](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [Messaggi di errore](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [Usare l'invio in batch per ottenere prestazioni migliori](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [Linee guida per la connettività](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [Alias DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [Impostare PowerShell per l'alias DNS](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [Porte - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C e C++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>Progettare le applicazioni

- [Progettazione per il ripristino di emergenza](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [Progettazione per i pool elastici](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [Progettazione per gli aggiornamenti di app](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>Progettazione di applicazioni SaaS multi-tenant

- [Modelli di progettazione SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [Indicizzatore video SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [Sicurezza delle app SaaS](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>Passaggi successivi
- Ottenere informazioni sulle [guide pratiche nell'istanza gestita](sql-database-howto-managed-instance.md).
- Ottenere informazioni sulle [guide pratiche nel Database singolo](sql-database-howto-single-database.md).
