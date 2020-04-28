---
title: Come configurare un singolo database
description: Informazioni su come configurare e gestire il database SQL di Azure (database singolo)
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 01/14/2020
ms.openlocfilehash: 4283e1a2c92b7fe738fb57a8103cea1deb0015a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76027712"
---
# <a name="how-to-use-a-single-database-in-azure-sql-database"></a>Come usare un database singolo in Database SQL di Azure

In questa sezione sono disponibili varie guide, script e spiegazioni che possono essere utili per gestire e configurare il database singolo in Database SQL di Azure

## <a name="migrate"></a>Migrazione

- [Eseguire la migrazione nel database SQL](sql-database-single-database-migrate.md): ottenere informazioni sul processo di migrazione consigliato e sugli strumenti per la migrazione in un'istanza gestita.
- Imparare a [gestire il database SQL dopo la migrazione](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Configurare le funzionalità

- [Configurare la replica transazionale](replication-to-sql-database.md) per replicare i dati tra database.
- [Configurare il rilevamento delle minacce](sql-database-threat-detection.md) per consentire al database SQL di Azure di identificare le attività sospette, ad esempio attacchi SQL injection o l'accesso da posizioni sospette.
- [Configurare la maschera dati dinamica](sql-database-dynamic-data-masking-get-started-portal.md) per proteggere i dati sensibili.
- [Configurare la conservazione dei backup](sql-database-long-term-backup-retention-configure.md) di un database in modo che vengano conservati nell'archivio BLOB di Azure. In alternativa, è possibile adottare l'approccio basato sulla [configurazione della conservazione dei backup con l'insieme di credenziali Azure (deprecato)](sql-database-long-term-backup-retention-configure-vault.md).
- [Configurare la replica geografica](sql-database-geo-replication-portal.md) per mantenere una replica del database in un'altra area.
- [Configurare la sicurezza per le repliche geografiche](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorare e ottimizzare il database

- [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md) per consentire al database SQL di Azure di ottimizzare le prestazioni del carico di lavoro.
- [Abilitare le notifiche di posta elettronica per l'ottimizzazione automatica](sql-database-automatic-tuning-email-notifications.md) per ottenere informazioni sui suggerimenti per l'ottimizzazione.
- [Applicare i suggerimenti per le prestazioni](sql-database-advisor-portal.md) e ottimizzare il database.
- [Creare avvisi](sql-database-insights-alerts-portal.md) per avere notifiche dal database SQL di Azure.
- [Risolvere i problemi di connettività](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) eventualmente rilevati tra le applicazioni e il database. È anche possibile usare [Integrità risorse per i problemi di connettività](sql-database-resource-health.md).
- [Gestire lo spazio file](sql-database-file-space-management.md) per monitorare l'utilizzo dello spazio di archiviazione nel database.

## <a name="query-distributed-data"></a>Eseguire query sui dati distribuiti

- [Eseguire query sui dati partizionati verticalmente](sql-database-elastic-query-getting-started-vertical.md) su più database.
- [Report nel livello dati con scalabilità orizzontale](sql-database-elastic-query-horizontal-partitioning.md).
- [Eseguire query tra tabelle con schemi diversi](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Processi di database elastici

- [Creazione e gestione](elastic-jobs-powershell.md) Processi di database elastici con PowerShell.
- [Creare e gestire](elastic-jobs-tsql.md) i processi di database elastici tramite Transact-SQL.
- [Eseguire la migrazione da un processo elastico precedente](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Partizionamento orizzontale del database

- [Aggiornare la libreria client del database elastico](sql-database-elastic-scale-upgrade-client-library.md).
- [Creare un'app partizionata](sql-database-elastic-scale-get-started.md).
- [Eseguire query sui dati partizionati orizzontalmente](sql-database-elastic-query-getting-started.md).
- Eseguire [query su più partizioni](sql-database-elastic-scale-multishard-querying.md).
- [Spostare i dati partizionati](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Configurare la sicurezza](sql-database-elastic-scale-split-merge-security-configuration.md) nelle partizioni di database.
- [Aggiungere una partizione](sql-database-elastic-scale-add-a-shard.md) al set corrente di partizioni di database.
- [Risolvere i problemi della mappa](sql-database-elastic-database-recovery-manager.md)partizioni.
- [Eseguire la migrazione del database partizionato](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Crea contatori](sql-database-elastic-database-perf-counters.md).
- [Usare Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) per eseguire query sui dati partizionati.
- [Usare Dapper Framework](sql-database-elastic-scale-working-with-dapper.md) per eseguire query sui dati partizionati.

## <a name="next-steps"></a>Passaggi successivi
- Ottenere altre informazioni sulle [Guide pratiche per l'istanza gestita](sql-database-howto-managed-instance.md)
