---
title: Configurare & gestire il riferimento al contenuto
titleSuffix: Azure SQL Managed Instance
description: Guida di riferimento del contenuto che illustra come configurare e gestire Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779765"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Informazioni di riferimento sul contenuto del Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In questo articolo è possibile trovare un riferimento al contenuto per diverse guide, script e spiegazioni che consentono di gestire e configurare Istanza gestita SQL di Azure.

## <a name="load-data"></a>Caricare dati

- [Eseguire la migrazione ad Azure sql istanza gestita](migrate-to-instance-from-sql-server.md): informazioni sul processo e sugli strumenti di migrazione consigliati per la migrazione ad azure SQL istanza gestita.
- Eseguire la migrazione del certificato Transparent Data Encryption [ad Azure SQL istanza gestita](tde-certificate-migrate.md): se il database SQL Server è protetto con Transparent Data Encryption (Transparent Data Encryption), è necessario eseguire la migrazione del certificato che SQL istanza gestita può usare per decrittografare il backup che si vuole ripristinare in Azure.
- [Importare un database da un file BACPAC](../database/database-import.md)
- [Esportare un database in un file BACPAC](../database/database-export.md)
- [Caricare dati con BCP](../load-from-csv-with-bcp.md)
- [Caricare i dati con Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Configurazione di rete

- [Determinare le dimensioni della subnet](vnet-subnet-determine-size.md): poiché non è possibile ridimensionare la subnet dopo la distribuzione di SQL istanza gestita, è necessario calcolare l'intervallo IP degli indirizzi necessari per il numero e i tipi di istanze gestite che si prevede di distribuire nella subnet. 
- [Creare una nuova VNet e una nuova subnet](virtual-network-subnet-create-arm-template.md): configurare la rete virtuale e la subnet in base ai [requisiti di rete](connectivity-architecture-overview.md#network-requirements). 
- [Configurare una VNet e una subnet esistenti](vnet-existing-add-subnet.md): verificare i requisiti di rete e configurare la rete virtuale e la subnet esistenti per la distribuzione di SQL istanza gestita. 
- [Configurare DNS personalizzato](custom-dns-configure.md): configurare un DNS personalizzato per concedere l'accesso alle risorse esterne ai domini personalizzati da SQL istanza gestita tramite un server collegato di profili di posta elettronica database. 
- [Sincronizzare la configurazione di rete](azure-app-sync-network-configuration.md): aggiornare il piano di configurazione della rete se non è possibile stabilire una connessione dopo aver [integrato l'app con una rete virtuale di Azure](../../app-service/web-sites-integrate-with-vnet.md).
- [Trovare l'indirizzo IP dell'endpoint di gestione](management-endpoint-find-ip-address.md): determinare l'endpoint pubblico usato da SQL istanza gestita per scopi di gestione. 
- [Verificare la protezione integrata del firewall](management-endpoint-verify-built-in-firewall.md): verificare che SQL istanza gestita consenta il traffico solo sulle porte necessarie e altre regole del firewall predefinite. 
- [Connettere](connect-application-instance.md)le applicazioni: informazioni sui diversi modelli per la connessione delle applicazioni a SQL istanza gestita.

## <a name="feature-configuration"></a>Configurazione delle funzionalità

- [Configurare l'autorizzazione di Azure AD](../database/authentication-aad-configure.md)
- [Configurare l'accesso condizionale](../database/conditional-access-configure.md)
- [Autenticazione di Azure AD a più fattori](../database/authentication-mfa-ssms-overview.md)
- [Configurare l'autenticazione a più fattori](../database/authentication-mfa-ssms-configure.md)
- [Configurare un criterio di conservazione temporale](../database/temporal-tables-retention-policy.md)
- [Configurare TDE con BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Eseguire la rotazione delle chiavi TDE BYOK](../database/transparent-data-encryption-byok-key-rotation.md)
- [Rimuovere una protezione Transparent Data Encryption](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Configurare OLTP in memoria](../in-memory-oltp-configure.md)
- [Configurare Automazione di Azure](../database/automation-manage.md)
- La [replica transazionale](replication-between-two-instances-configure-tutorial.md) consente di replicare i dati tra istanze gestite o da SQL Server da locale a SQL istanza gestita e viceversa.
- [Configurare il rilevamento](threat-detection-configure.md) delle minacce: il [rilevamento delle minacce](../database/threat-detection-overview.md) è una funzionalità predefinita di istanza gestita SQL di Azure che rileva diversi potenziali attacchi, ad esempio attacchi SQL Injection o accesso da posizioni sospette. 
- La [creazione di avvisi](alerts-create.md) consente di configurare avvisi sulle metriche monitorate, ad esempio l'utilizzo della CPU, l'utilizzo dello spazio di archiviazione, IOPS e altre per SQL istanza gestita. 

## <a name="monitoring-and-tuning"></a>Monitoraggio e ottimizzazione

- [Ottimizzazione manuale](../database/performance-guidance.md)
- [Usare le viste a gestione dinamica per monitorare le prestazioni](../database/monitoring-with-dmvs.md)
- [Usare Query Store per monitorare le prestazioni](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Risolvere i problemi delle prestazioni con Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Usare il log di diagnostica Intelligent Insights](../database/intelligent-insights-use-diagnostics-log.md)
- [Monitorare In-Memory spazio OLTP](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Eventi estesi

- [Eventi estesi](../database/xevent-db-diff-from-svr.md)
- [Archiviare eventi estesi in un file di eventi](../database/xevent-code-event-file.md)
- [Archiviare gli eventi estesi in un buffer circolare](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Creazione di avvisi

- [Creare avvisi per l'istanza gestita](alerts-create.md)

## <a name="operations"></a>Operazioni

- [Failover manuale avviato dall'utente in SQL Istanza gestita](user-initiated-failover.md)

## <a name="develop-applications"></a>Sviluppare applicazioni

- [Connettività](../database/connect-query-content-reference-guide.md#libraries)
- [Usare il connettore Spark](../../cosmos-db/spark-connector.md)
- [Autenticare un'app](../database/application-authentication-get-client-id-keys.md)
- [Usare l'invio in batch per ottenere prestazioni migliori](../performance-improve-use-batching.md)
- [Linee guida per la connettività](../database/troubleshoot-common-connectivity-issues.md)
- [Alias DNS](../database/dns-alias-overview.md)
- [Configurare un alias DNS usando PowerShell](../database/dns-alias-powershell-create.md)
- [Porte: ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C e C++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Progettare le applicazioni

- [Progettazione per il ripristino di emergenza](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Progettazione per i pool elastici](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Progettazione per gli aggiornamenti di app](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Progettazione di applicazioni SaaS multi-tenant

- [Modelli di progettazione SaaS](../database/saas-tenancy-app-design-patterns.md)
- [Indicizzatore video SaaS](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Sicurezza delle app SaaS](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, [distribuire SQL istanza gestita](instance-create-quickstart.md).